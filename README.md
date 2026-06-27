# AI-Vulnerability-Researches-Portfolio (From JMak-Security)
**A comprehensive log of security research, vulnerability discovery processes, and lessons learned.**

This repository serves as a digital garden for my bug hunting journey. It captures the technical details of my security assessments, ranging from initial reconnaissance and exploitation attempts to full post-mortem analyses of both successful findings and "dead-end" leads.

# 🧪 Case Studies:
### 1. Agentic Workflow Hijack: Indirect Prompt Injection (IPI) & Exfiltration Chain Analysis (2026-04-26):

**Description:** An audit of agentic data-handling within Gemini’s Workspace Extension. The study analyzed the security of a multi-stage Indirect Prompt Injection where a third-party email was used to weaponize the agent's cross-tool permissions. The payload was designed to force the agent to harvest private data (email subjects) and exfiltrate it to an external listener. The attack reached the final execution stage but failed due to Non-Uniform Syntax Encapsulation: the model's output parser fractured the logic by rendering the JavaScript as plain text while encapsulating the HTML. This logic de-coupling successfully acted as a secondary security boundary, blocking the automated exfiltration trigger.

**Status:** `FAILED` (Technical Execution Blocked)

---

### 2. Agentic Workflow Hijack: Indirect Prompt Injection (IPI) & Metadata Spoofing (2026-05-13):

**Description:** An audit of an AI-powered YouTube studio assistant’s vulnerability to untrusted external data. This study explored a potential Indirect Prompt Injection where a malicious payload was embedded within a public YouTube comment using a spoofed technical block (`[METADATA_SYNC_BLOCK]`). The payload exploited the model's tendency to prioritize structured "system" data, attempting to force the assistant to retrieve a metadata identifier, transform it into a hexadecimal token, and concatenate it with a Base64-decoded prefix.

The research demonstrated that the AI could be tricked into generating a functional URL. However, the study confirmed that current platform safeguards successfully mitigate the risk of **automated** exfiltration. Because the AI presents the link to the user rather than executing a background request, the platform relies on a "human-in-the-loop" defense. Furthermore, the data retrieved was identified as public metadata (Comment_ID) rather than private session data, highlighting the effectiveness of existing Context Isolation boundaries.

**Status:** `CLOSED / INTENDED BEHAVIOR` (VRP Case #512830034; Verified as Social Engineering/Safety Bypass)

---

### 3. Multi-File Semantic Logic Hijacking: Indirect Prompt Injection (IPI) & Payload Fragmentation (2026-04-22):

**Description:** An investigation into Microsoft 365 Copilot’s processing of multi-source context windows. This study successfully demonstrated a coordinated Indirect Prompt Injection (IPI) by distributing an adversarial payload across disparate file formats (`.docx` and `.xlsx`). By automating the fragmentation of a malicious Webhook URL via a custom Python utility (`Copilot_Inject.py`), the research proved that no individual file-level scanner could detect the latent threat.

The attack exploited "Compliance Bias"—the model's inherent tendency to prioritize structural formatting rules found in a "primary" document (a spoofed Regression Test Report) over the safety filtering of data fragments in a "secondary" source (the Excel case file). The AI was effectively manipulated into acting as a logic compiler: it autonomously retrieved the fragments, concatenated them according to the spoofed "Mandatory Auditor Rules," and rendered a functional Markdown exfiltration link. This confirms a critical architectural risk where "Instruction/Data Conflation" allows one untrusted file to weaponize the data of another within the same session context.

**Status:** `CLOSED/INFORMATIONAL` (Logic Bypass Demonstrated; MSRC Case 114093)

#
