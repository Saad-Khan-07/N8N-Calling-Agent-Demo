Automated Calling Agent with n8n 📞
This repository contains an n8n workflow setup designed to demonstrate a basic automated calling agent. This framework provides a starting point for building more sophisticated and dynamic calling solutions.

Overview
The solution consists of two primary n8n workflows:

Call Initiating Workflow (Trigger Workflow): This workflow is responsible for initiating calls to employees.

Calling Agent Workflows (Calling Workflows): This set of workflows handles the interactive call flow, question asking, and transcription processing.

Important Note: This demo is primarily for understanding and demonstrating the core concepts of an automated calling agent. It uses a hardcoded approach for questions and can be significantly expanded for a more dynamic and efficient agent.

Workflow Setup
To get started, you will need to import these workflows into your n8n instance.

1. Call Initiating Workflow
This workflow triggers the call by accessing employee details from a Google Sheet and then sending an HTTP request to Twilio to initiate the call.

Workflow Diagram:

![image](https://github.com/user-attachments/assets/4ca4198e-40e8-4c6b-b542-a0c7c8c0dc1e)


Description:

When Triggered: The workflow begins when manually executed or triggered by a schedule.

Access Sheet: It connects to a Google Sheet to retrieve employee phone numbers and other relevant details.

HTTP Request to Twilio: An HTTP request is sent to Twilio, including the employee's phone number and the URL of the "Call Question Workflow" as a URL parameter. This URL informs Twilio where to send the call's TwiML instructions.

2. Calling Agent Workflows
This section comprises multiple interconnected workflows that manage the actual call interaction. If you encounter issues with the combined calling workflow, you can divide its three main sections into separate workflows for easier debugging. Remember to activate all workflows before retrying. Do not use the -test workflow as it is known to cause errors. ⚠️

a. Call Question Workflow
This workflow has a webhook that listens for triggers from Twilio. It sends TwiML (Twilio Markup Language) to the user's phone to ask a question and then waits for their answer.

Workflow Diagram:

![image](https://github.com/user-attachments/assets/b8e5fa6f-0b90-481f-b213-3040d4bfbd19)


Description:

Webhook: Listens for incoming requests from Twilio, which are initiated by the "Call Initiating Workflow."

TwiML Generation: Sends TwiML instructions back to Twilio, which then plays an audio message asking a predefined question to the user.

Wait for Answer: The workflow is designed to wait for the user's spoken response.

b. Question Update Workflow (Call Continue Workflow)
This workflow contains code that helps advance the question number in the conversation. It then hits the "Call Question Workflow" to ask the next question in the sequence.

Workflow Diagram:

![image](https://github.com/user-attachments/assets/bc40f570-7692-42cd-b12b-1ea0303a2b68)


Description:

Code Node: Contains logic to increment a question counter or determine the next question based on the conversation flow.

HTTP Request to Call Question Workflow: Sends an HTTP request back to the "Call Question Workflow" to trigger the next question.

c. Conversation Summary and Saving Workflow (Transcription Workflow)
This workflow listens for transcriptions of user answers, temporarily stores them, and then uses a Large Language Model (LLM) to summarize the conversation before saving the summary to a Google Sheet.

Workflow Diagram (Overall Calling Agent Workflows):

![image](https://github.com/user-attachments/assets/2e86c258-5f0c-4aaa-ae2b-e2471e81bd7e)


Workflow Diagram (Detailed Transcription Workflow):

![image](https://github.com/user-attachments/assets/89fcc628-a779-4c9d-8a19-f895d6207925)


Description:

Webhook: Listens for transcription events from Twilio (or another transcription service).

Temporary Storage (Airtable): User answers are temporarily stored in an Airtable database.

Summary Generation (Gemini Chat/LLM Node): As the last answer arrives (determined by an else condition), all collected answers are sent to a Gemini Chat LLM node with a prompt to generate a summary of the conversation.

Store Summary (Google Sheet): The generated summary is then taken and stored in the designated Google Sheet.

Temporary Data Handling: Answers are stored temporarily in Airtable and are cleared after the workflow completes. Crucially, instead of deleting records, the workflow updates the column with a blank string. ✅ This prevents changes to the record ID, which is static in the n8n flow, ensuring consistent operation without manual record ID updates.

Potential Enhancements and Suggestions ✨
This framework is a foundational example. Here are some suggestions for expanding it into a more robust and dynamic agent:

Better Conversation Flow: Consider using services like retell.ai for more natural and fluid conversational experiences.

Dynamic Questions: Integrate with chat models (e.g., Gemini) to generate questions dynamically based on previous user responses, making the agent more interactive and intelligent.

Faster Data Storage: Explore alternative methods for storing answers (e.g., a dedicated in-memory cache or a faster database) to improve processing speed, as Airtable can sometimes introduce latency.

Feel free to use this demo and framework as a starting point to create your own smart and efficient calling agents! My goal was to provide a solid foundation for building large-scale and highly efficient automated calling solutions.
