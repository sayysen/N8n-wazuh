# N8n-wazuh
8n workflow that converts Wazuh alert emails into automated SOC responses. It checks a local cybersecurity knowledge base first; if no match is found, it performs a controlled web search, generates a new playbook, updates the KB, and sends an incident notification email automatically.
