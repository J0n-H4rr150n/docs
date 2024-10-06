# subdomains  

## crt.sh  
[https://crt.sh/](https://crt.sh/)  

**Curl Command:**  
Add the following curl command to a file named `crt.sh`  
```
curl -s "https://crt.sh/?q={domain_name}&output=json"
```  
$ `chmod +x crt.sh`    
$ `./crt.sh | jq -r '.[].name_value' | sort -u`  

**Install CLI Tool on Ubuntu:**    
[https://github.com/az7rb/crt.sh](https://github.com/az7rb/crt.sh)  
$ `git clone https://github.com/az7rb/crt.sh.git && cd crt.sh/`  
$ `chmod +x crt.sh crt_v2.sh`  

Steps:  
$ `./crt.sh -d {domain_name}`  
$ `./crt.sh -o {organization_name}`  
$ `ls output`  
$ `cat output/org.{organization_name}.txt output/domain.{domain_name}.txt | anew output/domains.txt`  
$ `cat output/domains.txt | grep "{domain_name}$" | sort -u | anew output/domains_{domain_name}.txt`  

## subdomainfinder.c99.nl  
[https://subdomainfinder.c99.nl/](https://subdomainfinder.c99.nl/)  

Steps:  
- Run a scan on [https://subdomainfinder.c99.nl/](https://subdomainfinder.c99.nl/)
- Export the scan results to `.json`  
$ `cat subdomainfinderc99nl.json | jq -r '.[].subdomain' | grep "{domain_name}$" | sort -u | anew subdomainfinderc99nl_{domain_name}.txt`  