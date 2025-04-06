# Angular Security Testing Cheatsheet

## 1. Vulnerability Focus: Angular Applications

* **Target:** Web applications built using the Angular framework (or similar client-side JavaScript frameworks/Single-Page Applications - SPAs).
* **Primary Vulnerability Categories to Focus On:**
  * **DOM-based Cross-Site Scripting (DOM XSS):**  *Highest priority in Angular apps.* Exploiting vulnerabilities in client-side JavaScript code that processes user input and dynamically updates the DOM in an unsafe manner.
  * **Client-Side Logic Flaws & Client-Side Security Issues:** Vulnerabilities arising from insecure client-side implementation, including client-side access control bypasses, sensitive data exposure in client-side code, and client-side validation weaknesses.
  * **Traditional Server-Side Vulnerabilities (Backend API Focus):** While less Angular-specific, remember to also test the backend APIs that the Angular application interacts with for common server-side vulnerabilities (XSS, Injection, Authentication/Authorization issues, etc.).

## 2. Common Attack Vectors in Angular Applications

* **DOM-based XSS Vectors (Client-Side Input Processing):**
  
  * **URL Fragments (Hash Parameters - `#`):** Angular often uses URL hashes for client-side routing. Look for vulnerabilities in how Angular processes `location.hash`.
  * **`window.location`, `document.URL`, `document.referrer`:**  Angular code might directly use these DOM properties, making them potential DOM XSS sources.
  * **Angular Templates and Data Binding:**  While Angular's templating is generally secure, edge cases or developer misconfigurations can introduce vulnerabilities, especially when bypassing Angular's security features.

* **Client-Side Logic Flaw Vectors:**
  
  * **Client-Side Routing and Navigation:**  Insecure client-side routing logic that might reveal information or allow unauthorized access if manipulated.
  * **Client-Side Data Handling:**  Vulnerabilities in how Angular components and services process and handle data on the client-side, especially sensitive data.
  * **Angular Component Logic:**  Security flaws within the custom JavaScript logic implemented in Angular components and services.

* **Backend API Attack Vectors (Traditional Web App Vectors):**
  
  * **API Endpoints Accepting User Input:** Test all API endpoints that take user input for standard web vulnerabilities:
    * API Parameters (GET/POST data, JSON payloads)
    * API Headers
    * File Upload Endpoints

## 3. What to Look For: Angular Security Indicators

* **DOM XSS Indicators (JavaScript Code Focus):**
  
  * **JavaScript "Sink" Functions:** Look for usage of dangerous "sink" functions in Angular code: `innerHTML`, `outerHTML`, `document.write`, `document.location`, `element.insertAdjacentHTML`, `eval`, `Function()`, `setTimeout`/`setInterval` (with string arguments).
  * **Data Flow to Sinks from URL/DOM:** Trace data from `location.hash`, `location.search`, `document.URL`, DOM element values to these "sink" functions in Angular's JavaScript code. Use Browser DevTools "Sources" tab (Debugger) to analyze JavaScript.
  * **Angular Template Binding Vulnerabilities:**  In rare cases, look for unusual template binding patterns that might bypass Angular's security context.

* **Client-Side Logic Flaw Indicators:**
  
  * **Client-Side Routing Logic in JavaScript:** Analyze Angular routing configuration and component logic for flaws in access control or information handling based purely on client-side checks.
  * **Sensitive Information in JavaScript Code/Config:** Examine JavaScript source code, configuration files (`environment.ts`, etc.) for accidentally exposed API keys, secrets, or sensitive data.
  * **Weak Client-Side Validation:**  If validation is only in Angular code, test for bypasses by directly manipulating API requests (using proxy/DevTools).

* **Backend API Vulnerability Indicators (Standard Web App Indicators):**
  
  * **(Refer to "What to Look For" sections in cheatsheets for relevant vulnerability types like XSS, SQLi, etc.)** Standard indicators for server-side vulnerabilities in API responses and behavior.

## 4. Example Payloads/Testing Techniques: Angular Specific

* **DOM XSS Payloads (Focus on Client-Side Context):**
  
  * **URL Hash (`#`) based payloads:**  Test URL hash parameters for DOM XSS: `/#<script>alert('DOMXSS-Hash-SynackSRT001')</script>` or `/#'><img src=x onerror=alert('DOMXSS-ImgHash-SynackSRT002')>`
  * **`window.location.hash` Payloads:** If you see code using `window.location.hash`, try payloads like `javascript:window.location.hash="#<script>...<\/script>"` (URL-encode as needed).
  * **Angular Template Injection (Advanced):** In rare cases, explore Angular-specific template injection techniques, but these are often complex and less reliable. Focus on DOM XSS via sinks first.

* **Client-Side Logic Flaw Techniques:**
  
  * **Manipulating Client-Side Routing:** Try to directly modify the URL (especially URL hash in Angular apps) to bypass client-side route guards or access controls.
  * **Direct API Requests (Bypassing Client-Side Logic):** Use a proxy or DevTools to craft and send API requests directly to the backend, bypassing client-side validation or access checks performed in Angular.
  * **JavaScript Code Analysis for Logic Flaws:** Spend time examining Angular's JavaScript code in DevTools "Sources" tab to understand client-side logic and identify potential weaknesses.

* **Backend API Testing Techniques (Standard Web App Techniques):**
  
  * **(Use standard testing techniques for common web vulnerabilities - as covered in other cheatsheets):**
    * XSS Payloads (Reflected/Stored, as per XSS Cheatsheet)
    * SQL Injection Payloads (as per SQL Injection Cheatsheet)
    * Parameter Manipulation for IDOR/Access Control (as per Access Control/IDOR Cheatsheet)
    * API Fuzzing for unexpected behavior and input validation flaws.

## 5. Tools to Use: Angular Security Testing

* **Web Browser with Developer Tools (Chrome/Firefox DevTools) - *Essential for Client-Side Debugging & Analysis*:**
  
  * **Elements Tab:** Inspect DOM structure, Angular components, data bindings.
  * **Console Tab:** Monitor JavaScript errors, output from `console.log()`, and XSS payloads.
  * **Network Tab:** Examine API requests and responses, track client-server communication.
  * **Sources Tab (Debugger):** ***Critical for Angular Security Analysis.*** Analyze Angular JavaScript code, set breakpoints, step through execution to understand data flow, identify DOM XSS sinks, and client-side logic flaws.  Use "Search" within Sources tab to find keywords like `innerHTML`, `eval`, `location.hash`, etc.
  * **Angular-Specific DevTools Extensions (e.g., Augury - Chrome/Firefox):** Can help visualize Angular component structure, data flow, and routing, potentially aiding in understanding application architecture and identifying client-side vulnerabilities (optional, but can be helpful).

* **Web Proxies (Burp Suite/ZAP) - *For API & Traffic Analysis*:**
  
  * **Intercepting Proxy:** Capture and inspect API requests and responses between Angular app and backend.
  * **Request Modification (Repeater/Intruder):**  Modify API requests to test for API vulnerabilities, bypass client-side validation, or manipulate routing.
  * **Automated Scanners (Burp Scanner Pro/ZAP Active Scanner):** Scan API endpoints for standard web vulnerabilities (use responsibly).
  * **Spider/Crawler (ZAP/Burp Spider):** Discover API endpoints and Angular application routes.

## 6. Synack Specific Notes: Angular Applications

* **Focus on Impactful Vulnerabilities:** As with all Synack SRT engagements, prioritize demonstrating *impact*.  For DOM XSS and client-side logic flaws, think about how they could lead to account compromise, data theft, or other significant security consequences.
* **DOM XSS - Demonstrate Real Impact:** While `alert()` is a PoC, try to show a more tangible impact of DOM XSS if possible (e.g., cookie theft, redirect, DOM manipulation that defaces the page).
* **Client-Side Logic Flaws - Justify Severity:** Clearly articulate the security implications of any client-side logic flaws you find.  Explain how they could be exploited to bypass intended security controls or gain unauthorized access (if applicable). Purely theoretical client-side flaws with no real-world impact might be lower priority.
* **API Vulnerabilities - Standard Synack Reporting:** Report API vulnerabilities (XSS, injection, auth issues) following standard Synack SRT reporting guidelines, emphasizing impact and providing clear PoCs.
* **Review Scope and Rules:**  Always check the SRT program scope and rules for any specific guidance related to Angular applications or client-side vulnerabilities.

## 7. Reporting & Considerations: Angular Vulnerabilities

* **Clearly Indicate "Angular Application Vulnerability":**  In your report title and summary, specify that you found a vulnerability in an Angular application (e.g., "DOM XSS in Angular Application Client-Side Routing").
* **Distinguish DOM XSS from Reflected/Stored XSS:**  If you find DOM XSS, clearly differentiate it from traditional Reflected or Stored XSS. Emphasize that DOM XSS is a *client-side* vulnerability in the JavaScript code.
* **Provide JavaScript Code Snippets (for DOM XSS/Client-Side Issues):**  When reporting DOM XSS or client-side logic flaws, include relevant snippets of JavaScript code (from DevTools "Sources" tab) that demonstrate the vulnerable code and data flow. Highlight the "sink" function and the user-controlled data source.
* **Explain Client-Side Attack Vector:**  Clearly describe the client-side attack vector. How is user input (e.g., via URL hash) being processed by the Angular application's JavaScript to trigger the vulnerability?
* **Show Impact in the Browser (Client-Side Focus):** Your Proof of Concept and screenshots should clearly demonstrate the vulnerability *within the browser's client-side context*. For DOM XSS, show the JavaScript execution and its effect on the page DOM.
* **API Vulnerabilities - Report as Standard Web Vulns:** For vulnerabilities found in the backend APIs used by the Angular app, report them as standard web application vulnerabilities (using appropriate vulnerability classifications - XSS, SQLi, etc.) following Synack guidelines. Mention in the report that it's the API of an Angular application for context.
* **Focus on Impact:** As always, emphasize the security impact of the Angular vulnerability.  For DOM XSS and client-side logic flaws, explain the potential consequences for users (data theft, account compromise, etc.) due to the client-side nature of the vulnerability.