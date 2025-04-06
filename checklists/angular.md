# Angular Security Testing Checklist

## 1. Identify Angular Application:
    - [ ] Confirm target uses Angular (or similar SPA framework).
        - [ ] Check for `angular.json`, `.component.ts` files in DevTools "Sources".
        - [ ] Look for `ng-app`, `ng-controller` HTML attributes.
        - [ ] Use browser extension (e.g., Angular Augury) for detection.

## 2. Recon Client-Side Routes & APIs:
    - [ ] Explore Angular app views, routes, functionalities.
    - [ ] Identify API endpoints in DevTools "Network" tab.

## 3. JavaScript Code Analysis (DevTools "Sources" Tab):
    - [ ] Examine Angular JavaScript code for DOM XSS & client-side flaws.
    - ** Search for JavaScript "Sink" Functions (DOM XSS):**
        - [ ] Search for `innerHTML` in JavaScript code.
        - [ ] Search for `outerHTML` in JavaScript code.
        - [ ] Search for `document.write` in JavaScript code.
        - [ ] Search for `document.location` in JavaScript code.
        - [ ] Search for `element.insertAdjacentHTML` in JavaScript code.
        - [ ] Search for `eval` in JavaScript code.
        - [ ] Search for `Function()` in JavaScript code (constructor).
        - [ ] Search for `setTimeout()` with string argument in JavaScript code.
        - [ ] Search for `setInterval()` with string argument in JavaScript code.
    - [ ] **For each "sink" found, trace data flow:**
        - [ ] Is user-controlled data (URL params, hash, DOM, etc.) flowing to the sink?
        - [ ] Is the data sanitized before reaching the sink?
    - [ ] Analyze Angular Component Logic for client-side security flaws.

## 4. Test for DOM XSS:
    - [ ] Craft DOM XSS payloads (URL hash, `javascript:` URLs, etc.).
    - [ ] Inject payloads into URL params, URL hash, client-side input locations.
    - [ ] Observe DOM XSS indicators:
        - [ ] Check for JavaScript alert/confirm/prompt boxes.
        - [ ] Check DevTools "Console" for errors/output.
        - [ ] Inspect DOM ("Elements" tab) for payload rendering.

## 5. Test Client-Side Logic Flaws:
    - [ ] Try to bypass client-side routing/access control by manipulating URLs.
    - [ ] Send direct API requests (proxy/DevTools) to bypass client-side validation/auth.

## 6. Test Backend APIs:
    - [ ] Test API endpoints for standard web vulnerabilities (XSS, Injection, Auth, etc.).
    - [ ] Use standard web app testing techniques & other cheatsheets.

## 7. Report Angular Vulnerabilities:
    - [ ] For DOM XSS/Client-Side Flaws: Explain client-side vuln & impact. Include code snippets.
    - [ ] For API Vulns: Report as standard web vulns, mention Angular context.
    - [ ] Emphasize security impact & provide clear Proof of Concept.

