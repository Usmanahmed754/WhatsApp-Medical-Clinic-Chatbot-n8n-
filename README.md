WhatsApp Medical Clinic Chatbot (n8n)
An automated WhatsApp chatbot for ABC Medical Center, built with n8n, the WhatsApp Business Cloud API, Google Sheets, and an LLM (Google Gemini, with Groq as fallback) for natural-language responses.
<br>
Overview
This workflow lets patients interact with the clinic via WhatsApp to:

View clinic information (timings, location, appointment booking instructions)
See available doctors
Browse offered services
Ask FAQs
Ask free-form questions, answered conversationally by an AI agent

Replies are sent as interactive WhatsApp list messages, with content pulled live from a Google Sheet and formatted into natural language by an LLM.
<br>
How It Works
<br>
1. Webhook
Receives incoming WhatsApp messages/button clicks from Meta's Cloud API. During local development, ngrok is used to expose this webhook on a public URL so Meta can actually reach it.
<br>
2. Dedupe Check / Skip If Duplicate
Prevents duplicate processing when WhatsApp resends the same webhook event.
<br>
3. If
Filters out non-message events (e.g. delivery/read receipts).
<br>
4. Switch (Rules)
Routes the request based on intent:

Clinic_Info — clinic details
Show_FAQ_Menu — triggered by typing "hi"
Doctors — doctor list
Services — services list
FAQ — FAQ button
Fallback — anything unmatched (free-text questions), sent straight to the AI agent

<br>
5. Edit Fields (5 / 3 / 7 / 6 / plain)
Sets which Google Sheet category to query for each intent, or prepares the raw message for the Fallback branch.
<br>
6. Get row(s) in sheet
Fetches the relevant data from Google Sheets (skipped for Fallback).
<br>
7. Aggregate
Combines multiple rows into a single dataset (skipped for Fallback).
<br>
8. AI Agent
Converts the sheet data (or, for Fallback, the user's free-text question) into a natural, formatted WhatsApp reply.

Primary model: Google Gemini
Fallback model: Groq (used if Gemini fails or is rate-limited)
Includes chat memory for conversational context

<br>
9. Edit Fields2
Builds the final WhatsApp Cloud API JSON payload (interactive list message format).
<br>
10. HTTP Request
Sends the formatted message back to the user via Meta's Graph API.
<br>
Tech Stack

n8n — workflow orchestration
WhatsApp Business Cloud API (Meta Graph API) — messaging channel
ngrok — public tunnel for local webhook testing
Google Sheets — content/data source (doctors, services, clinic info, FAQs)
Google Gemini — primary LLM
Groq — fallback LLM

<br>
Setup
<br>
1. Import the workflow JSON into your n8n instance.
<br>
2. Install and run ngrok to expose your local n8n webhook:
ngrok http <your-n8n-port>
Then register the generated HTTPS URL as your WhatsApp webhook endpoint in Meta's developer dashboard.
<br>
3. Connect your WhatsApp Business Cloud API credentials to the Webhook and HTTP Request nodes.
<br>
4. Connect your Google Sheets credentials to "Get row(s) in sheet."
<br>
5. Add your Google Gemini and Groq API credentials to the AI Agent node.
<br>
6. Update the Google Sheet with your clinic's doctors, services, clinic info, and FAQ content.
<br>
7. Activate the workflow.
<br>
