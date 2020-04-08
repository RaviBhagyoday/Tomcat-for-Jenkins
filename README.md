# Tomcat-for-Jenkins
Tomcat for Jenkins in AWS


Apache Tomcat is an open-source implementation of the Java Servlet, JavaServer Pages, Java Expression Language and WebSocket technologies. Tomcat provides a "pure Java" HTTP web server environment in which Java code can run.

<h1> Prerequisites </h1>

1. EC2 RHEL instance
    Security Group with Port 8090 open.
    
2. Java v1.8.x

<h1> Install Java </h1>
<hr>

We will be using open java for demo.

<pre>
yum install java-1.8*
</pre>

<h1> Confirm Java version </h1>
<hr>

Lets install java and set the java home

<pre>
java -version
find /usr/lib/jvm/java-1.8* | head -n 3
vi /etc/profile


JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.242.b08-0.el8_1.x86_64
PATH=$PATH:$JAVA_HOME
export PATH
</pre>
<pre>
source /etc/profile
</pre>

<h1> Install Apache Tomcat </h1>
<hr>

Download tomcat packages from <a href="https://tomcat.apache.org/download-80.cgi">https://tomcat.apache.org/download-80.cgi</a> onto /opt on EC2 instance

<pre>
# Create tomcat Directory
cd /opt

# Download tomcat in /opt - download  *.tar.gz file from provided link
wget http://mirrors.fibergrid.in/apache/tomcat/tomcat-8/v8.5.35/bin/apache-tomcat-8.5.35.tar.gz
tar -xvzf /opt/apache-tomcat-8.5.35.tar.gz

</pre>

give executing permissions to startup.sh and shutdown.sh which are under bin.

<pre>
 chmod +x /opt/apache-tomcat-8.5.35/bin/startup.sh shutdown.sh
</pre>

create link files for tomcat startup.sh and shutdown.sh

<pre>
ln -s /opt/apache-tomcat-8.5.35/bin/startup.sh /usr/local/bin/tomcatup
ln -s /opt/apache-tomcat-8.5.35/bin/shutdown.sh /usr/local/bin/tomcatdown
tomcatup
</pre>

<b>check point:</b>
access tomcat application from browser on prot 8080
http://<Public_IP>:8080

Using unique ports for each application is a best practice in an environment. But tomcat and Jenkins runs on ports number 8080. Hence lets change tomcat port number to 8090. Change port number in conf/server.xml file under tomcat home

<pre>
cd /opt/apache-tomcat-8.5.35/conf

# update port number in the "connecter port" field in server.xml
# restart tomcat after configuration update

tomcatdown
tomcatup

</pre>
<b>check point:</b>
access tomcat application from browser on prot 8090
http://<Public_IP>:8090

now application is accessible on port 8090. but tomcat application doesnt allow to login from browser. changing a default parameter in context.xml does address this issue

<pre>
#search for context.xml
find / -name context.xml
</pre>

above command gives 3 context.xml files. comment () "Value ClassName" field on files which are under webapp directory. After that restart tomcat services to effect these changes

<pre>
tomcatdown
tomcatup
</pre>

Update users information in the tomcat-users.xml file goto tomcat home directory and Add below users to conf/tomcat-user.xml file

```sh
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
<user username="deployer" password="deployer" roles="manager-script"/>
<user username="tomcat" password="s3cret" roles="manager-gui"/>
```
Restart serivce and try to login to tomcat application from the browser. This time it should be Successful
