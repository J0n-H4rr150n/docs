# recon-ng  
[https://github.com/lanmaster53/recon-ng](https://github.com/lanmaster53/recon-ng)  
[https://www.kali.org/tools/recon-ng/](https://www.kali.org/tools/recon-ng/)  

## Tutorial  
[https://hackertarget.com/recon-ng-tutorial/](https://hackertarget.com/recon-ng-tutorial/)  

## Ubuntu  

### Using PTF (Penetration Testers Framework)  

1. Install PTF  
[https://github.com/trustedsec/ptf](https://github.com/trustedsec/ptf)  

2. Install `recon-ng` through `PTF`  
$ `sudo ptf`  
ptf> `search recon-ng`  
ptf> `use modules/intelligence-gathering/recon-ng`  
ptf:(modules/intelligence-gathering/recon-ng)> `show options`    
ptf:(modules/intelligence-gathering/recon-ng)> `install`  
ptf:(modules/intelligence-gathering/recon-ng)> `exit`  
ptf> `exit`  

3. Run `recon-ng` from anywhere once it has been installed with `ptf`  
$ `recon-ng`  

4. Use `recon-ng`  
Create a workspace:    
[recon-ng][default] > `workspaces create {target_name}`

Open a workspace:  
$ `recon-ng -w {workspace_name}`  

See available modules:
[recon-ng][default] > `marketplace search`  

Get information about a specific module:  
[recon-ng][default] > `marketplace info {module_name}`  

Install specific module:  
[recon-ng][default] > `marketplace install {module_name}`  

Use a module:  
[recon-ng][default] > `modules load {module_name}`  

Get help for a module:
[recon-ng][default][`{module_name}`] > `help`  

See options for a module:  
[recon-ng][default][`{module_name}`] > `show options`  
