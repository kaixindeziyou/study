# web.xml文件2.3、2.4、2.5、3.0不同版本问题

Web.xml文件有2.3、2.4、2.5、3.0版本，其中有一个很重要的配置差异：

在Servlet 2.5 版本中可以这样配置，多个url映射到同一个servlet。具体如下。

<servlet-mapping>
<servlet-name>servletName</servlet-name>
<url-pattern>/index</url-pattern>
<url-pattern>/login</url-pattern>
</servlet-mapping>

在2.3或2.4中不能。

Servlet 2.5

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         id="WebApp_ID" version="2.5">
  
</web-app>
```