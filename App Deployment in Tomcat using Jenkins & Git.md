
# Deploying Java-Maven App in Tomcat Server using Jenkins & Git




## Environment 

The Main Server of this project was deployed in Google Cloud Platform with below initial installations :-

`Instance - Ubuntu 20.04`

`Java - OpenJDK Runtime Environment (build 11.0.17+8-post-Ubuntu-1ubuntu220.04)`

`Apache Tomcat 9.x`

## Installation

Tomcat Installation
```bash
 sudo useradd -m -d /opt/tomcat -U -s /bin/false tomcat

```
# Installing Java
   ```bash
 sudo apt update
 sudo apt install default-jdk
 java -version

``` 
# Tomcat Installation 
   ```bash
cd /tmp
 wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.71/bin/apache-tomcat-9.0.71.tar.gz

``` 
  ```bash 
sudo tar xzvf apache-tomcat-9*tar.gz -C /opt/tomcat --strip-components=1
```
```bash
sudo chown -R tomcat:tomcat /opt/tomcat/
sudo chmod -R u+x /opt/tomcat/bin
```
Configuring Users
```bash
sudo nano /opt/tomcat/conf/tomcat-users.xml 
```
```bash
Change admin & Password with roles changing to "admin-gui, manager-gui"
```
Remove restriction on Manager & Host-Manager gui

```bash 
sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
```
```bash 
comment out valve with <!--- & --->
```
```bash
repeat the same for :-
sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
```
Create a systemd file
```bash
sudo update-java-alternatives -l
```
```bash 
sudo nano /etc/systemd/system/tomcat.service 
```
Add following lines 
```bash
[Unit]
Description=Tomcat
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom"
Environment="CATALINA_BASE=/opt/tomcat"
Environment="CATALINA_HOME=/opt/tomcat"
Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
```
Reload daemon so the system gets aware of new service

```bash
sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl status tomcat
sudo systemctl enable tomcat
```
To Access Web Interface - Tomcat uses Port - 8080
```bash
sudo ufw allow 8080
```
Access Tomcat with :-
```bash
http://your_server_ip:8080
```
# Installing Jenkins

```bash
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
```
```bash
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
```
```bash 
sudo apt update
```
```bash
sudo apt install jenkins
```
Change Jenkins Port to 8081
```bash
Open-  /etc/default/jenkins
Change Port to : 8081
```
Start Jenkins by using systemctl:
```bash
sudo systemctl start jenkins
```
```bash
sudo systemctl status jenkins
```
```bash
sudo ufw allow 8081
```
```bash
sudo ufw allow OpenSSH
sudo ufw enable
```
Setting up Jenkins:-
```bash
http://your_server_ip_or_domain:8081
```

# Jenkins Configuration

```bash
Manage Jenkins -> Global Tool Configuration

Add Git & Maven 
```
Installing Plugin :-
```bash
Manage Jenkins -> Manage Plugins -> Available -> Deploy to Container Plugin
```
Build New Project :
```bash
In the Configuration Section, Under Source Code Management Fill your Github/BeanStalk/Gitlab Repository URL
```
```bash
Go to the Post-build Actions section
Click on Add post-build action button
On the available options click on the Deploy war/ear to container
```
Fill the Details in Post-Build Action
```bash
War File - **/*.war
Fill - Credential of Tomcat
```
Build Jenkins Job