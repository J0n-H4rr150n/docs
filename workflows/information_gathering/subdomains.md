# subdomains  

## crt.sh  
[https://crt.sh/](https://crt.sh/)  

**Curl Command:**  

*PREREQUISITES:*  
- curl  
- [anew](https://github.com/tomnomnom/anew)  

Add the following curl command to a file named `crt.sh`  
```curl -s "https://crt.sh/?q={domain_name}&output=json"```  

$ ```chmod +x crt.sh```    
$ ```./crt.sh | jq -r '.[].name_value' | grep "\.{domain_name}$" | sort -u | anew domain.{domain_name}.txt```  
$ ```cat domain.[domain_name}.txt | httpx -json -status-code -ip -cname -status-code -web-server -tech-detect -title -content-length -content-type -location -favicon -hash sha512 -rt -lc -wc -o httpx_domains.jsonl```  
$ ```cat httpx_domains.jsonl | jq -s '[.[]]' > httpx_domains.json```    
$ ```cat httpx_domains.json | jq -r '.[] | select(.status_code == 200) | .url' | sort -u | anew httpx_domains_sc_200.txt```    

**Install CLI Tool on Ubuntu:**    
[https://github.com/az7rb/crt.sh](https://github.com/az7rb/crt.sh)  
$ `git clone https://github.com/az7rb/crt.sh.git && cd crt.sh/`  
$ `chmod +x crt.sh crt_v2.sh`  

Steps:  
$ `./crt.sh -d {domain_name}`  
$ `./crt.sh -o {organization_name}`  
$ `ls output`  
$ `cat output/org.{organization_name}.txt output/domain.{domain_name}.txt | anew output/domains.txt`  
$ `cat output/domains.txt | grep "\.{domain_name}$" | sort -u | anew output/domains_{domain_name}.txt`  

## subdomainfinder.c99.nl  
[https://subdomainfinder.c99.nl/](https://subdomainfinder.c99.nl/)  

Steps:  
- Run a scan on [https://subdomainfinder.c99.nl/](https://subdomainfinder.c99.nl/)
- Export the scan results to `.json`  
$ `cat subdomainfinderc99nl.json | jq -r '.[].subdomain' | grep "\.{domain_name}$" | sort -u | anew subdomainfinderc99nl_{domain_name}.txt`  
