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

**Prompting:** \ 
In Discussion Mode, prompting is designed to provide instruction and context necessary to facilitate seamless and coherent interactions while the Conversations workflow remains in this mode. Since prompts are tailored to each organization, a conversation **system prompt** typically includes the following elements:

- Greeting style, tone, and mood
- Organization-specific customer assistant information
- Current date and time

Within Discussion Mode, in addition to the **system prompt**, relevant data (often formatted as question and answer pairs) related to the most recent incoming message is dynamically appended to the message thread as a **message prompt**. This process provides the necessary contextual information to generate an accurate and contextually relevant response. Once a response is generated, the dynamically retrieved context is discarded, and the cycle repeats with the reception of a new message signal. It is important to note that the system prompts remain consistent throughout the conversation mode; only the context retrieved via the RAG process is discarded after each use.

**Functions:** 

In **Discussion Mode**, functions play a crucial role in determining the actions performed by the chat reply AI agent. A key decision in this mode is whether to transition to Transaction Mode. This decision is facilitated by enforced functions within the discussion mode.

**InitiateTransaction**: The `InitiateTransaction` function is responsible for transitioning the conversation workflow from Discussion Mode to Transaction Mode. It utilizes the decision-making capabilities of Large Language Models (LLMs). Given a set of supported transaction types within an organization (e.g., Booking a Room, Cancelling a Booking), the LLM uses this function to decide on the appropriate transaction to initiate. With the `InitiateTransaction` function, the LLM generates an output containing the necessary arguments required for executing the mode switch in the conversation workflow. The OpenAI Function schema is used to define these functions. The definition of the `InitiateTransaction` function is provided below.


Transaction Mode
------------------


.. _conversation_workflow: