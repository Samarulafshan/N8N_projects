AI-Powered Customer Review Insights

An n8n workflow that collects product reviews through a form, uses GPT-4 to summarize them and detect sentiment/tone, logs everything to Google Sheets, and automatically emails the team when a review is negative or neutral.

What It Does-
Collects reviews via an embedded n8n form (Product Name + Product Review).
Summarizes the review into a concise 3–4 line summary using GPT-4.
Analyzes sentiment — classifies the review as Positive, Negative, or Neutral, with a confidence score (0.00–1.00).
Classifies tone of the summary — Friendly, Professional, Angry, Disappointed, Excited, or Neutral — with a confidence score.
Logs the results (product name, original review, summary, sentiment, tone) as a new row in a Google Sheet.
Alerts the team — if the sentiment is Negative or Neutral, sends an automatic email to the team lead with the review summary, sentiment, and tone attached.

Workflow Steps-
On form submission → triggers the workflow with the submitted product name and review
Summarization Node (GPT-4) → summarizes the review in 3–4 lines
Sentiment Analysis (GPT-4) → classifies sentiment and assigns a confidence score
Summary Tone Classification Node (GPT-4) → classifies the tone of the summary and assigns a confidence score
Append row in sheet → logs everything to Google Sheets
If (Negative or Neutral?) → checks the sentiment result
Send a message (Gmail) → emails the team lead if the sentiment is Negative or Neutral

Nodes Used-
On form submission (n8n-nodes-base.formTrigger) — entry point, collects product name and review text
Summarization Node (@n8n/n8n-nodes-langchain.chainLlm) — summarizes the review
OpenAI Chat Model (@n8n/n8n-nodes-langchain.lmChatOpenAi) — GPT-4 model backing the Summarization Node
Sentiment Analysis (@n8n/n8n-nodes-langchain.chainLlm) — classifies review sentiment + score
OpenAI Chat Model1 (@n8n/n8n-nodes-langchain.lmChatOpenAi) — GPT-4 model backing Sentiment Analysis
Summary Tone Classification Node (@n8n/n8n-nodes-langchain.chainLlm) — classifies tone of the summary + score
OpenAI Chat Model2 (@n8n/n8n-nodes-langchain.lmChatOpenAi) — GPT-4 model backing the Tone Classification
Append row in sheet (n8n-nodes-base.googleSheets) — logs the review + AI outputs to Google Sheets
If (n8n-nodes-base.if) — routes to the email alert if sentiment is Negative/Neutral
Send a message (n8n-nodes-base.gmail) — emails the team lead about the flagged review
Prerequisites
An n8n instance (self-hosted or cloud) with the LangChain nodes package available (@n8n/n8n-nodes-langchain)
An OpenAI API credential with access to gpt-4
A Google Sheets OAuth2 credential
A Gmail OAuth2 credential

Setup-
Import the workflow — in n8n, go to Workflows → Import from File and select customer_review_insights.json.
Connect credentials — set your OpenAI credential on all three OpenAI Chat Model nodes, your Google Sheets credential on Append row in sheet, and your Gmail credential on Send a message.
Point to your own Google Sheet — in Append row in sheet, use a sheet with these columns: Product Name, Product Review, Product Review Summary, Sentiment and Score, Tone and Score.
Update the alert email — in Send a message, replace the sendTo address with the team/lead email that should receive negative/neutral review alerts.
Activate the workflow and share the form trigger's public URL to start collecting reviews.
Example Output
Product Name: Wireless Earbuds X1
Product Review: "Battery dies after an hour, very disappointed..."
Product Review Summary: Customer reports poor battery life and overall dissatisfaction with the product.
Sentiment and Score: Sentiment: Negative | Score: 0.87
Tone and Score: Tone: Disappointed | Score: 0.79
Alert Email Trigger

An email is sent automatically whenever the AI-detected sentiment contains "negative" or "neutral", so the team can follow up quickly on reviews that aren't clearly positive.

Notes-
All three LLM calls currently use gpt-4 — swap the model in each OpenAI Chat Model node if you want to use a different model or provider.
Sentiment and tone are returned as plain-text strings in a fixed format (e.g. Sentiment: Negative | Score: 0.87) rather than structured JSON — parse or adjust the prompts if you need machine-readable fields.
The workflow is currently set to active: false — activate it in n8n after importing and configuring credentials.
