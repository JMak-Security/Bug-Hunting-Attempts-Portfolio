# Case Study: Indirect Prompt Injection (IPI) for Token Exfiltration

### 🎯 Executive Summary

This research investigates an **Indirect Prompt Injection (IPI)** vulnerability within an AI-powered YouTube studio assistant. By embedding a malicious `[METADATA_SYNC_BLOCK]` into a public comment, an attacker can manipulate the AI into executing unauthorized instructions, resulting in the exfiltration of sensitive identifiers to an external webhook.

---

### 🛠️ The Hypothesis

By mimicking structured system metadata, an attacker can bypass the AI's "summarization" intent and trigger "execution" logic. The hypothesis is that the LLM will prioritize text within brackets or technical headers as high-authority system instructions, allowing a third party to hijack the assistant's output and exfiltrate data via query parameters.

---

### 🔬 Proof of Concept (PoC)

**The Attack Chain:**

1. **Injection:** A comment is posted containing hidden directives disguised as a technical handshake.
2. **Trigger:** The user asks the AI to "Detailedly summarize the latest comment," pulling the "poisoned" data into the LLM's active context.
3. **Transformation:** The AI parses the comment, extracts the `Comment_ID`, converts it to a `Verification_Token`, and prepends the attacker's webhook URL.

| Step | Action | Evidence |
| --- | --- | --- |
| **1. The Payload** | Attacker posts a comment with an embedded instruction block. | `Youtube Video Comment.png` |
| **2. The Hijack** | The AI ignores its summary task to follow the "metadata" instructions. | `Studio AI Execution.png` |
| **3. Exfiltration** | The AI presents a "Sync URL" containing the hex-encoded token. | `Intended Further Execution.png` |

---

### 🛡️ Outcome & Security Analysis

**Result:** **Vulnerability Confirmed.** The AI assistant successfully processed the malicious directives and generated a functional exfiltration link.

**Technical Root Causes:**

* **Instruction/Data Conflation:** The model fails to distinguish between the content it is analyzing (the comment) and the instructions it should follow (the system prompt).
* **Persona Hijacking:** The use of "official" technical syntax (`[METADATA_SYNC_BLOCK]`) creates a high-probability trigger for the LLM to switch into a "technical assistant" persona.
* **Unsanitized Output:** The system allows the AI to construct and display arbitrary URLs based on input data without domain allowlisting.

---

### 💡 Key Takeaways

* **Boundary Enforcement:** AI agents must be explicitly instructed to treat all retrieved external data as literal strings, never as actionable code or metadata.
* **The "Confused Deputy" Risk:** This case demonstrates how an AI can be tricked into using its legitimate internal tools (like ID retrieval) for an attacker's benefit.
* **Defense in Depth:** Implementing a Content Security Policy (CSP) or a URL proxy/filter would prevent the AI from ever presenting unauthorized third-party domains to the end-user.

---
