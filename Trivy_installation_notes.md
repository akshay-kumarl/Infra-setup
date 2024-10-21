## Trivy by devops shack
### INSTALLATION STEPS
```
sudo apt-get install wget apt-transport-https gnupg lsb-release

wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null

echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list

sudo apt-get update

sudo apt-get install trivy
```
---



## Installation by anonymous
vim trivy.sh

copy below commands and save and run as (give executable permisisions)
> chmod +x ./trivy.sh <br/>
> sh trivy.sh    (OR)  ./trivy.sh  
```
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y
```


---

## Notes
### COMMANDS
```
trivy image imagename
```

```
trivy fs --security-checks vuln,config   Folder_name_OR_Path
```

```
trivy image --severity HIGH,CRITICAL image_name
```

```
trivy image -f json -o results.json image_name
            format   output FILENAME IMAGENAME
```

```
trivy repo repo-url
```

```
trivy k8s --report summary cluster
```


---

Targets **[what trivy can scan]**

Container Image<br/>
Filesystem <br/>
GitRepo<br/>
virtual machine image<br/>
kubernetes<br/>
AWS

Scanners [what trivy can find there]:<br/>
OS packages and software dependencies in use <br/>
known vulnerabilities <br/>
IaC issues and misconfigurations <br/>
sensitive information and secrets <br/>
software licenses



---

what trivy can scan 

1. container image
2. Filesystem
3. git repository (remote)
4. Virtualmachine image
5. kubernetes
6. AWS

What trivy can find  <br/>
OS packages and s/w vulnerabilities in use <br/>
known vulnerabilities <br/>
Iac issues and misconfiguration<br/>
sensitive information and secrets<br/>
software licenses

