# Tomcat Note

#### Tomcat Official Site
- <http://tomcat.apache.org/>  
- <http://tomcat.apache.org/tomcat-7.0-doc/index.html>

#### Tomcat & JDK  
|Servlet|JSP|EL|JDK|JIT|Tomcat|Jetty|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|4.0|2.4|3.0|8|52|9.0.x||
|3.1|2.3|3.0|7|51|8.0.x|9.2|
|3.0|2.2|2.2|6|50|7.0.x|8|
|2.5|2.1|2.1|5|49|6.0.x|7|
**出自：** <http://tomcat.apache.org/whichversion.html>

#### Tomcat Directories structure
```
$CATALINA_HOME  
$CATALINA_HOME/bin  
$CATALINA_HOME/conf  
$CATALINA_HOME/logs  
$CATALINA_HOME/webapps  
```

#### Tomcat Install  
- Windows  
- Unix  

**出自：**<http://tomcat.apache.org/tomcat-7.0-doc/setup.html>

#### 配置管理员帐号  
修改`$CATALINA_HOME/conf/tomcat-users.xml`文件，添加如下配置：
> &lt;user username="zlikun" password="123456" roles="standard,manager-gui,admin-gui,manager-script" /&gt;

#### 配置工程上下文路径  
修改`$CATALINA_HOME/conf/server.xml`文件，添加如下配置：  
> &lt;Context path="/foobar" docBase="/path/to/application/foobar" /&gt;

#### Tomcat Configuration  
- 文件：`$CATALINA_HOME/conf/server.xml`  
- 参考：<http://localhost:8080/docs/config/index.html>  

###### Server  
- `className`，可选配置，必须是：org.apache.catalina.Server子类  
- `address`，The TCP/IP address on which this server waits for a shutdown command. If no address is specified, localhost is used.  
- `port`，The TCP/IP port number on which this server waits for a shutdown command. Set to -1 to disable the shutdown port.  
- `shutdown`，The command string that must be received via a TCP/IP connection to the specified port number, in order to shut down Tomcat.

###### Service  
- className  
- name

参考：
<http://www.ttlsa.com/tomcat/tomcat-configuration-server-xml-description/>  
