# n8n Wazuh SOC AI (KB-first triage + auto KB growth)

This project provides an n8n workflow that:
- Parses Wazuh alert emails from Outlook
- Looks up procedures in a Knowledge Base (n8n Data Table)
- If no KB match: performs restricted web search, generates a new KB entry, updates the KB
- Sends a SOC-ready incident notification email

## Features
- KB-first decisioning (reduces hallucination)
- Auto KB enrichment on unknown alerts
- SOC-focused outputs: actions, validation checks, escalation triggers
- Fully modular nodes (easy to extend)

## Requirements
- n8n (cloud or self-hosted)
- Wazuh alert emails via Outlook (or adapt to IMAP/Gmail)
- OpenAI API key (or swap to local model)
- Brave Search API key (optional, only for KB enrichment)

## Setup
See `docs/setup.md`.

## Workflow
Import: `workflows/wazuh-soc-ai.json`

## KB Schema
See `docs/kb-schema.md`.

## License
MIT (or pick another)
