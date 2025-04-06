# API Security Testing Checklist

## 1. API Endpoint Discovery:
 - [ ] Identify API endpoints used by the application.
  - [ ] Analyze client-side code (JavaScript, Angular code from Step 3).
  - [ ] Use web proxies (Burp/ZAP) to intercept traffic and log API requests during application usage.
  - [ ] Review API documentation (Swagger/OpenAPI) if available.
  - [ ] Use web crawlers or endpoint discovery tools to find hidden endpoints.
  - [ ] Analyze `routes` identified in client-side code (e.g., from `main.js` in Angular).

## 2. API Authentication & Authorization Analysis:
 - [ ] Understand the API's authentication scheme (API keys, tokens, OAuth, session cookies, etc.).
 - [ ] Identify how authorization is enforced (roles, scopes, permissions, etc.).
 - [ ] Note any potential weaknesses in the authentication or authorization design.

## 3. Test for Broken Authentication (BA):
 - [ ] **Authentication Bypass:**
  - [ ] Try to access API endpoints without authentication credentials.
  - [ ] Test for default credentials or easily guessable credentials.
  - [ ] Look for exposed API keys or secrets in client-side code or configuration.
 - [ ] **Brute-Force Attacks:**
  - [ ] If rate limiting is weak, attempt to brute-force login endpoints or token generation endpoints (Burp Intruder, Hydra).
  - [ ] Test for common passwords or weak credentials.
 - [ ] **Token Vulnerabilities:**
  - [ ] Capture API tokens and try to reuse them after expiry or for different users.
  - [ ] Analyze token structure (JWT) for manipulation or signature bypass.
 - [ ] **Session Management Issues:**
  - [ ] Test for session fixation or session hijacking vulnerabilities.

## 4. Test for Broken Access Control (BAC) & IDOR:
 - [ ] **Insecure Direct Object References (IDOR):**
  - [ ] **ID Parameter Manipulation:** Increment/decrement IDs in API URLs/parameters to access other resources.
  - [ ] Fuzz ID parameters with sequential IDs or common patterns.
  - [ ] Try to access resources using IDs of different users or objects.
  - [ ] Check for sequential or predictable IDs in API endpoints.
  - [ ] Look for APIs using simple integer IDs instead of UUIDs/GUIDs.
 - [ ] **Authorization Bypass:**
  - [ ] **Role/Scope Manipulation:** Modify or remove roles/scopes in tokens/requests to bypass checks.
  - [ ] Try to access admin/privileged endpoints with regular user credentials.
  - [ ] **HTTP Method Tampering:** Use unexpected HTTP methods (POST, PUT, DELETE) to bypass method-based authorization.
  - [ ] **Forced Browsing:** Access undocumented or hidden API endpoints and test authorization.
  - [ ] Check for inconsistent authorization across different endpoints.
  - [ ] Identify APIs relying solely on client-side authorization.

## 5. Test for Injection Vulnerabilities:
 - [ ] **SQL Injection:**
  - [ ] Test API endpoints interacting with databases for SQL injection (using SQLi payloads in parameters, bodies, headers).
  - [ ] Look for API error messages revealing database errors.
 - [ ] **NoSQL Injection:**
  - [ ] If the API uses NoSQL databases, test for NoSQL injection (using NoSQLi payloads).
 - [ ] **Command Injection:**
  - [ ] Test for command injection in API endpoints that might execute system commands based on user input.
 - [ ] **Payload Fuzzing:** Fuzz API inputs (parameters, bodies, headers) with injection payloads (Burp Intruder, fuzzing tools).
 - [ ] Analyze API error messages for injection clues.

## 6. Test for Security Misconfigurations:
 - [ ] **Exposed Admin Endpoints:** Look for and test admin or privileged API endpoints that might be unintentionally exposed.
 - [ ] **Verbose Error Messages:** Check for API error messages revealing sensitive information or internal details.
 - [ ] **Insecure Headers:** Analyze HTTP headers for security misconfigurations (e.g., missing security headers, insecure CORS policies).
 - [ ] **Default Credentials:** Test for default credentials on API-related services or components.
 - [ ] **Unnecessary Features:** Identify and test unnecessary or insecure API features that are enabled.

## 7. Test for Lack of Resources & Rate Limiting:
 - [ ] **Rate Limiting on Authentication:** Test rate limiting on login/token generation endpoints to prevent brute-force.
 - [ ] **Rate Limiting on Data Retrieval:** Test rate limiting on API endpoints that retrieve sensitive or large amounts of data to prevent DoS.
 - [ ] **Resource Exhaustion:** Try to exhaust API resources (e.g., by sending many large requests) to test for DoS vulnerabilities.

## 8. Test for Exposed Sensitive Data:
 - [ ] **Sensitive Data in Responses:** Analyze API responses for unintentional exposure of sensitive data (PII, credentials, API keys, internal data).
 - [ ] **Sensitive Data in Logs:** Check API logs and error logs for exposed sensitive data.
 - [ ] **Insufficient Output Filtering:** Identify APIs lacking proper filtering or masking of sensitive data in responses.

## 9. Test for Mass Assignment:
 - [ ] **Data Update APIs:** Identify API endpoints that handle data updates (PUT, PATCH, POST requests).
 - [ ] **Property Manipulation:** Try to modify object properties you shouldn't be able to (e.g., by adding extra parameters in request bodies).
 - [ ] Check if APIs blindly accept client-provided data without filtering.

## 10. Test for Vulnerable Components:
 - [ ] Identify versions of frameworks, libraries, and components used by the API (if possible).
 - [ ] Check for known vulnerabilities in identified components (using vulnerability databases, scanners).

## 11. Reporting & Documentation:
 - [ ] For each vulnerability found, clearly document:
  - [ ] API endpoint URL, HTTP method, parameters, request/response.
  - [ ] Step-by-step Proof of Concept (PoC) with API requests/responses (Burp/ZAP).
  - [ ] Security Impact in API context (data breach, access, etc.).
  - [ ] Remediation recommendations.
 - [ ] Follow Synack SRT reporting guidelines for API vulnerabilities.


