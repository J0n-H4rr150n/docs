# amass
[https://github.com/owasp-amass/amass](https://github.com/owasp-amass/amass)  

| Name | Description | Metadata |
| ------ | ------------ | ---------- |
| **[amass](https://github.com/owasp-amass/amass)** | In-depth attack surface mapping and asset discovery |[![stars](https://badgen.net/github/stars/owasp-amass/amass)](https://badgen.net/github/stars/owasp-amass/amass) [![contributors](https://badgen.net/github/contributors/owasp-amass/amass)](https://badgen.net/github/contributors/owasp-amass/amass) [![watchers](https://badgen.net/github/watchers/owasp-amass/amass)](https://badgen.net/github/watchers/owasp-amass/amass) [![open-issues](https://badgen.net/github/open-issues/owasp-amass/amass)](https://badgen.net/github/open-issues/owasp-amass/amass) [![closed-issues](https://badgen.net/github/closed-issues/owasp-amass/amass)](https://badgen.net/github/closed-issues/owasp-amass/amass) [![last-commit](https://badgen.net/github/last-commit/owasp-amass/amass)](https://badgen.net/github/last-commit/owasp-amass/amass) |  

Install:  
```
go install -v github.com/owasp-amass/amass/v4/...@master
```   

## Example Usage on Ubuntu  

### Enum
```
amass enum -brute -active -d {domain_name} -r 8.8.8.8,1.1.1.1
```  
