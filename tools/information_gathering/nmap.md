# nmap  

## Install  
```
sudo apt update
```  
```
sudo apt install nmap
```  

## Usage  

Ping Scan:  
```
sudo nmap -v -sn 192.168.0.0./16 10.0.0.0/8
```  

Service/Version Scan:    
```
sudo nmap -sV $target_ip
```  

Open Ports:  
```
sudo nmap -p- $target_ip
```  
- `-p` can be a comma-delimited list of port numbers (i.e. `p80,443`).  

Aggressive Scan:  
```
sudo nmap -A $target_ip
```  
- `-A` enables OS detection, version detection, script scanning, and traceroute.  

Firewall Scan:  
```
sudo nmap -sA $target_ip
```  

### Useful Scripts  

http-vuln:  
```
sudo nmap -sV --script vuln $target_ip -oN nmap-output.txt
```  
