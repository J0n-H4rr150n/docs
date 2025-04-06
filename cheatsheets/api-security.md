# API Security Testing Cheatsheet

## 1. Vulnerability Focus: APIs (Application Programming Interfaces)

* **Target:**  Web APIs, typically RESTful APIs using JSON or XML, often powering web applications, mobile apps, and integrations.
* **Primary Vulnerability Categories to Focus On (OWASP API Security Top 10 & Common API Issues):**
  * **Broken Access Control (BAC):** *High Priority*.  API authorization flaws that allow users to access resources or perform actions they should not be permitted to (e.g., bypassing authentication, improper authorization checks, IDOR, privilege escalation).
  * **Insecure Direct Object References (IDOR):** *High Priority, a subset of BAC*.  APIs expose internal object references (IDs) without proper authorization, allowing attackers to access unauthorized data by manipulating these IDs.
  * **Broken Authentication (BA):**  Flaws in API authentication mechanisms that allow attackers to bypass authentication or impersonate users (e.g., weak authentication schemes, predictable tokens, session management issues).
  * **Injection (SQLi, NoSQLi, Command Injection, etc.):**  APIs are often vulnerable to injection attacks if they don't properly sanitize or validate input data before using it in database queries, system commands, or other backend operations.
  * **Security Misconfiguration (SM):**  Insecure default configurations, misconfigured permissions, unnecessary features enabled, lack of security hardening in API infrastructure and related services.
  * **Insufficient Logging & Monitoring (ILM):** Lack of sufficient logging and monitoring makes it difficult to detect, respond to, and investigate security incidents targeting APIs.
  * **Exposed Sensitive Data (ESD):** APIs unintentionally expose sensitive data (PII, credentials, API keys, internal data) in API responses, logs, or error messages due to improper data handling or insufficient output filtering.
  * **Lack of Resources & Rate Limiting (LRRL):** APIs lack proper rate limiting or resource controls, making them vulnerable to denial-of-service (DoS) attacks and brute-force attempts.
  * **Mass Assignment (MA):** APIs blindly accept data from clients without proper filtering, allowing attackers to modify object properties they shouldn't be able to (especially relevant in APIs that handle data updates/creation).
  * **Vulnerable and Outdated Components (VOC):** APIs rely on vulnerable or outdated libraries, frameworks, and software components, exposing them to known vulnerabilities.

## 2. Common API Attack Vectors

* **Direct API Endpoint Access:** Attackers directly access API endpoints, bypassing the intended user interface (web app, mobile app) to interact with the API directly and potentially exploit vulnerabilities.
* **API Parameter Manipulation:** Tampering with API parameters (query parameters, POST data, JSON/XML payloads) to bypass authorization, inject malicious data, or trigger unexpected behavior.
* **HTTP Method Manipulation:** Using unexpected HTTP methods (e.g., using `POST` instead of `GET`, `PUT` instead of `PATCH`) on API endpoints to bypass routing or trigger different code paths and potential vulnerabilities.
* **Header Manipulation:** Modifying HTTP headers (e.g., `Authorization`, `Content-Type`, custom headers) to bypass authentication, inject data, or exploit header-based vulnerabilities.
* **Session/Token Hijacking & Manipulation:**  Exploiting vulnerabilities in API authentication and session management to hijack user sessions or manipulate tokens to gain unauthorized access.
* **API Fuzzing:** Sending a large number of unexpected or malformed requests to API endpoints to discover input validation flaws, error handling issues, and potential crashes or vulnerabilities.
* **Logic/Business Logic Flaws:** Exploiting flaws in the API's business logic and workflows to achieve unintended outcomes, bypass security controls, or gain unauthorized access (often related to access control and authorization).

## 3. What to Look For: API Security Indicators

* **Broken Access Control (BAC) Indicators:**
  
  * **Lack of Authorization Checks:** API endpoints that don't properly verify user permissions before granting access to resources or actions.
  * **Inconsistent Authorization:** Authorization checks performed inconsistently across different API endpoints or operations.
  * **IDOR Patterns:** API endpoints that use predictable or sequential IDs in URLs or request parameters (e.g., `/api/users/123`, `/api/orders?orderId=456`).
  * **Verbose Error Messages Revealing Internal IDs:** API error messages that expose internal object IDs or data structures.
  * **Client-Side Authorization Reliance:** APIs that rely solely on client-side code to enforce authorization, with no server-side checks.

* **Insecure Direct Object References (IDOR) Indicators:**
  
  * **Sequential or Predictable IDs:** API endpoints using sequential or easily guessable IDs in URLs or parameters.
  * **Lack of Randomization/GUIDs:** API endpoints using simple integer IDs instead of UUIDs/GUIDs or other randomized identifiers.
  * **No Authorization Checks on ID-Based Access:** API endpoints that directly retrieve or manipulate objects based on IDs without verifying if the user is authorized to access that specific object.

* **Broken Authentication (BA) Indicators:**
  
  * **Weak Authentication Schemes:** APIs using basic authentication, weak custom authentication, or easily bypassable authentication methods.
  * **Predictable or Insecure Tokens:** API tokens that are easily predictable, guessable, or vulnerable to brute-force or token reuse attacks.
  * **Session Fixation/Session Hijacking:** APIs vulnerable to session fixation or session hijacking due to insecure session management.
  * **Lack of Rate Limiting on Authentication Endpoints:** Authentication endpoints (login, token generation) lacking rate limiting, making them vulnerable to brute-force attacks.

* **Injection Vulnerability Indicators:**
  
  * **API Endpoints Accepting User Input:** API endpoints that take user input in parameters (GET/POST), JSON/XML payloads, or headers.
  * **Database Interactions:** APIs that interact with databases to retrieve or store data based on user input.
  * **Command Execution:** APIs that execute system commands or interact with external systems based on user input.
  * **Lack of Input Validation:** APIs that don't properly validate or sanitize user input before processing it.
  * **Error Messages Revealing Backend Technology:** API error messages that reveal the backend database type, framework, or other internal details, potentially hinting at injection points.

## 4. Example Payloads/Testing Techniques: API Specific

* **Broken Access Control (BAC) & IDOR Testing Techniques:**
  
  * **IDOR Parameter Manipulation:**
    * Increment/Decrement IDs in API URLs or parameters (e.g., `/api/users/123` -> `/api/users/124`).
    * Fuzz ID parameters with lists of sequential IDs or common ID patterns.
    * Try to access resources using IDs of other users, resources, or objects.
  * **Authorization Bypass via Role/Scope Manipulation:**
    * If API uses roles or scopes in tokens or requests, try to modify or remove them to bypass authorization checks.
    * Attempt to access admin or privileged API endpoints with regular user credentials.
  * **Forced Browsing of API Endpoints:**
    * Use API documentation, client-side code analysis, or web crawlers to discover hidden or undocumented API endpoints.
    * Try to access these endpoints directly and see if they have proper authorization controls.
  * **HTTP Method Tampering:**
    * Try using different HTTP methods (e.g., `GET`, `POST`, `PUT`, `DELETE`) on API endpoints to see if authorization is method-dependent and if you can bypass checks by using unexpected methods.

* **Broken Authentication (BA) Testing Techniques:**
  
  * **Brute-Force Authentication Endpoints:**
    * Use tools like Burp Intruder or Hydra to brute-force login endpoints or token generation endpoints (if rate limiting is weak).
    * Test for common passwords or weak credentials.
  * **Token Replay & Manipulation:**
    * Capture valid API tokens and try to reuse them after they should have expired or for different users.
    * Analyze token structure (e.g., JWT) for potential manipulation or weaknesses in signature verification.
  * **Session Fixation/Session Hijacking Testing:**
    * Test for session fixation vulnerabilities by trying to set session IDs or tokens in advance.
    * Monitor session behavior after login and logout to identify session management issues.

* **Injection Testing Techniques:**
  
  * **Standard Injection Payloads:**
    * Use standard SQL injection, NoSQL injection, command injection, XML injection, etc., payloads in API parameters and request bodies (refer to relevant injection cheatsheets).
    * Adapt payloads to the specific data format (JSON, XML, URL-encoded).
  * **Fuzz API Inputs for Injection:**
    * Use fuzzing tools to send a wide range of potentially malicious inputs to API endpoints to trigger injection vulnerabilities.
    * Focus on API parameters that are likely to be used in database queries or backend commands.
  * **Analyze API Error Messages for Injection Clues:**
    * Look for API error messages that reveal database errors, backend errors, or code execution errors when injecting payloads. This can confirm injection vulnerabilities and provide clues about the backend technology.

## 5. Tools to Use: API Security Testing

* **Web Proxies (Burp Suite/ZAP) - *Essential for API Interception, Modification, and Replay*:**
  
  * **Intercepting Proxy:** Capture and inspect API requests and responses.
  * **Repeater:** Manually modify and replay API requests to test for vulnerabilities.
  * **Intruder:** Automate API fuzzing and brute-force attacks.
  * **Scanner (Burp Scanner Pro/ZAP Active Scanner):** Automated API vulnerability scanning (use responsibly and within scope).

* **API Testing Clients (Postman, Insomnia, REST Client VSCode Extension) - *For Crafting and Sending API Requests*:**
  
  * Create, save, and organize API requests.
  * Set custom headers, request bodies (JSON/XML), and authentication.
  * Easier to manage complex API workflows compared to just using a browser.

* **Command-Line Tools (curl, httpie) - *For Scripting and Automation*:**
  
  * Automate API requests in scripts (Bash, Python, etc.).
  * Useful for quick API testing and integration with other tools.

* **API Fuzzers (wfuzz, ffuf, custom scripts):**
  
  * Fuzz API parameters and endpoints with various payloads to discover vulnerabilities.

## 6. Synack Specific Notes: API Applications

* **Prioritize Impactful API Vulnerabilities:** Focus on demonstrating *security impact* for API vulnerabilities.  Show how an API vulnerability could lead to data breaches, account compromise, privilege escalation, or other significant security consequences.
* **Broken Access Control & IDOR - High Impact:** These API vulnerabilities are often considered high severity, especially if they allow access to sensitive data or critical functionalities. Clearly demonstrate the unauthorized access and potential impact.
* **Injection Vulnerabilities in APIs - Significant Risk:** API injection vulnerabilities (SQLi, etc.) can have severe consequences. Focus on demonstrating data exfiltration, data modification, or code execution if possible.
* **API Documentation & Swagger/OpenAPI:** If API documentation (Swagger/OpenAPI) is available, use it to understand API endpoints, parameters, and authentication schemes. However, also test endpoints and parameters *not* documented, as they might have different security controls.
* **Review Scope and Rules:** Always check the SRT program scope and rules for any specific guidance related to API testing, allowed attack types, and reporting requirements.

## 7. Reporting & Considerations: API Vulnerabilities

* **Clearly Indicate "API Vulnerability":** In your report title and summary, specify that you found a vulnerability in the API (e.g., "IDOR in API Endpoint `/api/users/{id}`").
* **Focus on API-Specific Details:** In your report, clearly describe the API endpoint URL, HTTP method, parameters, request/response bodies, and authentication/authorization scheme relevant to the vulnerability.
* **Demonstrate API Request/Response Flow in PoC:** Your Proof of Concept (PoC) should clearly show the API requests and responses that demonstrate the vulnerability. Include screenshots or code snippets of API requests/responses from Burp/ZAP or API testing clients.
* **Explain Security Impact in API Context:**  Emphasize the *security impact* of the API vulnerability in the context of the API and the application it supports. How could this vulnerability be abused by an attacker to harm users, the application, or the organization through the API?
* **For Access Control/IDOR:** Clearly show the unauthorized access to data or functionalities via API requests and parameter manipulation. Explain what data or actions are exposed and the potential impact.
* **For Injection:** Provide PoC API requests with injection payloads and show the resulting error messages or data exfiltration/manipulation.
* **Follow Synack Reporting Guidelines:**  Adhere to standard Synack SRT reporting guidelines for API vulnerabilities, emphasizing impact, providing clear PoCs, and following vulnerability classification and severity guidelines.
