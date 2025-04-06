# XSS Testing Cheatsheet

# 1. Vulnerability Description

`Cross-Site Scripting (XSS) attacks are a type of injection, in which malicious scripts are injected into otherwise benign and trusted websites. XSS attacks occur when an attacker uses a web application to send malicious code, generally in the form of a browser side script, to a different end user. Flaws that allow these attacks to succeed are quite widespread and occur anywhere a web application uses input from a user within the output it generates without validating or encoding it.`
https://owasp.org/www-community/attacks/xss/

# 2. Common Attack Vectors

- **URL Parameters (Query String Parameters):** *(High Likelihood for Reflected XSS)*
  
  - **Why:** Easily manipulated, directly reflected in URLs, often processed by client-side JavaScript. Classic Reflected XSS vector.
  - **Example:** Search terms, redirect URLs, any parameter that visibly changes page content.
  - **Think:** Any URL parameter that seems to influence content and might be used in client-side code.

- **URL Path Segments:** *(Medium to High Likelihood for Reflected XSS)*
  
  - **Why:** Similar to URL parameters, can be used in routing and processed by JavaScript, especially in modern single-page applications.
  - **Example:** Usernames in profiles, product categories, article titles in URLs.
  - **Think:** Path segments used in dynamic routing or content loading, especially in SPA frameworks.

- **Search Forms and Search Results Display:** *(High Likelihood for Reflected XSS)*
  
  - **Why:** Search queries are user-provided and often displayed prominently in results pages. If not sanitized, reflected XSS is common.
  - **Example:** Search term reflection, search result snippets, "Did you mean?" suggestions.
  - **Think:**  Search input fields and result areas - anywhere the search term is displayed back to the user.

- **Error Messages and Debug Information:** *(Lower to Medium Likelihood, often lower impact XSS)*
  
  - **Why:** Error messages sometimes reflect user input or system data. While less common for high-impact XSS, they can be injection points.
  - **Example:** 404 errors with URL paths reflected, debug messages showing user-provided values.
  - **Think:** Error pages, especially those that show details related to user actions or input.

- **User-Generated Content Display:** *(High Likelihood for Stored/Persistent XSS)*
  
  - **Why:** Content created by users (comments, profiles, posts) is stored and then displayed to *other* users later. If not sanitized when *stored* or when *displayed*, it leads to Persistent XSS (higher impact).
  - **Example:** Comment sections, forum posts, profile descriptions, product reviews, chat messages, file uploads (filenames, metadata).
  - **Think:** Any area where users can input and save data that will be shown to other users later.

- **HTTP Headers (Referer, User-Agent, etc.):** *(Lower Likelihood for XSS, often requires specific conditions)*
  
  - **Why:** Some applications might log or reflect HTTP headers. `Referer` is the most commonly reflected header. Exploiting headers often requires specific application behavior.
  - **Example:** Referer header reflected in error pages or logs. User-Agent sometimes used for content adaptation (less common for direct XSS).
  - **Think:**  Unusual reflection of HTTP headers in the application's responses.

- **DOM-based XSS Sinks (JavaScript Code, Client-Side Rendering):** *(Specific to Client-Side JavaScript)*
  
  - **Why:** Modern JavaScript frameworks often process URL fragments (`#hash`), `window.location`, `document.URL`, `document.referrer` directly in client-side scripts. If these values are used in a "sink" (a function that can execute JavaScript, like `eval()`, `innerHTML`, `outerHTML`, `document.write()`, etc.) without sanitization, DOM-based XSS occurs.
  - **Example:** Applications that use URL hash for routing, client-side search/filtering, dynamic content loading based on URL fragments.
  - **Think:** Client-side JavaScript code processing URL or DOM properties in dangerous "sink" functions.  Requires JavaScript code analysis.

# 3. What to look for

## 3. What to look for: XSS Indicators

- **Visual Indicators (Direct JavaScript Execution):**
  
  - **JavaScript Alert Boxes:**  *(The most direct and obvious sign of successful XSS. If you inject `<script>alert('XSS')</script>` or similar and an alert box pops up in the browser, you have confirmed XSS.)*
  - **`confirm()` or `prompt()` Boxes:** *(Similar to `alert()`, these JavaScript functions also create visible dialog boxes and confirm script execution.)*
  - **Changes in Page Behavior Triggered by JavaScript:** *(Beyond just HTML rendering changes. Look for things like:*
    * **Redirects:**  Your injected script causes the page to redirect to a different URL.
    * **DOM Manipulation:** Noticeable changes to the page content, layout, or elements that are *clearly* driven by JavaScript actions (e.g., elements appearing/disappearing dynamically, content being replaced or modified in a complex way).
    * **Console Output (DevTools "Console" tab):** If your injected script uses `console.log()`, `console.error()`, etc., check the browser's DevTools "Console" tab for output. This is less visually obvious to a regular user but a strong indicator for testers.

- **Source Code Clues (HTML & JavaScript Code Inspection):**
  
  - **Unsanitized Input within `<script>` Tags (Inline JavaScript):** *(Look for places in the HTML source code where user input appears to be directly embedded *inside* `<script>` tags, especially within JavaScript string literals, without proper escaping or encoding. This is a classic Reflected XSS scenario.)*
    * **Example:** `<script>var userInput = "[USER_INPUT_HERE]"; ...</script>` - If `[USER_INPUT_HERE]` is your unescaped input, XSS is highly likely.
  - **Input within Event Handler Attributes:** *(Check HTML tags for event handler attributes like `onload`, `onerror`, `onclick`, `onmouseover`, `onkeyup`, etc., where user input might be used without proper sanitization. These are common vectors for XSS.)*
    * **Example:** `<img src="[USER_INPUT_HERE]" onerror="...">` or `<div onclick="handleInput('[USER_INPUT_HERE]')">` - Unsanitized input in event handlers is a major XSS risk.
  - **DOM-based XSS "Sinks" in JavaScript Code:** *(Examine the JavaScript code for "sink" functions that can execute JavaScript (e.g., `eval()`, `innerHTML`, `outerHTML`, `document.write()`, `Function() constructor`, `setTimeout()/setInterval()` with string arguments) and trace if user-controlled data (from URL, DOM, etc.) flows into these sinks *without proper sanitization*. This requires JavaScript code analysis.)*
    * **Look for:**  Assignments to `innerHTML`, `outerHTML`, `document.write` where the assigned value is derived from URL parameters (`location.hash`, `location.search`), DOM elements (`element.value`), or other user-controllable sources, without sanitization.
  - **Input Reflected in JavaScript Comments (Less Common, but Possible):** *(In rare cases, if user input is reflected in HTML comments `` and those comments are *later* processed by client-side JavaScript in a vulnerable way (e.g., by parsing comments and using their content), it *could* potentially lead to XSS, though less direct.)*

- **Behavioral Clues (JavaScript Driven Actions):**
  
  - **Unexpected Redirects or Page Changes After Input:** *(If your input triggers an immediate redirect to an unexpected URL, or causes significant changes in the application's state or flow, it could be due to injected JavaScript altering the application's behavior.)*
  - **Network Requests Initiated by Your Input (Observed in DevTools "Network" tab):** *(If your injected payload causes the browser to make unexpected HTTP requests (e.g., to a different domain, or for unusual resources), this could indicate that your JavaScript is running and performing actions like exfiltrating data or communicating with an attacker's server. Check the "Network" tab in DevTools for unexpected outbound requests after injecting payloads.)*
  - **Changes to Cookies or Local Storage (Observed in DevTools "Application" tab):** *(If your injected JavaScript is able to manipulate cookies or local storage, you might see changes in the "Application" tab of DevTools after injecting payloads. This could be a sign of more advanced XSS impact.)*

# 4. Example payloads / testing techniques

- **a) Basic `alert()` Payload with Tracking:** `<script>alert('SynackSRT001XSS')</script>`
  
  - **Purpose:** Classic XSS test, now with "SynackSRT001XSS" in the alert message for easy searching in Burp/ZAP history.
  - **Technique:** Inject and search Burp/ZAP history for "SynackSRT001XSS" to confirm execution.

- **b) Variations of `alert()` (with Tracking):**
  
  - `<script>alert('SynackSRT002XSS - DOMAIN: ' + document.domain)</script>` _(Alerts domain with tracking)_
  - `<script>window.alert('SynackSRT003XSS')</script>` _(Alternative syntax with tracking)_
  - `<script>javascript:alert('SynackSRT004XSS')</script>` _(For `<a href="...">` with tracking)_

- **c) Image Tag with `onerror` (with Tracking):** `<img src="x" onerror="alert('SynackSRT005XSS')">`
  
  - **Purpose:** `onerror` XSS, with tracking string "SynackSRT005XSS".

- **d) Event Handler Attribute (`onmouseover` with Tracking):** `<div onmouseover="alert('SynackSRT006XSS')">Hover SynackSRT006XSS</div>`
  
  - **Purpose:** Event handler XSS, with visible "Hover SynackSRT006XSS" text and tracking in the alert. Making the tracking string visible on the page itself can also help with visual confirmation during testing.

- **e) Link Tag with JavaScript `href` (with Tracking):** `<a href="javascript:alert('SynackSRT007XSS')">Click SynackSRT007XSS</a>`
  
  - **Purpose:** `javascript:` href XSS, with visible "Click SynackSRT007XSS" link text and tracking in the alert.

- **f) DOM Clobbering + `formaction` (with Tracking):** `<form id=xssSynackSRT008><button formaction="javascript:alert('SynackSRT008XSS')">Click SynackSRT008XSS</button></form>`
  
  - **Purpose:** DOM clobbering XSS, with tracking in the `id`, button text, and alert. Using the tracking string in multiple parts of the payload increases chances of detection and confirmation.

**Important Notes for XSS Payloads (Updated):**

- **Include "SynackSRT[RandomNumber]XSS" in Payloads:** Use the "SynackSRT" convention in all your XSS payloads (and Spoof HTML Content payloads too, if you wish!) to make them easily searchable in Burp/ZAP history. Increment the random number for each payload variation to keep them unique (e.g., SynackSRT001XSS, SynackSRT002XSS, SynackSRT003XSS, etc.).

- **(Other notes from previous version remain - Start with `alert()`, Try different tags, Test in multiple locations, Observe behavior, URL encode when needed)**

# 5. Tools to use

* **Web Browser with Developer Tools (e.g., Chrome DevTools, Firefox Developer Tools) - *Crucial for Client-Side Analysis & JavaScript Debugging*:**
  
  * **Why Essential for XSS:**  Even more critical for XSS than Spoof HTML Content. XSS is about JavaScript execution in the browser, and DevTools are your window into the browser's JavaScript engine, DOM, and client-side behavior.
  
  * **Specific DevTools Tabs - *Emphasized for XSS*:**
    
    * **Elements Tab:** *(Same importance as for Spoof HTML Content - inspect the DOM, but now especially to see how your XSS payloads are being rendered and affecting the DOM structure, and to examine event handlers.)*
    * **Network Tab:** *(Still important to monitor requests, but for XSS, also watch for:*
      * **Unexpected requests initiated by your injected JavaScript:**  e.g., requests to attacker-controlled domains, exfiltration attempts.
    * **Console Tab:** ***Extremely Important for XSS:*** *(The Console is your primary tool for observing JavaScript errors and output from your injected scripts (e.g., `console.log()`, `console.error()`).  JavaScript errors in the Console can sometimes indicate issues with your XSS payload or reveal details about the application's client-side code.)*
    * **"View Page Source":** *(Useful to see the initial HTML, but for dynamic XSS and DOM-based XSS, the *live DOM* in the Elements Tab is often more critical.)*
    * **Sources Tab (Debugger):** *(For more advanced DOM-based XSS hunting, the "Sources" tab allows you to inspect JavaScript code, set breakpoints, and step through code execution. This is essential for understanding complex client-side logic and identifying DOM XSS sinks.)*
  
  * **How to Use DevTools for XSS:**
    
    * *After injecting XSS payloads,* **primarily focus on the Elements Tab and the Console Tab.**
    * **Elements Tab:** Inspect the DOM to see how your payloads are rendered, especially event handlers and script blocks.
    * **Console Tab:**  Check for JavaScript errors and any output from your payloads (if using `console.log()` etc.).
    * **Sources Tab (Advanced):** For suspected DOM XSS, use the "Sources" tab to analyze JavaScript code for vulnerable sinks and data flow.
    * **Network Tab:** Monitor for unexpected network activity triggered by your payloads.

* **Web Proxies (e.g., Burp Suite Community Edition/Professional, OWASP ZAP) - *Enhanced for XSS Testing & Automation*:**
  
  * **Why Essential for XSS:**  All the reasons from Spoof HTML Content testing apply, but proxies become even more powerful for XSS due to features for automation and more in-depth analysis.
  * **Specific Proxy Features - *Emphasized/Added for XSS*:**
    * **(Intercepting Proxy, Request Modification, Response Inspection, "Search/Find in Responses" - *Keep these from the Spoof HTML Content Tools section - they are still essential for XSS*)**
    * **Automated Scanners (Burp Scanner Professional / ZAP Active Scanner):** *(Burp Suite Professional and ZAP have active scanners that can automatically crawl the application and identify potential XSS vulnerabilities. While not foolproof, they can be a huge time-saver for initial scans and finding common reflected and stored XSS vectors.)*
      * **Use with Caution on Live Targets:**  Automated scanners can be noisy and potentially disruptive. Use them responsibly, and ideally on test/staging environments or with permission.  On Synack engagements, follow SRT program rules regarding automated scanning.
    * **Fuzzing/Intruder/Repeater (Burp Suite):** *(Burp Suite's Intruder and Repeater are invaluable for systematically testing multiple XSS payloads against different injection points.  Intruder allows you to automate payload injection and response analysis. Repeater lets you manually refine and resend requests to test variations and bypass filters.)*
    * **Spider (ZAP):** *(ZAP's Spider can automatically crawl the application to discover more URLs and potential injection points for XSS testing.  Helps expand your test coverage.)*
    * **Passive

# 6. Synack specific notes

## 6. Synack Specific Notes: XSS

* **Check Scope & Vulnerability Classifications:**
  
  * **Always review the specific SRT brief, program updates, and the "yellow box" on the SRT target page for any specific rules or exclusions related to XSS vulnerabilities.**  Synack programs sometimes have specific guidelines on the types of XSS that are in scope or out of scope (e.g., self-XSS, certain types of DOM-based XSS in very specific contexts might be lower priority or out of scope in some programs).
  * **Understand Synack's Vulnerability Rating System:** Familiarize yourself with how Synack rates XSS vulnerabilities (Critical, High, Medium, etc.).  Generally, XSS vulnerabilities that demonstrate a higher impact (account takeover, data theft, etc.) will be rated higher and receive larger bounties.

* **Focus on Impactful XSS:**
  
  * **Prioritize demonstrating high-impact XSS scenarios.** While a simple `alert('XSS')` confirms the vulnerability, for Synack, you'll generally get a better payout by showing a more significant impact.
  * **Examples of Higher Impact XSS Demonstrations (beyond `alert()`):**
    * **Session Hijacking/Cookie Theft:**  PoC demonstrating how an attacker could steal a user's session cookies using XSS.
    * **Account Takeover:**  Scenario showing how XSS could lead to account compromise (e.g., by modifying user profile data, session manipulation).
    * **Sensitive Data Theft:**  Demonstrate how XSS could be used to exfiltrate sensitive data from the page (e.g., by sending data to an attacker-controlled server).
    * **Redirection to Malicious Sites:**  Show how XSS could redirect users to attacker-controlled websites (for phishing or malware distribution - **use with extreme caution and *only* for demonstration on test accounts, *never* redirect to actual malicious sites during testing**).
    * **Defacement/DOM Manipulation:**  More elaborate DOM manipulation that visibly defaces the target page in a significant way.

* **Distinguish Reflected vs. Stored XSS (Impact & Reporting):**
  
  * **Stored XSS often has higher impact:** Persistent XSS vulnerabilities, where the injected script affects *multiple* users over time, are generally considered higher severity than Reflected XSS (which typically affects only the victim who clicks a specific link).
  * **Clearly indicate in your report whether you found Reflected or Stored XSS.**

* **Self-XSS (Usually Out of Scope):** Be aware that "Self-XSS" (where an attacker needs to trick the *victim* into pasting malicious code into their *own* browser console or address bar) is **almost always considered out of scope** on Synack and most bug bounty programs. Focus on XSS that can be triggered by an attacker *without* requiring such social engineering of the victim.

* **Proof of Concept (PoC) is Key:**
  
  * **Provide a clear and concise Proof of Concept (PoC) in your report.** Step-by-step instructions, payloads, and screenshots are essential for Synack SRT to quickly verify and triage your XSS findings.
  * **Use the "SynackSRT" tracking strings in your payloads (as we discussed in Section 4) to make your PoC easily verifiable in logs and traffic analysis.**

# 7. Reporting & Considerations (XSS specific)

- **Clearly State "Cross-Site Scripting (XSS) Vulnerability":**
  
  - Begin your report title and summary by clearly stating that you have found a "Cross-Site Scripting (XSS)" vulnerability. Be specific (e.g., "Reflected XSS in Search Parameter", "Stored XSS in User Comments").

- **Specify Reflected or Stored XSS Type:**
  
  - **Crucially, identify and explicitly state in your report whether the XSS vulnerability is "Reflected XSS" or "Stored XSS".** Explain the difference in your report, especially if it's Stored XSS (as it's generally higher impact).
    - **Reflected XSS:** Explain that the XSS is triggered immediately when a user interacts with a crafted link or input.
    - **Stored XSS:** Emphasize that the XSS payload is _permanently stored_ and affects _multiple users_ who view the vulnerable content over time.

- **Vulnerable URL & Parameters/Location (Precise Details):**
  
  - Provide the _exact vulnerable URL_ and the specific _parameter(s)_ or location where the XSS payload is injected. Be precise and make it easy for the security team to reproduce.
  - For Stored XSS, clearly indicate where the payload is stored (e.g., "User comment field on profile page").

- **Proof of Concept (PoC) - Step-by-Step Instructions:**
  
  - **Provide clear, numbered, step-by-step instructions on how to reproduce the XSS vulnerability.** Assume the security team has no prior knowledge of the issue.
  - Start from the initial URL, describe the actions to take, the input to provide, and the expected outcome (e.g., "Step 1: Go to [Vulnerable URL]", "Step 2: Enter payload `[YOUR_XSS_PAYLOAD]` in the `[parameter]` field", "Step 3: Click 'Submit'", "Step 4: Observe the JavaScript alert box appear").

- **XSS Payload (Clearly Show JavaScript Execution):**
  
  - Include the _exact XSS payload_ you used to demonstrate JavaScript execution.
  - **Showcase a payload that demonstrates _JavaScript execution_ beyond just HTML injection.** While HTML injection might be a stepping stone, focus your report on the XSS aspect.
  - Ideally, if you achieved higher impact XSS, use a payload that demonstrates that impact (cookie theft, redirect, etc.) in your PoC. If only `alert()` is achievable, use `alert('SynackSRT[YourSRTNumber]XSS')` for easy tracking.

- **Screenshot Evidence (Visual Proof):**
  
  - **Include screenshots that visually demonstrate the XSS vulnerability.**
    - Screenshot of the JavaScript `alert()` box (or `confirm()`, `prompt()`).
    - Screenshot showing the demonstrated impact if you escalated beyond `alert()` (e.g., cookie theft PoC, redirected page).
    - Screenshot of the relevant HTML source code from Browser DevTools ("Elements" tab) showing your injected payload in the DOM.

- **Explain the Security Impact (Go Beyond "Alert Box"):**
  
  - **Crucially, explain the _potential security impact_ of the XSS vulnerability.** Don't just say "JavaScript code can be executed." Explain _what an attacker could _do_ with that JavaScript execution_ to harm users or the application.
  
  - **Examples of XSS Impact to Explain (choose relevant ones):**
    
    - **Account Takeover:** How XSS could be used to steal session cookies and take over user accounts.
    - **Sensitive Data Theft:** Possibility of stealing personal information, credentials, or other sensitive data.
    - **Website Defacement:** Potential for attackers to modify the appearance and content of the website.
    - **Redirection to Malicious Sites (Phishing/Malware):** Risk of redirecting users to attacker-controlled sites for phishing attacks or malware distribution.
    - **Keylogger Injection:** Possibility of injecting keylogging scripts to capture user input.
    - **Drive-by Downloads:** Potential for delivering malware to users' browsers.

- **Positive and Professional Tone:**
  
  - Maintain a positive and professional tone in your report. Focus on clearly and constructively reporting the vulnerability and its impact.
