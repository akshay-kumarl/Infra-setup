# Tomcat Installation on ubuntu server/AWSlinux 

```
   For the Yum package manager also follow the below steps 
   sudo yum update
   sudo apt update
   wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.23/bin/apache-tomcat-10.1.23.tar.gz
   sudo apt-get install default-jdk -y
   java -version
   tar -xvf apache-tomcat-10.1.23.tar.gz
   sudo mv apache-tomcat-10.1.23 /opt/tomcat
   cd /opt/tomcat/
    
   cd conf
   sudo nano tomcat-users.xml
  
```

Add the below lines to tomcat-users.xml (the location for this file is /opt/tomcat/conf)
```
<!-- Define a user with the username "admin" and password "password" -->
    <user username="admin" password="password" roles="manager-gui,admin-gui"/>
    
    <!-- Define another user with different roles -->
    <user username="user" password="userpassword" roles="manager-script"/>
```

```
sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml 
```
 Change the context.xml file as below (NOTE: only allow line need to be changed in this file)
```
<Context antiResourceLocking="false" privileged="true" > 

    <!-- Modify the Valve with the new allow attribute value -->
    <Valve className="org.apache.catalina.valves.RemoteAddrValve"
           allow=".*" />
</Context>

```


To stop the server
```
     sudo /opt/tomcat/bin/shutdown.sh
```

To start the server
```
     sudo /opt/tomcat/bin/startup.sh
```

-> we can change tomcat server default port in tomcat/conf/server.xml file
