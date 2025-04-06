# Spoof HTML Content Testing Cheatsheet

# 1. Vulnerability Description

HTML Injection / Content Spoofing
```
HTML Injection that allows attackers to replace genuine website content with malicious or misleading content, leading to phishing, brand damage, etc.
```  

# 2. Common Attack Vectors

- **URL Parameters (Query String Parameters):**
    - **Why:** URL parameters are often used to dynamically control page content, especially in web applications that build pages based on parameters. They are directly visible in the URL and easily manipulated by attackers.
    - **Example:** Search parameters, product IDs, article IDs, language codes, redirect URLs, pagination parameters, etc.
    - **Think:** Any URL parameter that seems to influence what content is shown on the page.

- **URL Path Segments:**
    - **Why:** Similar to URL parameters, path segments (parts of the URL after the domain, separated by slashes) can also be used to dynamically determine content.
    - **Example:**  Application paths that include identifiers or names, like `/profile/USERNAME`, `/products/CATEGORY/ITEM_ID`, etc.  The example payload URL `"https://www.TARGET.com/">... .childrenlist.html#"` hints at path segment injection possibilities with `.childrenlist.html`.

- **Full URLs Embedded in HTML (especially within attributes):**
    - **Why:** Applications frequently embed URLs within HTML, especially in attributes like `href` (for links), `src` (for images, iframes, scripts), `action` (for forms), `data-` attributes, etc. If these URLs are constructed using dynamic data and not properly encoded, it's a prime injection point. **This is directly related to your example payload!**
    - **Example:** Redirect URLs, URLs for dynamically loaded content, links to user profiles or resources, URLs used in "share" functionalities.

- **Search Forms and Search Results Display:**
    - **Why:** Search functionalities take user-provided input and often display it back in the search results page. If the search query is not HTML-encoded before being displayed in the HTML of the results page, HTML injection is possible.
    - **Example:** The search term itself being reflected in the results page, highlighting of search terms within results.

- **Error Messages and Debug Information Displayed to Users:**
    - **Why:**  Error messages sometimes inadvertently include user input or system data that is rendered in HTML.  If not properly encoded, these error messages can become injection points.
    - **Example:**  Error messages showing invalid filenames, database query errors, or reflecting user-provided IDs in error messages.  Often lower impact, but still worth checking.

- **User-Generated Content Display (If any exists in the target app):**
    - **Why:**  Applications that allow users to create content (profiles, comments, forum posts, reviews, etc.) are classic XSS/HTML Injection targets. If the application displays user-generated content in HTML without proper sanitization, injection is highly likely.
    - **Example:** Profile descriptions, comment sections, forum posts, product reviews, blog post content.  Less directly related to *spoofing* but important for general HTML injection testing, and *spoofing* can be a way to exploit this.

- **Less Common, but Consider:**
    - **HTTP Referer Header Reflection:** Some older or poorly built applications might reflect the `Referer` header in HTML, especially in error pages or logs. Less common, but technically possible.
    - **WebSockets or Real-time Communication (if applicable):** If the application uses WebSockets to display dynamic updates, and if messages from the WebSocket server are directly inserted into the DOM without encoding, there might be an injection point.  More advanced/less common for simple spoofing.

# 3. What to look for

- **Visual Indicators:**
    - **Unexpected HTML Rendering:** Output on the page renders as HTML (e.g., bold text, larger headings, links appearing) instead of plain text after submitting input.
    - **Broken Page Layout or Structure:**  Page layout becomes distorted, elements are misaligned, or parts of the page disappear unexpectedly.
    - **Appearance of Unexpected HTML Elements:** Headings (`<h1>`-`<h6>`), paragraphs (`<p>`), links (`<a>`), images (`<img>`), forms (`<form>`, `<input>`, etc.), lists (`<ul>`, `<ol>`) appear on the page as a result of your input.

- **Source Code Clues (View Page Source/DevTools):**
    - **Unencoded HTML Tags in Source:** Raw HTML tags (like `<h1>`, `<b>`, `<script>`, `<img>`) are directly visible in the HTML source code corresponding to your input, instead of HTML entities (like `&lt;h1&gt;`).
    - **Input Reflected within HTML Tags without Encoding:** Your input appears to be directly embedded inside HTML tags or attributes (e.g., `<p>[YOUR_INPUT_HERE]</p>`, `<div attr="[YOUR_INPUT_HERE]">`) without proper HTML encoding.

- **Behavioral Clues (Less Direct for Spoofing, more for XSS in general):**
    - **JavaScript Execution (for XSS):**  Injecting `<script>alert('XSS')</script>` results in a JavaScript alert box appearing.
    - **Unexpected Application Behavior:**  HTML injection disrupts JavaScript functionality, form submissions, or other intended application behaviors (more relevant in complex scenarios).

# 4. Example Payloads / Testing Techniques

- URL encode payloads as needed
	- `<h1>` might be encoded as `%3Ch1%3E`
## Generic Payloads

Visual: Large, bold heading
```
<h1>SynackSRT</h1>
```

Visual: Bold text
```
<b>SynackSRT</b>
```

Visual: Emphasized/italicized text
```
<em>SynackSRT</em>
```

Visual: Hyperlink
```
<a href="https://synack.com">SynackSRT</a>
```

Visual: Extra line breaks / vertical spacing
```
<br><br><br>
```
## Breaking Out of HTML Attribute Context

Closes attribute and tag, injects heading
```
"><h1>SynackSRT</h1>
```

Closes attribute and tag, injects plain text
```
'>"SynackSRT
```

Break out of textarea
```
'><textarea><p>SynackSRT</p>'
```

Break out of style tags
```
'></style><p>SynackSRT</p>'
```

Technique
- Append `">` or `'>` at the beginning of your spoofing payloads
## Content Spoofing Examples

* `<h1><b>SECURED By [TARGET NAME]</b></h1>` - *(Fake "Secured By" heading - adapt "[TARGET NAME]" dynamically)*

* `<h2>Fake Important Notice!</h2><p>This is a spoofed message.</p>` - *(Fake notice with heading and paragraph)* 

* `<a href="https://malicious.example.com">Click here for a prize!</a>` - *(Spoofed link to a malicious site - **use with extreme caution & only on test systems!** For bug bounty, focus on demonstrating the *possibility* without actually clicking or causing harm)* 

* `"><input type="text" placeholder="Email"><br><input type="password" placeholder="Password"><br><input type="submit" value="Login">` - *(Fake Login Form - adapted from Synack example, good for demonstrating phishing potential)* 

* `"><img src="https://[attacker-controlled-server]/spoofed-image.png" style="width:300px;height:100px;">` - *(Spoofed image - host image on your own server for testing, or use a safe placeholder image URL. Useful for brand spoofing or replacing legitimate images)*
## Test in Different Locations

**(Remind yourself to systematically test these locations with the above payloads):* 
* URL Parameters 
* URL Path Segments 
* URLs Embedded in HTML attributes 
* Search Forms 
* Error Messages 
* User-Generated Content areas
## Encoding Considerations

* **URL Encode for URL Parameters:** If injecting into URL parameters, URL-encode your payloads (especially special characters like `<`, `>`, `"`, ` `, etc.) to ensure they are correctly transmitted in the URL. (Tools like Burp Suite, ZAP, or online encoders can help). 

* **HTML Encode for HTML Context (Less Common for Spoofing):** In some rare cases, you might try HTML-encoding your payloads (e.g., `&lt;h1&gt;Spoofed&lt;/h1&gt;`) to see if the application *double*-decodes or incorrectly handles HTML entities, but for Spoof HTML Content, usually, you want the browser to interpret the *raw* HTML tags, so URL-encoding is more often relevant for transmission, not HTML-encoding the payload itself.
# 5. Tools to use

## Web Browser with Developer Tools (e.g., Chrome DevTools, Firefox Developer Tools):

*   **Why Essential:**  Browser DevTools are built-in and provide real-time insights into how the browser is rendering the page, executing JavaScript, and handling network requests.  They are indispensable for understanding and debugging client-side issues.

*   **Specific DevTools Tabs to Focus On for Spoof HTML Content:**
	*   **Elements Tab:**  *(Inspect the DOM (Document Object Model) in real-time to see the actual HTML structure rendered by the browser. Crucial for identifying if your injected HTML is being interpreted and how it's affecting the page structure.  This is where you'll see the *rendered result* of your spoofing attempts.)*
	*   **Network Tab:** *(Monitor HTTP requests and responses. Useful to see the initial HTML of the page, and any subsequent requests for resources. While less direct for *spoofing itself*, understanding the network flow is always helpful in web app testing.)*
	*   **Console Tab:** *(Check for JavaScript errors. While not always directly related to Spoof HTML Content, errors can sometimes indicate issues with how the page is rendering or processing data, which *might* be related to injection points.)*
	*   **"View Page Source" (Alternative to Elements Tab for static HTML):** *(Right-click "View Page Source" gives you the initial HTML received from the server. While Elements Tab shows the *live* DOM, "View Page Source" is useful to see the *original* HTML and compare it to the live DOM after JavaScript execution.)*
*   **How to Use for Spoof HTML Content:**
	*   *After injecting a payload,* immediately **switch to the Elements Tab** and inspect the relevant part of the DOM to see if your injected HTML tags have been rendered as intended.
	*   Use **"View Page Source"** to compare the initial HTML with the live DOM to understand how the browser has processed and rendered the page, including your injected content.


## Web Proxies (e.g., Burp Suite, OWASP ZAP):

*   **Why Essential:** Proxies act as intermediaries between your browser and the web application, allowing you to intercept, inspect, and modify HTTP requests and responses. Crucial for manual testing and vulnerability analysis.
*   **Specific Proxy Features Helpful for Spoof HTML Content:**
	*   **Intercepting Proxy Functionality:** *(Intercept and examine HTTP requests *before* they are sent to the server, and HTTP responses *before* they are received by your browser. This allows you to see the raw communication and manipulate it.)*
	*   **Request Modification:** *(Modify requests on-the-fly. For Spoof HTML Content, you can use this to easily inject your payloads into URL parameters, form data, headers, and resend the modified request to test different injection points.)*
	*   **Response Inspection:** *(Examine server responses in detail. Crucial for seeing how the server is handling your injected payloads and how the response is structured.)*
	*   **"Search/Find in Responses" Feature:** *(This is key for efficiency! Both Burp Suite and ZAP have powerful search functionalities to search through intercepted HTTP responses.  You can search for specific strings or patterns within the HTML content of responses.)*

```
***How to Use Search for Spoof HTML Content:** Use the "Search" or "Find" function within your proxy's HTTP history or scanner results.  **Search for unique strings or parts of your injected HTML payloads** (e.g., search for "Spoofed Heading", "<b>", "<h1>", or unique words from your payloads).** This helps you quickly locate where your injected content is being reflected in the server's response, and confirm if it's being reflected in a potentially vulnerable way.
```

# 6. Synack specific notes

- Check the brief, updates, and yellow box for any mention of this type of vulnerability being out of scope.

# 7. **Reporting & Escalation: Spoof HTML Content vs. XSS**  
	- **If you ONLY achieve Spoof HTML Content (no JavaScript Execution):**
	    - **Document and Report:** If you can successfully spoof content but _cannot_ execute JavaScript (e.g., no `<script>` execution, no event handler XSS), then document your findings clearly and prepare your Synack report. "Spoof HTML Content" is still a valid vulnerability and can have impact (phishing, brand damage). Follow the reporting guidelines below.
	    - **Reporting Guidelines (for Spoof HTML Content):**
	        - Vulnerable URL and parameters/location.
	        - Payload used to demonstrate the spoofing.
	        - Step-by-step instructions to reproduce the vulnerability.
	        - Screenshot of the spoofed content in the browser and the relevant HTML source code (from DevTools).
	        - Explain the potential impact (phishing, brand damage, social engineering).
	
	- **If you Suspect or Can Escalate to XSS (JavaScript Execution Possible):**
	    - **Prioritize XSS Testing:** If, during your Spoof HTML Content testing, you find indications that you _might_ be able to execute JavaScript (e.g., basic HTML injection works easily, input is reflected in script context, event handlers might be vulnerable), **shift your focus to _escalating to XSS_.** XSS vulnerabilities generally have a higher impact and bounty reward on platforms like Synack.
	    - **XSS Payloads to Try:** Test with classic XSS payloads to confirm JavaScript execution:
	        - `<script>alert('XSS')</script>`
	        - `<img src="x" onerror="alert('XSS')">`
	        - `<div onmouseover="alert('XSS')">Hover Me</div>` (Test event handlers)
	    - **Refine Payloads & Bypasses (If Needed):** If basic XSS payloads are blocked, try common XSS bypass techniques (e.g., different tag variations, encoding, escaping filters). (We can create a separate "XSS Payloads & Bypass Cheatsheet" later if you want to go deep into XSS).
	    - **Confirm Impact of XSS:** If you achieve JavaScript execution, demonstrate a clear impact beyond just `alert()`. Examples for higher impact XSS:
	        - Cookie theft (proof-of-concept of stealing session cookies).
	        - Redirection to an attacker-controlled site.
	        - DOM manipulation to deface the page more extensively.
	- **Reporting Guidelines (for XSS - if Escalated):**
	    - If you successfully escalate to XSS, focus your report on the XSS vulnerability and its _higher_ impact.
	    - Vulnerable URL and parameters/location.
	    - XSS Payload that demonstrates JavaScript execution.
	    - Step-by-step instructions to reproduce the XSS.
	    - Screenshot of the XSS alert or demonstrated impact.
	    - Explain the potential impact of the XSS vulnerability (account takeover, data theft, malware distribution, etc. - justify a higher severity).
	    - _(You can briefly mention that you initially identified HTML Injection/Spoof Content as a stepping stone to finding the XSS)._
	
