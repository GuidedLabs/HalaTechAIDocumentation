*****************
Conversation Workflows
*****************

This page is intended to serve as the main reference point to how AI-enabled Conversations are
executed in the Halatech system.

Overview
========
Conversations workflow is designed to provide a durable, reliable, and dynamic environment for executing and orchestrating the complexity of AI-enabled human-to-machine conversations. With the combination of technologies such as RAG (Retrieval-Augmented Generation), LLM (Large Language Model) intelligence, and the Twilio Communications Suite, the Conversations workflow provides a highly flexible and adaptable environment to properly orchestrate complex human-to-machine communications.

Key Components
==============

Operation Modes
===============
The Conversations workflow is architected with three distinct operational modes, each designed to maintain a clear separation of concerns. These modes are as follows:

- **Relay Mode**
- **Discussion Mode**
- **Transaction Mode**

Discussion Mode
---------------
In **Discussion Mode**, the primary objective of the Conversations workflow is to execute Retrieval-Augmented Generation (RAG) operations to produce the most relevant and natural response to an incoming message. To achieve this, each organization utilizing the Conversations workflow maintains a dedicated datastore containing comprehensive information pertinent to their operations. This includes frequently asked questions, details about products and services, and general business information deemed relevant to customers. The integrity and relevance of the QA mode responses are directly dependent on the quality and completeness of this datastore.

Upon receiving a new message event from a customer, the Conversations workflow initiates a RAG operation utilizing OpenAPI vector embeddings and the Halatech Vector Postgres Database. The operation retrieves specific pieces of information most relevant to the incoming message. This retrieved content is then used as contextual input for a Large Language Model (LLM), which generates a response that is both relevant and natural. The Conversations workflow also ensures a stateful environment, maintaining a record of all messages within a given conversation thread to support ongoing dialogue.

*Prompting*

Prompting is designed to provide instruction and context necessary to facilitate seamless and coherent interactions while the conversations workflow is in Discussion Mode. Since prompts are tailored to each organization, a conversation's **system prompt** typically includes the following elements:

- Greeting style, tone, and mood
- Organization-specific customer assistant information
- Current date and time

Within discussion mode, in addition to the **system prompt**, relevant data (often formatted as question and answer pairs) related to the most recent incoming message is dynamically appended to the message thread as a **message prompt**. This process provides the necessary contextual information to generate an accurate and contextually relevant response. Once a response is generated, the dynamically retrieved context is discarded, and the cycle repeats with the reception of a new message signal. It is important to note that the system prompts remain unchanged throughout the conversation mode; only the context retrieved via the RAG process is discarded after each use.

.. note::

   Dialogue occurring in an instance of the conversation workflow is stored as a thread of all inbound and outbound messages in order w.r.t time. The **system prompt** is located at the start of the conversation (i.e. the first message) and the **message prompt** maintains its location at the bottom of the message thread as the conversation evolves.

*Functions*

In discussion mode, functions play a crucial role at enabling the AI agent  execute specific task. Concretely, in decision mode a major task to be executed is **Mode Switching** which is enabled by the ``initiateTransaction`` function. 

.. note::
    **Mode Switching** involves altering the mode of the Conversation Workflow from one mode to another. Several stateful variables and managed to preserve the mode as the workflow continues  to run.

The ``initiateTransaction`` function is responsible for transitioning the conversation workflow from **Discussion Mode** to **Transaction Mode**. It utilizes the decision-making capabilities of Large Language Models (LLMs). Given a set of supported transaction types within an organization (e.g., Booking a Room, Cancelling a Booking), the LLM uses this function to decide on the appropriate transaction to initiate. With ``initiateTransaction``, the LLM generates an output containing the necessary arguments required for executing the mode switch within the conversation workflow. The `openAI function schema <https://platform.openai.com/docs/guides/function-calling>`_ is used to define these functions and is provided as follows.

.. code-block:: json

   {
     "name": "initiateTransaction",
     "description": "
       Starts the following transactions ONLY WHEN ABSOLUTELY NECESSARY.

       1. Transaction ID: {{id_1}}; Transaction Description: {{desc_1}}
       2. Transaction ID: {{id_2}}; Transaction Description: {{desc_2}}
     ",
     "parameters": {
       "type": "object",
       "properties": {
         "transactionID": {
           "type": "string",
           "description": "The transaction ID for the intended transaction"
         },
         "userMessage": {
           "type": "string",
           "description": "The incoming user message."
         }
       },
       "required": ["transactionID", "userMessage"]
     }
   }

.. note::
    The data containing all transactions assigned to an organization is always fetched and stored in the workflow instance during initiation. This transaction information is then used to populate the list of transactions in ``initiateTransaction``.

Functions in decision mode are **enforced**. For example, an hospitality business can have a enforcer decision map that determines when a `food order` or `room booking` transaction should be be kicked off by the ``initiateTransaction`` function while in discussion mode. This decision map can have the following conditions (Read more on `Decision maps <https://platform.openai>`_): 

.. code-block:: json

   {
     "question": "Has the customer clearly indicated that they would like to start the process of booking a room reservation?",
     "answer": {
       "no": {
         "question": "Has the customer clearly indicated that they would like to start the process of ordering food or drinks?",
         "answer": {
           "no": {
             "functionName": "doNothing"
           },
           "yes": {
             "functionName": "initiateTransaction"
           }
         },
       },
       "yes": {
         "functionName": "initiateTransaction"
       }
     },
   }

Transaction Mode
------------------


.. _conversation_workflow: