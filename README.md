# Wazuh SOC Incident Analysis Workflow

An intelligent n8n workflow that automates Wazuh security alert processing using AI agents. This workflow analyzes security incidents, searches a knowledge base for response procedures, performs web research when needed, and generates actionable incident reports.

## 🎯 Features

- **Automated Alert Processing**: Monitors Outlook inbox for Wazuh alerts
- **AI-Powered Analysis**: Uses GPT-4o-mini to analyze security incidents
- **Knowledge Base Integration**: Searches and updates a cybersecurity playbook database
- **Intelligent Web Research**: Automatically searches Microsoft and security documentation when KB entries are missing
- **Dynamic KB Creation**: Generates new knowledge base entries from web research
- **Email Notifications**: Sends formatted incident reports with actionable recommendations

## 📋 Prerequisites

- n8n instance (self-hosted or cloud)
- Microsoft Outlook account
- OpenAI API account
- Brave Search API account
- n8n Data Table for Knowledge Base storage

## 🔧 Configuration Steps

### 1. Credentials Setup

You'll need to configure the following credentials in n8n:

#### Microsoft Outlook OAuth2
- **Nodes using this**: `Microsoft Outlook Trigger`, `Send a message`
- **Setup**: Create OAuth2 credentials in n8n for Microsoft Outlook
- **Replace in workflow**: `YOUR_OUTLOOK_CREDENTIAL_ID`

#### OpenAI API
- **Nodes using this**: `LLM – GPT-4o-mini`, `OpenAI Chat Model1`
- **Setup**: Add your OpenAI API key in n8n credentials
- **Replace in workflow**: `YOUR_OPENAI_API_CREDENTIAL_ID`

#### Brave Search API
- **Node using this**: `Brave Search`
- **Setup**: Get API key from [Brave Search API](https://brave.com/search/api/)
- **Replace in workflow**: `YOUR_BRAVE_SEARCH_API_CREDENTIAL_ID`

### 2. Microsoft Outlook Configuration

#### Get Your Folder ID
1. In n8n, create a test workflow with Microsoft Outlook Trigger
2. Run it once to see available folders
3. Copy the folder ID you want to monitor
4. Replace `YOUR_OUTLOOK_FOLDER_ID_HERE` in the workflow

#### Set Your Email Address
Replace `YOUR_EMAIL@example.com` with your actual email address in:
- **Microsoft Outlook Trigger** (sender filter)
- **Send a message** (recipient)

### 3. Data Table Setup

Create an n8n Data Table with the following columns:

| Column Name | Type | Description |
|------------|------|-------------|
| kb_id | String | Unique identifier |
| title | String | Playbook title |
| category | String | Incident category |
| platform | String | Platform (e.g., Windows, Linux) |
| attack_type | String | Type of attack |
| tags | String | Keywords for searching |
| severity_min | String | Minimum severity level |
| triggers | String | What triggers this playbook |
| summary | String | Brief summary |
| steps | String | Response steps |
| what_to_check | String | Investigation checklist |
| escalate_if | String | Escalation criteria |
| references | String | Reference URLs |
| event_id | String | Related event IDs |
| rule_id | String | Related rule IDs |
| severity | String | Severity level |
| confidence | String | Confidence level |
| response_actions | String | Immediate actions |
| source | String | Source of entry |
| created_at | String | Timestamp |

**Replace in workflow**:
- `YOUR_DATATABLE_ID_HERE` (appears twice - in KB_Search_Cybersecurity and Update KB nodes)
- `YOUR_PROJECT_ID` in cachedResultUrl fields

## 🔄 Workflow Overview

```
1. Microsoft Outlook Trigger
   ↓
2. Edit Fields (Extract email body)
   ↓
3. Normalize Wazuh Alert (Parse alert data)
   ↓
4. SOC AI Agent (Analyze with KB search)
   ↓
5. Code in JavaScript2 (Parse agent output)
   ↓
6. If (Check if KB match found)
   ├─ Match Found → Email Writer AI Agent
   └─ No Match:
      ↓
      Code in JavaScript1 (Prepare search query)
      ↓
      Brave Search (Research online)
      ↓
      AI Agent ("KB Writer") (Create KB entry)
      ↓
      Code in JavaScript3 (Format KB entry)
      ↓
      Update KB (Save to database)
      ↓
      Email Writer AI Agent
   ↓
7. Send a message (Email notification)
```

## 🚀 Usage

### Initial Setup
1. Import the workflow JSON into n8n
2. Configure all credentials
3. Update email addresses and folder IDs
4. Create and link your Data Table
5. Activate the workflow

### Running the Workflow
The workflow triggers automatically when:
- A Wazuh alert email arrives in the monitored Outlook folder
- The email is from the specified sender

### What Happens

#### Scenario 1: KB Match Found
1. Alert is normalized and analyzed
2. AI searches the Knowledge Base
3. Matching playbook is found
4. Email notification sent with KB-based response

#### Scenario 2: No KB Match
1. Alert is normalized and analyzed
2. AI searches the Knowledge Base (no match)
3. Brave Search performs web research
4. AI creates new KB entry from research
5. KB entry saved to database
6. Email notification sent with new guidance

## 📧 Email Notification Format

The workflow sends structured emails containing:

- **Incident Summary**: 2-3 line overview
- **Recommended Immediate Actions**: Bullet-pointed response steps
- **What to Check Next**: Investigation checklist
- **Escalation Criteria**: When to escalate
- **References**: Relevant documentation URLs

## 🛠️ Customization

### Adjust Search Scope
Modify the Brave Search query in the node parameters to search different documentation sources:
```javascript
"(site:learn.microsoft.com OR site:ultimatewindowssecurity.com) " + $json.brave_query
```

### Modify AI Prompts
Each AI Agent node has customizable prompts. Key nodes to adjust:
- **SOC AI Agent**: Main incident analysis logic
- **AI Agent ("KB Writer")**: KB entry generation
- **Email Writer AI Agent**: Email formatting

### Change Polling Frequency
In `Microsoft Outlook Trigger`, adjust `pollTimes` from `everyMinute` to your preferred interval.

## 🔐 Security Considerations

- Store all API keys securely in n8n credentials
- Limit Outlook folder access to Wazuh alerts only
- Review generated KB entries before deploying to production
- Consider email encryption for sensitive incident data
- Implement proper access controls on your n8n instance

## 📊 Monitoring

Monitor workflow execution through:
- n8n execution logs
- Email delivery confirmations
- Data Table entry counts
- OpenAI API usage dashboard

## 🐛 Troubleshooting

### Workflow Not Triggering
- Verify Outlook credentials are valid
- Check folder ID is correct
- Confirm sender email matches filter

### AI Agent Errors
- Check OpenAI API key and quota
- Verify prompt syntax in agent nodes
- Review execution logs for JSON parsing errors

### KB Search Not Working
- Confirm Data Table exists and is accessible
- Verify column names match schema
- Check Data Table permissions

### Email Not Sending
- Verify Outlook send credentials
- Check recipient email address
- Review email content for formatting issues

## 📝 License

This workflow is provided as-is for educational and operational purposes.

## 🤝 Contributing

Contributions welcome! Areas for improvement:
- Additional platform support (Linux, MacOS)
- More sophisticated KB matching algorithms
- Integration with SOAR platforms
- Custom severity scoring models

## 📞 Support

For issues or questions:
1. Check n8n execution logs
2. Review node configurations
3. Verify all credentials are properly set
4. Test individual nodes in isolation

## 🔄 Version History

- **v1.0**: Initial release with Wazuh alert processing, KB management, and automated research
