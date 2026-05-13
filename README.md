# Bug-Hunting-Attempts-Portfolio (From JMak-Security)
**A comprehensive log of security research, vulnerability discovery processes, and lessons learned.**

This repository serves as a digital garden for my bug hunting journey. It captures the technical details of my security assessments, ranging from initial reconnaissance and exploitation attempts to full post-mortem analyses of both successful findings and "dead-end" leads.

# 🧪 Case Studies:
**1. Indirect Prompt Injection (IPI) & Exfiltration Chain Analysis (2026-04-26):**

**Description:** An audit of Gemini’s Workspace Extension security via a multi-stage Indirect Prompt Injection. The test utilized a third-party email to deliver a payload designed to harvest private data (email subject lines) and exfiltrate it to an external listener (webhooksite.net) using a Google Apps Script web app. The vulnerability failed at the final stage due to Non-Uniform Syntax Encapsulation, where the AI's output parser fractured the payload—rendering the JavaScript as plain text while encapsulating the HTML. This de-coupling of logic prevented the automated exfiltration trigger.

**Status:** `FAILED` (Technical Execution Blocked)
# 
