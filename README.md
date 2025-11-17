# Spendly: AI-Powered Telegram Expense Tracker

![Spendly Workflow](Images/Workflow.png)

A lightweight, conversational expense-tracking system built using **n8n**, **Google Gemini**, and **Google Sheets**. Spendly enables users to log expenses naturally through **Telegram messages or voice notes**, while an AI agent automatically extracts structured financial data and persists it to cloud storage.

## Overview

Spendly transforms unstructured user input into structured expense records. For example, a natural language message such as:

> "Paid 12€ for coffee today"

is converted into structured JSON and logged to Google Sheets with full automation.

The system architecture combines:
- Natural language processing via conversational interface
- Structured data extraction and validation
- Automation-first workflow design
- LLM-based semantic understanding

## Features

### Natural Language Input
Users submit expense records via text or voice messages through Telegram, eliminating the need for dedicated applications or structured forms.

### Voice Transcription
Audio messages are downloaded from Telegram, converted to binary format, and transcribed using **Gemini 2.5 Flash** audio processing capabilities.

### Structured JSON Output
A custom system prompt ensures:
- Reliable extraction of temporal, monetary, categorical, and descriptive information
- Strict JSON schema validation via n8n Structured Output Parser
- Contextual date interpretation (relative references such as "today" and "yesterday")
- Appropriate user-facing feedback messages

### Persistent Data Storage
All processed expense records are appended to Google Sheets with the following schema:
- Date
- Amount
- Currency
- Category
- Description
- Message ID
- Chat ID

### Modular Workflow Architecture
The workflow implements conditional routing logic for:
- Content type classification (text, audio, unsupported media)
- Message processing pipeline selection
- Error handling and fallback mechanisms
- Output schema validation
- Time-based and spreadsheet-based tool invocation

## Architecture

### Workflow Pipeline

1. **Telegram Message Reception** - Incoming updates are received from the Telegram Bot API
2. **Content Routing** - A conditional switch node categorizes messages as:
   - Audio/voice messages
   - Text messages
   - Unsupported file types or images
3. **Audio Processing Pipeline**
   - Audio file download from Telegram servers
   - Transcription via Gemini 2.5 Flash
   - Transcribed text forwarding to semantic analysis
4. **Text Processing Pipeline**
   - Direct forwarding of text content to semantic analysis
5. **Semantic Analysis Agent** - Gemini-based LLM processes input using custom system prompt to generate structured JSON output
6. **Schema Validation** - n8n Structured Output Parser validates JSON conformance
7. **Conditional Processing** - If semantic relevance flag is true, proceed; otherwise send fallback response
8. **Data Transformation** - JavaScript transformation converts parsed JSON into spreadsheet-compatible format
9. **Persistent Storage** - Google Sheets API appends formatted record to designated spreadsheet
10. **User Confirmation** - Telegram sends acknowledgment message to user

---

## System Prompt Specification

The semantic analysis is governed by a custom system prompt that enforces:
- Extraction of temporal information (converted to YYYY-MM-DD format)
- Monetary amount extraction
- Currency classification (default: EUR if unspecified)
- Categorical classification
- Brief descriptive summarization
- Relative date interpretation using n8n temporal utilities
- JSON output structure with three keys:
  - `"relevant"`: Boolean indicating semantic relevance
  - `"expense_record"`: Object containing structured expense data
  - `"message"`: User-facing response text
- No supplementary text or explanations outside the JSON structure

## Technology Stack

| Component | Technology |
|-----------|-----------|
| Orchestration Engine | n8n |
| Language Model | Google Gemini |
| Audio Processing | Gemini 2.5 Flash |
| Data Persistence | Google Sheets API |
| Messaging Protocol | Telegram Bot API |
| Output Validation | n8n Structured Output Parser |
| Temporal Processing | n8n Date & Time Utilities |

---

## Repository Structure

```
Spendly-n8n/
├── Spendly.json          # n8n workflow export
├── README.md             # Project documentation
└── Images/               # Workflow diagrams and supplementary media
    └── Workflow.png      # Workflow architecture diagram
```

---

## Data Schema

| Field | Type | Description |
|-------|------|------------|
| Date | ISO 8601 | Extracted or computed date in YYYY-MM-DD format |
| Amount | Float | Numeric transaction amount |
| Currency | String | ISO 4217 currency code |
| Category | String | Semantic category classification |
| Description | String | Concise transaction description |
| Message ID | Integer | Telegram message identifier |
| Chat ID | Integer | Telegram chat/sender identifier |

---

## Example: Input-Output Behavior

### Input
```
"Bought apples for 4.5 today"
```

### Processed Output
```json
{
  "relevant": true,
  "expense_record": {
    "date": "2025-11-17",
    "amount": 4.5,
    "currency": "EUR",
    "category": "Groceries",
    "description": "Apples"
  },
  "message": "Recorded expense: 4.5 EUR for Groceries"
}
```

---

## Setup and Deployment

### Prerequisites
- n8n instance (cloud or self-hosted)
- Telegram bot token (obtain via BotFather)
- Google Cloud project with Sheets and Gemini API enabled
- OAuth2 credentials for Google Sheets access
- Google Gemini API key

### Installation Steps

1. **Clone Repository**
```bash
git clone https://github.com/BadassAman4014/Spendly-n8n.git
cd Spendly-n8n
```

2. **Import Workflow into n8n**
   - Navigate to Workflows → Import From File
   - Select `Spendly.json`

3. **Configure API Credentials**
   - Add Telegram Bot API token
   - Authenticate Google Sheets via OAuth2
   - Provide Google Gemini API key

4. **Configure Google Sheets**
   - Update spreadsheet Document ID
   - Set sheet GID (tab identifier)
   - Create header row with schema fields

5. **Activate Workflow**
   - Enable the workflow
   - Send test messages to Telegram bot for validation

---

## Planned Enhancements

- Multi-currency conversion and normalization
- Temporal analytics (weekly/monthly expense summaries)
- Automated category learning and classification refinement
- Integration with alternative data persistence layers (Notion, Airtable, SQL databases)
- Telegram mini-app for analytics visualization
- Privacy-preserving analytics capabilities

---

## Author

**Badass Aman**

Focuses on agentic AI systems, process automation, and robotics applications.

---

## License

This project is released under the MIT License. See LICENSE file for details.

---

For questions or contributions, please open an issue or pull request on the repository.
