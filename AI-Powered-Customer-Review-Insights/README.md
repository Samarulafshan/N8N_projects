AI-Powered Customer Review Insights

An n8n workflow that collects product reviews through a form, uses GPT-4 to summarize them and detect sentiment/tone, logs everything to Google Sheets, and automatically emails the team when a review is negative or neutral.

🔎 What It Does
Collects reviews via an embedded n8n form (Product Name + Product Review).
Summarizes the review into a concise 3–4 line summary using GPT-4.
Analyzes sentiment — classifies the review as Positive, Negative, or Neutral, with a confidence score (0.00–1.00).
Classifies tone of the summary — Friendly, Professional, Angry, Disappointed, Excited, or Neutral — with a confidence score.
Logs the results (product name, original review, summary, sentiment, tone) as a new row in a Google Sheet.
Alerts the team — if the sentiment is Negative or Neutral, sends an automatic email to the team lead with the review summary, sentiment, and tone attached.
🧩 Workflow Diagram
Yes
No
On form submission
Summarization Node
Sentiment Analysis
Summary ToneClassification Node
Append row in sheet
If: Negative or Neutral?
Send email to team lead
End
OpenAI Chat Model - GPT-4
OpenAI Chat Model1 -GPT-4
OpenAI Chat Model2 -GPT-4
🛠️ Nodes Used
Node	Type	Purpose
On form submission	n8n-nodes-base.formTrigger	Entry point — collects product name and review text
Summarization Node	@n8n/n8n-nodes-langchain.chainLlm	Summarizes the review (3–4 lines)
OpenAI Chat Model	@n8n/n8n-nodes-langchain.lmChatOpenAi	GPT-4 model backing the Summarization Node
Sentiment Analysis	@n8n/n8n-nodes-langchain.chainLlm	Classifies review sentiment + score
OpenAI Chat Model1	@n8n/n8n-nodes-langchain.lmChatOpenAi	GPT-4 model backing Sentiment Analysis
Summary Tone Classification Node	@n8n/n8n-nodes-langchain.chainLlm	Classifies tone of the summary + score
OpenAI Chat Model2	@n8n/n8n-nodes-langchain.lmChatOpenAi	GPT-4 model backing the Tone Classification
Append row in sheet	n8n-nodes-base.googleSheets	Logs the review + AI outputs to Google Sheets
If	n8n-nodes-base.if	Routes to email alert if sentiment is Negative/Neutral
Send a message	n8n-nodes-base.gmail	Emails the team lead about the flagged review
⚙️ Prerequisites
An n8n instance (self-hosted or cloud) with the LangChain nodes package available (@n8n/n8n-nodes-langchain)
An OpenAI API credential with access to gpt-4
A Google Sheets OAuth2 credential
A Gmail OAuth2 credential
🚀 Setup
Import the workflow In n8n, go to Workflows → Import from File and select customer_review_insights.json.
Connect credentials
Set your OpenAI credential on all three OpenAI Chat Model nodes.
Set your Google Sheets credential on Append row in sheet.
Set your Gmail credential on Send a message.
Point to your own Google Sheet In Append row in sheet, replace the spreadsheet/sheet with your own, using these columns:
Product Name
Product Review
Product Review Summary
Sentiment and Score
Tone and Score
Update the alert email In Send a message, replace the sendTo address with the team/lead email that should receive negative/neutral review alerts.
Activate the workflow and share the form trigger's public URL to start collecting reviews.
📄 Example Output Row
Product Name	Product Review	Product Review Summary	Sentiment and Score	Tone and Score
Wireless Earbuds X1	"Battery dies after an hour, very disappointed..."	Customer reports poor battery life and overall dissatisfaction with the product.	Sentiment: Negative | Score: 0.87	Tone: Disappointed | Score: 0.79
📬 Alert Email Trigger

An email is sent automatically whenever the AI-detected sentiment contains "negative" or "neutral", so the team can follow up quickly on reviews that aren't clearly positive.

📝 Notes
All three LLM calls currently use gpt-4 — swap the model in each OpenAI Chat Model node if you want to use a different model or provider.
Sentiment and tone are returned as plain-text strings in a fixed format (e.g. Sentiment: Negative | Score: 0.87) rather than structured JSON — parse or adjust the prompts if you need machine-readable fields.
The workflow is currently set to active: false — activate it in n8n after importing and configuring credentials.
