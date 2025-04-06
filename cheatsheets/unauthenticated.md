## Unauthenticated Testing Cheatsheet

**Focus:** Identifying vulnerabilities in web applications and APIs that are accessible without requiring user login or authentication.

**Why Unauthenticated Testing Matters:**

*   **Large Attack Surface:** Unauthenticated areas are often the first point of interaction for users and attackers.
*   **Direct Impact:** Vulnerabilities here can often have immediate impact (information disclosure, DoS, etc.) without needing account compromise.
*   **Common Misconfigurations:** Access control issues and logic flaws in public areas are frequently found.

**Key Vulnerability Areas in Unauthenticated Contexts:**

*   **Information Disclosure:**
    *   Accidental exposure of sensitive data in publicly accessible files (e.g., `.git/config`, backups).
    *   Verbose error messages revealing internal paths, technologies, or configurations.
    *   Publicly accessible directories listing files.
    *   Source code comments or debugging information left in client-side code.
    *   Leaking sensitive data via `Referer` header due to permissive `Referrer-Policy`.

*   **Unauthenticated Access to Sensitive Resources:**
    *   Bypassing intended access controls to reach administrative panels, internal APIs, or data intended for authorized users only.
    *   Accessing sensitive files or directories not meant for public access.

*   **Business Logic Flaws in Public Workflows:**
    *   Exploiting logic errors in unauthenticated features (e.g., guest checkout, free trials, public forms, search functionality).
    *   Bypassing rate limiting or abuse prevention mechanisms in public APIs or features.
    *   Parameter manipulation in public forms to gain unintended access or functionality.

*   **Cross-Origin Resource Sharing (CORS) Misconfigurations:**
    *   Overly permissive CORS policies allowing unauthorized cross-domain access to sensitive APIs or data, potentially leading to data theft or account takeover.
    *   Exploiting wildcard (`*`) origins or null origin issues in CORS headers.

*   **Server-Side Vulnerabilities (Unauthenticated Exploitation):**
    *   Server-Side Request Forgery (SSRF) exploitable without authentication.
    *   Command Injection, Path Traversal, or other injection vulnerabilities that can be triggered in unauthenticated areas.
    *   Denial of Service (DoS) vulnerabilities in publicly accessible features.
    *   Vulnerabilities in third-party components or libraries used in unauthenticated functionalities.

**Unauthenticated Testing Steps & Methodology:**

1.  **Reconnaissance & Attack Surface Mapping (Initial - Automated):**
    *   **Tool:** `httpx` (or `naabu` for subdomain enumeration if needed)
    *   **Purpose:**  Quickly discover live domains/paths, gather basic information (status codes, headers, server info).
    *   **Command Example:** `httpx -l target_list_168.txt -json -retries 1 -timeout 3 -follow-redirects -status-code -headers -server-header -output initial_recon.json`

2.  **Content Discovery (Unauthenticated Paths & Files):**
    *   **Tools:** `ffuf`, `dirsearch`, `gobuster`
    *   **Purpose:**  Brute-force for hidden directories, files, and endpoints that might be publicly accessible but not linked or obvious.
    *   **Wordlists:** Use common web wordlists (e.g., SecLists, dirbuster).
    *   **Targeted Fuzzing:** Focus on common admin paths, backup extensions, and sensitive file names (e.g., `.env`, `.git`, `config.php`, `admin/`, `api/v1/`).
    *   **Example (ffuf):** `ffuf -w path_wordlist.txt -u TARGET_URL/FUZZ -fs <filesize_of_404_page> -o content_discovery.txt` (Filter out common 404 responses to reduce noise)

3.  **Parameter Fuzzing & Manipulation (Unauthenticated Endpoints):**
    *   **Tools:** `ffuf`, Burp Intruder
    *   **Purpose:**  Identify vulnerabilities by manipulating URL parameters, headers, and request bodies in publicly accessible endpoints.
    *   **Fuzzing Payloads:** Use common web vulnerability payloads (e.g., XSS, SQLi, command injection, path traversal - but focus on *unauthenticated* context).
    *   **Parameter Tampering:**  Experiment with modifying parameters to bypass logic, access different data, or trigger errors.
    *   **API Testing:**  Focus on unauthenticated API endpoints. Test for:
        *   **Information Disclosure:**  Do API responses reveal more data than intended without authentication?
        *   **Mass Assignment:** Can you manipulate request parameters to modify data you shouldn't be able to?
        *   **Rate Limiting Issues:** Can you abuse public APIs due to lack of rate limiting?
        *   **Input Validation Flaws:**  Fuzz API parameters for injection vulnerabilities.

4.  **Business Logic Testing (Unauthenticated Features):**
    *   **Tools:** Browser, Burp Proxy, Manual Analysis
    *   **Purpose:**  Identify flaws in the application's logic within publicly accessible workflows.
    *   **Analyze Public Features:**  Thoroughly test guest features, signup/registration processes, public forms, search functions, contact forms, etc.
    *   **Look for Logic Flaws:**  Can you bypass steps, manipulate quantities, get free access to paid features, abuse public forms for spam or unintended actions?
    *   **Example:** Test guest checkout workflows for price manipulation, coupon abuse, or ability to access order details without login.

5.  **CORS Misconfiguration Testing:**
    *   **Tools:** Browser Developer Tools, `curl`
    *   **Purpose:**  Identify and exploit Cross-Origin Resource Sharing (CORS) vulnerabilities.
    *   **Inspect CORS Headers:**  Examine `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, `Access-Control-Allow-Credentials` headers in responses.
    *   **Test with Different Origins:** Try to access resources from different origins (attacker-controlled domain) using JavaScript (`fetch` or `XMLHttpRequest`) to see if CORS policies are correctly enforced.
    *   **Look for:** Wildcard Origins (`*`), `null` origin issues, insecure use of `Access-Control-Allow-Credentials: true`.

6.  **Error Message Analysis:**
    *   **Tools:** Browser, Burp Proxy, Manual Review
    *   **Purpose:**  Carefully analyze error messages for information leakage.
    *   **Trigger Errors:** Intentionally cause errors by providing invalid input, manipulating requests, etc., in unauthenticated areas.
    *   **Look for:** Stack traces, internal paths, database errors, technology/version disclosure in error messages.

7.  **Source Code Review (If Publicly Available):**
    *   **Tools:** Browser Dev Tools (for client-side code), `curl` or `wget` (to download publicly accessible code repositories if found - e.g., `.git/`, `.svn/`)
    *   **Purpose:**  Analyze client-side code (JavaScript, HTML, CSS) and server-side code (if accidentally exposed) for vulnerabilities and sensitive information.
    *   **Look for:** Hardcoded API keys, secrets, comments revealing logic flaws, insecure client-side handling of data, exposed API endpoints in JavaScript, etc.


**Key Things to Look For (Red Flags in Unauthenticated Testing):**

*   **Unexpected HTTP Status Codes:** `403 Forbidden`, `401 Unauthorized`, `500 Internal Server Error` on publicly accessible paths.
*   **Verbose Error Messages:** Revealing internal details.
*   **Directory Listings Enabled:** Publicly accessible directories showing file structures.
*   **Sensitive Data in Source Code Comments:** Or client-side code.
*   **Overly Permissive CORS Headers:**  Wildcard origins, `null` origin allowed.
*   **Lack of Rate Limiting:** On public APIs or features.
*   **Logic Flaws:** In guest workflows or public forms.
*   **Default Credentials:** (Though less common in "unauthenticated" context, still worth a check on login pages if found during content discovery).

**Remember:**

*   **Focus on publicly accessible areas first.**
*   **Automate initial reconnaissance to cover the large attack surface efficiently.**
*   **Prioritize targets and findings based on potential impact.**
*   **Document your steps and findings clearly.**

