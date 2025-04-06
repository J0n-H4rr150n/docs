## httpx JSON Output Analysis Checklist for Prioritization (Unauthenticated Testing)

**I. HTTP Status Codes ( `"status-code"` field ) - Look for these "Red Flags":**

- [ ] **1. Non-200 OK Status Codes on Unauthenticated Targets:**
    - [ ] **`403 Forbidden`:**  *(Why Interesting: Potentially restricted areas, access control misconfigurations. Might be unintentional public exposure of restricted content. Worth investigating if truly unauthenticated target.)*
    - [ ] **`401 Unauthorized`:** *(Why Interesting: Similar to 403.  Could indicate authentication *is* expected but perhaps bypassable or misconfigured in an "unauthenticated" context.  Check if authentication is consistently enforced across the target.)*
    - [ ] **`5xx Server Error` (e.g., `500 Internal Server Error`, `502 Bad Gateway`, `503 Service Unavailable`):** *(Why Interesting: Server-side issues, potential instability, error handling vulnerabilities, or misconfigurations. Could reveal internal workings through error messages. May be exploitable for DoS or information disclosure.)*
    - [ ] **`3xx Redirects` (especially to unexpected locations or loops):** *(Why Interesting:  Redirects themselves are normal, but unusual redirects or redirect loops could point to logic errors, misconfigurations, or interesting paths being revealed through redirection.)*
    - [ ] **Anything *other* than `200 OK` in general on an *unauthenticated* target:** *(Why Interesting:  Investigate *why* a public endpoint is not returning `200 OK`. Is it an error, a deliberate restriction, or something unexpected?)*

**II. HTTP Headers ( `"headers"` section in JSON ) - Focus on Security & Configuration:**

- [ ] **2. Missing Security Headers:**
    - [ ] **Missing `Content-Security-Policy` (CSP):** *(Why Interesting: Lack of CSP significantly increases XSS risk. Reportable as missing security header.)*
    - [ ] **Missing `Strict-Transport-Security` (HSTS):** *(Why Interesting: No HSTS exposes users to protocol downgrade attacks. Reportable as missing security header.)*
    - [ ] **Missing `X-Frame-Options` or `Content-Security-Policy: frame-ancestors`:** *(Why Interesting: No clickjacking protection. Reportable as missing security header.)*
    - [ ] **Missing `X-Content-Type-Options: nosniff`:** *(Why Interesting:  MIME-sniffing vulnerability risk. Reportable as missing security header.)*
    - [ ] **Permissive `Referrer-Policy` (e.g., `unsafe-url`, `origin-when-cross-origin`):** *(Why Interesting: Potential for referrer leakage of sensitive data if URLs contain secrets.)*
    - [ ] **Missing `Permissions-Policy` (or overly permissive policy):** *(Why Interesting:  Unnecessary exposure of browser features. Less critical but good to note.)*

- [ ] **3. CORS Headers ( `Access-Control-Allow-Origin`, etc. ):**
    - [ ] **Presence of CORS headers on *unauthenticated* APIs or endpoints:** *(Why Interesting: CORS policies control cross-origin access. If present on *unauthenticated* APIs, they are worth examining for misconfigurations.  Look for overly permissive policies.)*
    - [ ] **`Access-Control-Allow-Origin: *` (wildcard origin):** *(Why High Priority:  Wildcard origin is often a CORS misconfiguration.  Allows *any* website to access the resource.  Potentially severe if sensitive data is exposed via the API.)*
    - [ ] **`Access-Control-Allow-Credentials: true` with `Access-Control-Allow-Origin: *` :** *(Why Critical: Extremely dangerous combination!  Wildcard origin + credentials allowed.  Often easily exploitable for data theft or account takeover.)*
    - [ ] **`Access-Control-Allow-Origin: null` (or allows `null` origin):** *(Why Interesting: `null` origin can sometimes be abused. Investigate context.)*
    - [ ] **Unusual or unexpected allowed origins in `Access-Control-Allow-Origin`:** *(Why Interesting:  Could indicate misconfiguration or unintended access paths.)*

- [ ] **4. Caching Headers (`Cache-Control`, `Pragma`):**
    - [ ] **Missing `Cache-Control: no-store` or `Pragma: no-cache` on pages/APIs that *should* be serving dynamic or sensitive data:** *(Why Interesting:  Potential for caching sensitive information in browser or intermediate caches, leading to information leakage or stale data issues.)*
    - [ ] **Overly long `max-age` in `Cache-Control` for dynamic content:** *(Why Interesting:  May lead to users seeing outdated information or caching of data that should be fresh.)*

- [ ] **5. Server Information Disclosure in `Server` Header:**
    - [ ] **Verbose `Server` header revealing specific web server software and *version* (e.g., `Apache/2.4.41 (Ubuntu)`):** *(Why Interesting:  Information disclosure. Low severity on its own, but helps attackers fingerprint and target known vulnerabilities for that specific software version.  Note down the version.)*
    - [ ] **Outdated Server Software Version in `Server` header:** *(Why High Priority if found:  Indicates potential for known exploits for that outdated server software version.  Requires further vulnerability scanning and research.)*

- [ ] **6. Unusual or Unexpected Headers in General:**
    - [ ] Any headers you don't recognize or that seem out of place for an unauthenticated endpoint. *(Why Interesting: Could indicate custom configurations, unusual technologies, or misconfigurations.)*

**III. Technology Detection (`tech-detect` section in JSON ) - Clues for Target Functionality:**

- [ ] **7. Identify Technologies Used:**
    - [ ] Review the `tech-detect` section for each target. Note down any detected technologies, frameworks, CMS, libraries, etc.  *(Why Useful: Gives you clues about the application's architecture and potential technology-specific vulnerabilities. Helps focus later vulnerability scanning and research.)*
    - [ ] **Pay attention to specific CMS or Framework versions detected:** *(Why Useful: Knowing the CMS/framework and version can immediately point to known vulnerabilities for those specific versions.)*

**IV. CDN/WAF Detection (`cdn` field in JSON ) - Infrastructure Context:**

- [ ] **8. CDN or WAF in Use:**
    - [ ] Check the `cdn` field. Is a CDN or WAF detected for any targets? *(Why Useful: Understanding if a CDN/WAF is in place helps in planning testing strategies and potential bypass techniques later if needed.)*

**V. Other Indicators:**

- [ ] **9. Significant Variations in Content Length (`content-length` or `cl` field) for similar endpoints:** *(Why Interesting:  Might indicate different content being served, different code paths being executed, or access control variations that are worth investigating.)*
- [ ] **10.  Significantly Slower Response Times (compare response times across targets):** *(Why Interesting:  Slower response times could indicate server-side processing issues, resource exhaustion, or complex logic that might be vulnerable to DoS or other flaws.)*
- [ ] **11. Targets with `title` values (if any are found):** *(Why Low Priority initially, but note down:  Titles are less common in APIs, but if some targets have titles, quickly check them in a browser – they *might* be unexpected HTML pages or documentation.  Low priority for API testing, but worth a very quick glance.)*

**Prioritization Strategy:**

*   **High Priority:** Targets with status codes like `403`, `401`, `5xx`, critical CORS misconfigurations (wildcard + credentials), outdated server software.
*   **Medium Priority:** Targets with missing security headers (CSP, HSTS, XFO, X-Content-Type-Options), permissive `Referrer-Policy`, caching issues on sensitive data, verbose `Server` headers, unusual status codes, CDN/WAF presence.
*   **Low Priority (for now):** Targets with mostly `200 OK` status, standard headers, no obvious "red flags" in this initial `httpx` scan.  Keep these in your list, but focus deeper testing on the higher priority ones *first*.

**Using the Checklist:**

1.  Open your `httpx` JSON output file.
2.  Go through each entry (each target) in the JSON.
3.  For each checklist item above, check if that "red flag" or interesting indicator is present in the JSON data for the current target.
4.  If you find something "interesting" based on the checklist, mark the checkbox `[x]` for that item *for that target*.
5.  After going through all targets, review your marked checkboxes. Targets with *more checked boxes*, especially in the **High Priority** categories (Status Codes, CORS, Server Info), are your **top priority targets** for deeper investigation!


