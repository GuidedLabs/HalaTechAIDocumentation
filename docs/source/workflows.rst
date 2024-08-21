*********
Workflows
*********


Halatech Workflows are Durable Execution workflows that run the core processes for the services we deliver.  We have chosen a durable execution environment over a typical web server for the following reasons:

Reasons for Choosing a Durable Execution Environment


- **State Management and Persistence**: Durable servers automatically handle the persistence of the state of workflows, ensuring that even if a process (such as a conversation) is interrupted (e.g., due to server failure or network issues), it can resume from where it left off without losing any data. 

  - **Traditional Web Servers**: Typically require developers to implement custom mechanisms to save and restore state across multiple requests, which can be error-prone and complex.

- **Complex Workflow Orchestration**: Natural human-to-machine communication is a highly complex task. Durable execution environments provide native support for orchestrating complex workflows involving multiple tasks, dependencies, retries, and compensation actions. This allows us to define these workflows declaratively in code, making them easier to understand, maintain, and debug.

  - **Traditional Web Servers**: Often require complex and custom-built solutions for workflow orchestration, which can be hard to maintain and scale.

Other benefits include scalability, consistency guarantees, and observability and debugging. For these reasons, our Conversations workflow offers the best architecture needed to handle the most complex conversational intelligence as efficiently as possible.

.. toctree::

    conversation_workflow
