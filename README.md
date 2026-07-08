# Job-Application-Email-Sorter (n8n + Gemini)

An n8n workflow that automatically classifies incoming job-application emails and labels them in Gmail — Applied, Interview, Assessment, Rejected, Acceptance, or Other — using Google Gemini for classification.

How it works

Gmail Trigger → Extract Email Fields (Code) → Call Gemini (HTTP Request)
   → Parse Gemini Response (Code) → Switch → Gmail: Add Label (×6 branches)


Gmail Trigger polls the inbox on a schedule for new mail, excluding anything already labeled.
Extract Email Fields flattens the raw Gmail payload into subject, from, and snippet.
Call Gemini sends that text to gemini-2.5-flash with a classification prompt and asks for a JSON response (category, confidence).
Parse Gemini Response safely parses that JSON (with a fallback to Other if Gemini's output is malformed), and handles multiple emails per poll correctly.
Switch routes each email based on its category.
Six Gmail nodes apply the matching label.


Setup

1. Create Gmail labels

In Gmail, manually create these labels before importing the workflow:
Applied, Interview, Assessment, Rejected, Acceptance, Other

2. Get a Gemini API key

Create one at https://aistudio.google.com/apikey

3. Import the workflow

In n8n: Workflows → Import from File → select Job_Application_Email_sorter.json

4. Reconnect credentials

This file has credential references stripped out (they're specific to individual n8n accounts and won't work for anyone else). After importing, you'll need to:


Open the Gmail Trigger node and the 6 Gmail label nodes → attach your own Gmail OAuth2 credential.
Open the Call Gemini node → attach your own Google Gemini (PaLM) API credential (n8n's built-in credential type for Gemini).


5. Update label IDs

The 6 Gmail label nodes reference Gmail's internal labelIds (e.g. Label_7245908206703557004), which are unique to the original account. After creating your own labels, open each Gmail node's Label Names or IDs field and reselect the correct label from the dropdown.

6. Test, then activate

Use n8n's Execute Workflow button to test against real emails before toggling the workflow Active.

Notes


Uses gemini-2.5-flash — fast and inexpensive for this kind of short classification task.
The parsing step defaults unrecognized/malformed Gemini output to Other rather than failing the whole run.
Built as a learning project while getting started with n8n and LLM-based automation.
