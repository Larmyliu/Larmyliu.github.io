今天在写实验报告，遇到一个问题
在开启服务器时报404
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200611103709173.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
手动输入地址后报500
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020061110365769.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
仔细看一下500页面的报错信息，发现了这个提示
`java.lang.IllegalStateException: Optional int parameter 'cno' is present but cannot be translated into a null value due to being declared as a primitive type. Consider declaring it as object wrapper for the corresponding primitive type.`
应该是`web.xml`中信息有误
检查后发现，在配置前端控制器时候命名不规范，	`studentmanage`应该是`dispatcherServlet`
`<welcome-file>`也要修改成对应的名字




```xml
  <servlet>
    <servlet-name>studentmanage</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc-config.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>studentmanage</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

```
修改后，Tomcat运行，成功
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200611104226162.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
