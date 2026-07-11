# Case Study: Youtube Studio AI Indirect Prompt Injection (IPI) for Metadata Spoofing

## 🎯 Executive Summary

This research investigates an **Indirect Prompt Injection (IPI)** vulnerability within an AI-powered YouTube studio assistant. By embedding a malicious `[METADATA_SYNC_BLOCK]` into a public comment, an attacker can manipulate the AI into executing unauthorized instructions. This results in the **hijacking of the model's logic flow**, forcing it to construct and present an exfiltration URL containing internal identifiers.

## 🛠️ The Hypothesis

The hypothesis is that the LLM will prioritize text within brackets or technical headers as high-authority system instructions. By mimicking structured system metadata, an attacker can bypass the AI's "summarization" intent and trigger "execution" logic, allowing a third party to hijack the assistant's output.

## 🔬 Proof of Concept (PoC)

### The Attack Chain

1. **Injection:** A comment is posted disguised as a technical handshake containing `[METADATA_SYNC_BLOCK]`.
2. **Trigger:** The user asks for a "Detailed summary," pulling the "poisoned" comment into the active context.
3. **Transformation:** The AI parses the comment, extracts the identifier, and generates a functional exfiltration link.

| Step | Action | Evidence |
| --- | --- | --- |
| **1. The Payload** | Attacker posts a comment with a Base64-encoded prefix and hex-conversion instructions. | *Youtube Video Comment.png* |
| **2. The Hijack** | The AI interprets the text as "Technical Meta-data" and generates a Verification Token. | *Studio AI Execution.png* |
| **3. Exfiltration** | The AI presents a "Complete Sync URL" linking to an external webhook. | *Intended Further Execution.png* |

## 📺 Research Demonstration

* **[Video 1: The Sandbox Simulation](https://youtu.be/[PLACEHOLDER])** *Watch the sandbox demostrate the research expected outputs (Disclaimer: Due to Youtube shadow-banned my comments, this video is used to simulate the outputs during my research.)*

## 🛡️ Outcome & Security Analysis

**Result: Instruction Hijack & Logic Bypass Confirmed.**
The AI assistant successfully processed the malicious directives and generated a functional exfiltration link.

> **Technical Note on Scoping:** During testing, the model retrieved the attacker's unique metadata (Comment ID) rather than the victim's private session data. While this limited the immediate data impact, the core vulnerability—the model's failure to maintain instruction boundaries—remains a critical security risk.

### Technical Root Causes

* **Instruction/Data Conflation:** The model fails to distinguish between the comment content and the instructions it should follow.
* **Persona Hijacking:** The use of `[METADATA_SYNC_BLOCK]` triggers the LLM to switch into a "logging/verification" persona.
* **Cross-Context Metadata Leakage:** The AI’s internal toolset lacks **Scope Awareness**, treating the metadata of the *injected* comment as a valid variable for system-level logic.

## 💡 Key Takeaways

* **Boundary Enforcement:** AI agents must be explicitly instructed to treat all retrieved external data as literal strings.
* **The "Confused Deputy" Risk:** The AI was successfully tricked into using its legitimate internal tools (ID retrieval and Hex conversion) for an attacker's benefit.
* **Defense in Depth:** Implementing a URL proxy or domain allowlisting would prevent the AI from presenting unauthorized third-party domains (like `webhook.site`) to the end-user.

---
