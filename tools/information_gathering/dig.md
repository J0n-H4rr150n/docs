# dig  

## Ubuntu  

Install:  
$ `sudo apt update`  
$ `sudo apt install dnsutils`  

Manual:  
[https://manpages.ubuntu.com/manpages/jammy/man1/dig.1.html](https://manpages.ubuntu.com/manpages/jammy/man1/dig.1.html)  
`dig is a flexible tool for interrogating DNS name servers. It performs DNS lookups and displays the answers that are returned from the name server(s) that were queried. Most DNS administrators use dig to troubleshoot DNS problems because of its flexibility, ease of use, and clarity of output. Other lookup tools tend to have less functionality than dig.`    

$ `man dig`  

Usage:  
$ `dig -h`  
$ `dig {domain}`  
$ `dig {domain} NS`  
$ `dig @{NS} {domain}`  
$ `dig @{NS} {domain} ANY`  

