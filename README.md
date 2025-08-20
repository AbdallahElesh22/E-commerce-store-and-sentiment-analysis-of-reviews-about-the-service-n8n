# E-commerce Orders & Sentiment Analysis Workflow

This **n8n workflow** automates collecting e-commerce orders, sending confirmation emails, requesting reviews, performing sentiment analysis, and updating Google Sheets.

---

## Workflow Overview

The workflow automates the following steps:

1. Collect customer order details via a form.
2. Save the order to Google Sheets.
3. Create a draft email notifying the team of the new order.
4. Send an email to the customer asking for a review and wait for their response.
5. Analyze the sentiment of the customer's review.
6. Update the Google Sheet with the review and sentiment analysis results.

---

## Nodes Description

### 1. Form Trigger – `On form submission`
- **Type:** `formTrigger`
- **Purpose:** Collect order details from customers.
- **Fields:**
  - `name` (required)
  - `mobile number` (required)
  - `email` (required)
  - `address` (required)
- **Output:** JSON object containing submitted form data.

---

### 2. Google Sheets – `Append or update row in sheet`
- **Type:** `googleSheets`
- **Purpose:** Save the new order into a Google Sheet.
- **Operation:** Append or update row based on `name`.
- **Columns Mapped:** `name`, `mobile number`, `email`, `address`
- **Google Sheet URL:** Provided in `documentId`
- **Notes:** Uses OAuth2 credentials for Google Sheets.

---

### 3. Gmail Draft – `Create a draft`
- **Type:** `gmail`
- **Purpose:** Create a draft email notifying about a new order.
- **Subject:** `new order`
- **Message:** `"new order from {customer_name}"`
- **Credentials:** Gmail OAuth2

---

### 4. Gmail Send & Wait – `Send message and wait for response`
- **Type:** `gmail`
- **Purpose:** Send an email to the customer asking them to leave a review.
- **Recipient:** Customer email from form.
- **Message:** `"Confirm please. Leave a review."`
- **Waits for customer reply** (response collected as free text)

---

### 5. Sentiment Analysis – `Sentiment Analysis`
- **Type:** `@n8n/n8n-nodes-langchain.sentimentAnalysis`
- **Purpose:** Analyze the sentiment of the review received.
- **Input:** Text from customer response.

---

### 6. OpenAI Chat – `OpenAI Chat Model`
- **Type:** `@n8n/n8n-nodes-langchain.lmChatOpenAi`
- **Purpose:** Optional AI processing; can enrich or process sentiment data.
- **Model:** `gpt-4.1-mini`

---

### 7. Google Sheets – `Update row in sheet`
- **Type:** `googleSheets`
- **Purpose:** Update the same row in Google Sheets with:
  - `review` text
  - `sentimentAnalysis` result
- **Matching Column:** `name` to ensure the review goes to the correct order.

---

## Workflow Connections

- `On form submission` → `Append or update row in sheet`
- `Append or update row in sheet` → `Create a draft` AND `Send message and wait for response`
- `Send message and wait for response` → `Sentiment Analysis`
- `Sentiment Analysis` → `Update row in sheet`

---

## Key Features

- Automates e-commerce order collection and tracking.
- Sends email confirmation and review request automatically.
- Captures customer feedback and performs sentiment analysis.
- Updates Google Sheets for easy tracking of orders, reviews, and customer sentiment.
- Integrates **n8n + Gmail + Google Sheets + LangChain/OpenAI**.

---

## Setup Notes

- Ensure Google Sheets credentials are set up correctly in n8n.
- Ensure Gmail OAuth2 credentials are active and authorized.
- Ensure OpenAI API credentials are configured correctly.
