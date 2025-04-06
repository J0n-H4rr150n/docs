# Spoof HTML Content Testing Checklist

## Reconnaissance & Identify Injection Points:
- [ ] Browse target application systematically.
- [ ] Identify potential input reflection points: URL parameters, path segments, URLs in attributes, forms, errors, UGC.
- [ ] Note down URLs & parameters of interest.

## Tool Setup:
- [ ] Enable Web Proxy (Burp/ZAP).
- [ ] Configure browser to use proxy.
- [ ] Open browser DevTools (Elements, Network tabs).

## Basic HTML Payload Tests (Quick Confirmation):
- [ ] Test `<h1>Spoofed</h1>` payload.
- [ ] Test `<b>Spoofed</b>` payload.
- [ ] Test `<em>Spoofed</em>` payload.
- [ ] Test `<p>Spoofed</p>` payload.
- [ ] Test `<br><br>` payload.
- [ ] For each payload, submit request to potential injection points.

## Observe & Check Source (After Each Payload):
- [ ] Visually inspect rendered page for unexpected HTML.
- [ ] Use DevTools "Elements" tab to examine DOM & injected tags.
- [ ] "View Page Source" to compare initial vs. live HTML.
- [ ] Check for "What to look for" indicators (Section 3 of cheatsheet).

## "Break Out" Payload Tests (Attribute Escape):
- [ ] Test `"><h1>Breakout</h1>` payload.
- [ ] Test `'>"Text Breakout` payload.
- [ ] Test `'></textarea><p>After Textarea</p>` payload.
- [ ] Inject "breakout" payloads in potential attribute contexts (URLs, form values).

## Proxy Response Examination:
- [ ] Examine raw HTTP responses in Burp/ZAP.
- [ ] Use "Search/Find in Responses" to look for payload reflections.
- [ ] Search for keywords like "Spoofed", "<h1>", "Breakout" in responses.

## Realistic Spoofing Demonstration (If Injection Confirmed):
- [ ] Test Fake "SECURED By [Target Name]" heading payload.
- [ ] Test Fake login form payload.
- [ ] Test Spoofed link payload (benign URL like `example.com`).
- [ ] Test Spoofed image payload (safe placeholder or test image).

## Reporting (If Spoofing Successful):
- [ ] Document vulnerable URL & parameters.
- [ ] Document payload used for spoofing.
- [ ] Provide step-by-step reproduction instructions.
- [ ] Include screenshot of spoofed content in browser.
- [ ] Include screenshot of relevant HTML source from DevTools.
- [ ] Explain potential impact (phishing, brand damage).

