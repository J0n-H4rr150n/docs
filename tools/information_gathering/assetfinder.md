# assetfinder  
[https://github.com/tomnomnom/assetfinder](https://github.com/tomnomnom/assetfinder)  
'Find domains and subdomains related to a given domain'  

Install:  
$ `go install github.com/tomnomnom/assetfinder@latest`  

## Example Usage on Ubuntu  

### Get subdomains only  
$ `echo "{domain_name}" | assetfinder -subs-only | sort -u`  

### Save output to a file  
**PREREQUISITE:**  
- [assetfinder](https://github.com/tomnomnom/assetfinder)  
- [anew](https://github.com/tomnomnom/anew)  

$ `echo "{domain_name}" | assetfinder -subs-only | sort -u | anew assetfinder-output.txt`  
