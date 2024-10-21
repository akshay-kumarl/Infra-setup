Run through docker <br/>
docker run -d -p 9000:9000 --name=sonarqubee sonarqube:lts-community  

To store data <br/>
docker run -d -p 9000:9000 --name=sonarqubee -v /home/ubuntu/vol:/opt/sonarqube/data sonarqube:lts-community

---
login credentials <br/>
username: admin  <br/>
passwd: admin 

---

youtube video -  https://www.youtube.com/watch?v=r2UVTDpIUj8

Documentation link - https://docs.sonarsource.com/sonarqube/9.9/
