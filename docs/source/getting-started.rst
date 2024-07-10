Getting Started 
===============

Creating an Admin Account
--------------------------
To get started with using the Halatech service, a root admin account is created via the application dashboard. Following that, email verification is required. After successful email verification, more details of the root admin are requested via an Onboarding flow. This is also where the admin’s associated Organisation is created.

.. image:: ../images/onboarding.png
   :align: center

Halatech Organisation Onboarding

Organisation Association
------------------------
After successful organisation creation, the admin user is assigned the necessary roles and user metadata. At this point, the admin user is granted access to the dashboard.

.. code-block:: json

   {
     "flex": {
       "roles": [
         "admin"
       ],
       "organization": "Belfast Hospitality"
     },
     "onboardingState": "COMPLETED",
     "id": "74143ca4b3bfcb447a9150d" // Auth0 ID
   }

Link Twilio Account
-------------------
To fully complete the organisation setup, the organisation has to be assigned a dedicated Twilio account with Twilio Flex and Twilio Conversations enabled. Currently, this is done by Halatech’s internal team. 

Provision Phone Number
----------------------
Following that, the organisation also needs to be provisioned a dedicated phone number to effectively communicate with their customers via WhatsApp, SMS, etc. Halatech’s internal team also maintains the provisioning of phone numbers to an organisation.

The last step of Twilio account setup is updating the inbound conversation’s webhook on the organisation’s Twilio account to point to Halatech’s automated conversations webhook. This provides a portal from the organisation’s Twilio account into the Halatech system.

.. code-block:: console

    >> https://halatech-ai-serverless-functions-2448.twil.io/api/automated-chat

Add FAQ Data
------------
As soon as a Twilio account is fully set up, the admin user can add more FAQ data about their organisation into the Halatech system. This can be done via the interactive prompt on the Dashboard.

.. image:: ../images/add-faq.png
   :align: center
   :width: 271px

Add Organisation FAQs 

Test AI Response
----------------
With FAQ data available, the first AI demo/test can be conducted against the provided data via the embeddable WeChat Widget or WhatsApp Messenger. This is the baseline setup for any organisation.

.. image:: ../images/test-bot.png
   :align: center
   :width: 271px

Test chatbot
