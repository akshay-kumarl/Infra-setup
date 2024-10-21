
## Docker installation in Amazon Linux
    sudo yum update -y 
    sudo yum install docker -y
    sudo service docker start
    sudo usermod -aG docker ec2-user
restart system 


## Install Docker In Ubuntu 

```
sudo apt update
sudo apt install docker.io -y
sudo usermod -aG docker ubuntu 

```

## Verify docker installation

```
docker -v
```


# Docker compose setup

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```


---

docker installation by devops shack

```
sudo usermod -aG docker ubuntu
newgrp docker #this command is used to for norestart
```






