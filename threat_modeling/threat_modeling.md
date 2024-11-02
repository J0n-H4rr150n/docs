# Threat Modeling  

Threat Modeling Manifesto  
[https://www.threatmodelingmanifesto.org/](https://www.threatmodelingmanifesto.org/)  

```
Threat modeling is analyzing representations of a system to highlight concerns about security and privacy characteristics.  

At the highest levels, when we threat model, we ask four key questions:  

- What are we working on?  
- What can go wrong?  
- What are we going to do about it?  
- Did we do a good enough job?  
```  

## Reference  

OWASP Threat Modeling Cheat Sheet  
[https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)  

### STRIDE  
`A mature and popular threat modeling technique and mnemonic originally developed by Microsoft employees.`  

| Threat Category | Violates | Examples |  
| --------------- | -------- | -------- |  
| Spoofing | Authenticity | An attacker steals the authentication token of a legitimate user and uses it to impersonate the user. |  
| Tampering | Integrity | An attacker abuses the application to perform unintended updates to a database. |  
| Repudiation | Non-repudiability | An attacker manipulates logs to cover their actions. |  
| Information Disclosure | Confidentiality | An attacker extracts data from a database containing user account info. |  
| Denial of Service | Availability | An attacker locks a legitimate user out of their account by performing many failed authentication attempts. |  
| Elevation of Privileges | Authorization | An attacker tampers with a JWT to change their role. |  

## Tools  

draw.io desktop  
[https://github.com/jgraph/drawio-desktop/releases](https://github.com/jgraph/drawio-desktop/releases)  

- Threat modeling libraries for draw.io desktop  
[https://github.com/michenriksen/drawio-threatmodeling](https://github.com/michenriksen/drawio-threatmodeling)  

OWASP Threat Dragon  
[https://owasp.org/www-project-threat-dragon/](https://owasp.org/www-project-threat-dragon/)  
- [Releases](https://github.com/OWASP/threat-dragon/releases)  

Microsoft Threat Modeling Tool  
[https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool)