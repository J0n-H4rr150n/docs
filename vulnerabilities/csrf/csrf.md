# Cross-Site Request Forgery (CSRF)  

[https://book.hacktricks.xyz/pentesting-web/csrf-cross-site-request-forgery](https://book.hacktricks.xyz/pentesting-web/csrf-cross-site-request-forgery)  

- [ ] Try POST to GET  
- [ ] Try removing the CSRF token completely  
- [ ] Check if CSRF token is not tied to the user session  
- [ ] Try a method bypass `?_method=PUT`  
- [ ] Try a method bypass with headers: `X-HTTP-Method` `X-HTTP-Method-Override` `X-Method-Override`  
- [ ] If there is a custom header token try the request without it or with a different value.  
- [ ] Check if CSRF token is verified by a cookie  
- [ ] Try changing Content-Type in the `enctype` of a form: `application/x-www-form-urlencoded` `multipart/form-data` `text/plain` `application/json` `text/xml` `application/xml`  
- [ ] Test for referrer / origin bypass `<meta name="referrer" content="never">`
