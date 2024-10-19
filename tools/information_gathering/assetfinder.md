# assetfinder  
[https://github.com/tomnomnom/assetfinder](https://github.com/tomnomnom/assetfinder)  
`Find domains and subdomains related to a given domain`  

| Name | Description | Metadata |
| ------ | ------------ | ---------- |
| **[assetfinder](https://github.com/tomnomnom/assetfinder)** | Find domains and subdomains related to a given domain |[![stars](https://badgen.net/github/stars/tomnomnom/assetfinder)](https://badgen.net/github/stars/tomnomnom/assetfinder) [![contributors](https://badgen.net/github/contributors/tomnomnom/assetfinder)](https://badgen.net/github/contributors/tomnomnom/assetfinder) [![watchers](https://badgen.net/github/watchers/tomnomnom/assetfinder)](https://badgen.net/github/watchers/tomnomnom/assetfinder) [![last-commit](https://badgen.net/github/last-commit/tomnomnom/assetfinder)](https://badgen.net/github/last-commit/tomnomnom/assetfinder)  [![open-issues](https://badgen.net/github/open-issues/tomnomnom/assetfinder)](https://badgen.net/github/open-issues/tomnomnom/assetfinder) [![closed-issues](https://badgen.net/github/closed-issues/tomnomnom/assetfinder)](https://badgen.net/github/closed-issues/tomnomnom/assetfinder) |

Install:  
```
go install github.com/tomnomnom/assetfinder@latest
```   

## Example Usage on Ubuntu  

### Get subdomains only  
```
echo "{domain_name}" | assetfinder -subs-only | sort -u
```  

### Save output to a file  
**PREREQUISITE:**  
- [assetfinder](https://github.com/tomnomnom/assetfinder)  
- [anew](https://github.com/tomnomnom/anew)  

```
echo "{domain_name}" | assetfinder -subs-only | sort -u | anew assetfinder-output.txt
```
