********************************
Tenant vault (credentials)
********************************

Purpose
=======

Every Halatech tenant that can run chat needs Twilio channel credentials and a
**webchat cypher**. Those live in engineering Postgres under the **tenant vault**
tables, keyed by the tenant's Twilio Account SID (``tenants.twiliosid``).

Without a vault row for that SID:

* the dashboard chat widget stays on **Loading organization…**
* serverless / workflow code cannot authenticate to Twilio for that tenant

This page is the operator-facing summary. The accepted engineering decision is
recorded as ADR 016 in the HalaTech-CEX monorepo
(``docs/decisions/016-tenantvault-rds-two-table-model.md``).

Two-table model (RDS)
=====================

After the move from Supabase (``pgsodium``) to AWS RDS, credentials use an
explicit split:

.. list-table::
   :header-rows: 1
   :widths: 28 28 44

   * - Store
     - Column
     - Contents / readers
   * - ``tenantvault``
     - ``secret``
     - Opaque **cypher** (legacy base64 blob or random hex). Used by UMS and the
       dashboard / embedded webchat as a public-ish lookup key — **not** the Twilio
       auth token.
   * - ``tenantvault``
     - ``api_key``, ``api_secret``, ``conversation_service_id``, ``address_id``
     - Twilio API key pair, Conversations service SID, webchat address SID.
   * - ``decrypted_tenantvault``
     - ``decrypted_secret``
     - Twilio **auth token** (32 hex). Read by hala-chat-flex and Workflows
       Platform for server-side Twilio calls.

**Rule:** never put the Twilio auth token in ``tenantvault.secret``. Mixing those
columns caused Twilio ``20003 Authenticate`` failures and widgets that treated the
auth token as a cypher.

Related registry
================

``tenant_stack_registry`` (per ``tenant_id``) holds Flex / serverless domains and
provisioning status (for example ``hala_chat_serverless_domain``). Vault answers
"how do we authenticate and which Conversations address?"; the registry answers
"which Twilio serverless / Flex stack?".

Chat init needs both: vault cypher + ``twiliosid``, and a resolvable serverless
URL (from the registry, or the local dashboard ``/twilio-webchat`` proxy).

Registration checklist
======================

For a new tenant (same playbook as Tenant Zero):

1. Set ``tenants.twiliosid`` to the tenant's Twilio Account SID (unique).
2. Upsert **both** vault tables in one transaction via
   ``infrastructure/tenant-stack/lib/tenant-vault-rds.mjs``
   (``upsertTenantVaultRds``).
3. Generate a cypher for ``tenantvault.secret`` (``crypto.randomBytes(16).hex``)
   when there is no legacy cypher to preserve.
4. Write the real auth token only to ``decrypted_tenantvault.decrypted_secret``.
5. Register stack domains in ``tenant_stack_registry``.
6. Hard-refresh the dashboard after cypher changes (Redux org cache).

Verify
======

* ``tenantvault.twiliosid`` matches ``tenants.twiliosid``.
* ``decrypted_secret`` matches ``^[a-f0-9]{32}$``.
* ``tenantvault.secret`` is **distinct** from the auth token (length ≥ 40 for
  legacy cyphers, or random hex for new tenants).
* Readiness: ``node infrastructure/tenant-stack/scripts/verify-tenant-zero-readiness.mjs --tenant-id <id>``

Do not
======

* Re-introduce ``pgsodium`` triggers on RDS without the extension.
* Run legacy ``migrate-tenantvault-data.js`` blindly (it can copy ciphertext into
  ``decrypted_secret`` and break tokens).
* Share one Twilio Account SID across two ``tenants`` rows (unique constraint;
  also wrong Flex / Conversations stack).

See also
========

* HalaTech-CEX ADR 016 — Tenantvault credentials on RDS
* HalaTech-CEX ADR 014 / 015 — tenant stack isolation and runtime lookup
* ``infrastructure/tenant-stack/README.md`` — Tenant Zero registration scripts
* ``docs/tenant-onboarding-runbook.md`` — full tenant onboarding checklist
