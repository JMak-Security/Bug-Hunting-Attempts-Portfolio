# Bug-Hunting-Attempts-Portfolio (From JMak-Security)
**A comprehensive log of security research, vulnerability discovery processes, and lessons learned.**

This repository serves as a digital garden for my bug hunting journey. It captures the technical details of my security assessments, ranging from initial reconnaissance and exploitation attempts to full post-mortem analyses of both successful findings and "dead-end" leads.

# 🧪 Case Studies:
**1. Agentic Workflow Hijack: Indirect Prompt Injection (IPI) & Exfiltration Chain Analysis (2026-04-26):**

**Description:** An audit of agentic data-handling within Gemini’s Workspace Extension. The study analyzed the security of a multi-stage Indirect Prompt Injection where a third-party email was used to weaponize the agent's cross-tool permissions. The payload was designed to force the agent to harvest private data (email subjects) and exfiltrate it to an external listener. The attack reached the final execution stage but failed due to Non-Uniform Syntax Encapsulation: the model's output parser fractured the logic by rendering the JavaScript as plain text while encapsulating the HTML. This logic de-coupling successfully acted as a secondary security boundary, blocking the automated exfiltration trigger.

**Status:** `FAILED` (Technical Execution Blocked)
#
**2. Agentic Workflow Hijack: Indirect Prompt Injection (IPI) & Metadata Spoofing (2026-05-13):**

**Description** An audit of an AI-powered YouTube studio assistant’s vulnerability to untrusted external data. This study demonstrated a successful Indirect Prompt Injection where a malicious payload was embedded within a public YouTube comment using a spoofed technical block (`[METADATA_SYNC_BLOCK]`). The payload exploited the model's tendency to prioritize structured "system" data, forcing the assistant to retrieve an internal identifier (`Comment ID`), transform it into a hexadecimal token, and concatenate it with a Base64-decoded prefix. The attack successfully tricked the AI into generating a functional exfiltration URL, proving that the model failed to distinguish between descriptive data and actionable system directives.

**Status:** `SUCCESS` (Vulnerability Confirmed & Exfiltration Validated)
# 
