---
title: FreeMarker邮件模板
date: 2019-08-14 20:59:01
tags: [FreeMarker,邮件]
---

`Spring`为`FreeMarker`提供了一个`FreeMarkerConfigurer`类，通过此类可方便地创建`FreeMarker`的基础环境，`Spring`提供`FreeMarkerTemplateUtils`工具类来完成解析模板的任务。



##### 模板文件

```html
<html>  
   <head>  
      <meta http-equiv="content-type" content="text/html;charset=utf8">  
   </head>  
   <body>  
       恭喜您成功注册！您的用户名为：<font color='red' size='30'>${username}</font>  
   </body>  
</html>  
```



##### 使用`FreeMarker`模板技术构造邮件内容

```java
public class TemplateEmailService {
    private JavaMailSender sender;
    // FreeMarker的技术类  
    private FreeMarkerConfigurer freeMarkerConfigurer = null;

    public void setFreeMarkerConfigurer(FreeMarkerConfigurer freeMarkerConfigurer) {
        this.freeMarkerConfigurer = freeMarkerConfigurer;
    }

    public void setSender(JavaMailSender sender) {
        this.sender = sender;
    }

    // 通过模板构造邮件内容，参数username将替换模板文件中的${username}标签。  
    private String getMailText(String username) {
        String htmlText = "";
        try {
            // 通过指定模板名获取FreeMarker模板实例  
            Template tpl = freeMarkerConfigurer.getConfiguration().getTemplate("registerUser.ftl");
            // FreeMarker通过Map传递动态数据
            Map map = new HashMap();
            // 注意动态数据的key和模板标签中指定的属性相匹配  
            map.put("username", username);
            // 解析模板并替换动态数据，最终username将替换模板文件中的${username}标签。  
            htmlText = FreeMarkerTemplateUtils.processTemplateIntoString(tpl, map);
        } catch (Exception e) {
            // TODO Auto-generated catch block  
            e.printStackTrace();
        }
        return htmlText;
    }

    // 发送模板邮件  
    public void sendTemplateMail(String username) throws MessagingException {
        MimeMessage msg = sender.createMimeMessage();
        // 由于是html邮件，不是mulitpart类型  
        MimeMessageHelper helper = new MimeMessageHelper(msg, false, "utf8");
        helper.setFrom("dongsanbo@sina.com");
        helper.setTo("dongsanbo@sina.com");
        helper.setSubject("注册成功-模板邮件");
        // 使用模板生成html邮件内容  
        String htmlText = getMailText(username);
        helper.setText(htmlText, true);
        sender.send(msg);
        System.out.println("成功发送模板邮件");
    }
    
    public static void main(String[] args) throws MessagingException {
        // TODO Auto-generated method stub  
        ApplicationContext ctx = new FileSystemXmlApplicationContext(
            "src/applicationContext.xml");
        TemplateEmailService ms = (TemplateEmailService) ctx.getBean("templateEmail");
        // 发送模板邮件  
        ms.sendTemplateMail("yefriend");
    }
}
```



##### `applicationContext.xml`配置文件

```xml
<bean id="freeMarker" class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
    <property name="templateLoaderPath" value="classpath:mailTemplate" />
    <!--指定模板文件目录-->
    <property name="freemarkerSettings">
        <!-- 设置FreeMarker环境属性-->
        <props>
            <prop key="template_update_delay">1800</prop>
            <!--刷新模板的周期，单位为秒-->
            <prop key="default_encoding">UTF-8</prop>
            <!--模板的编码格式 -->
            <prop key="locale">zh_CN</prop><!-- 本地化设置-->
        </props>
    </property>
</bean>
<bean id="templateEmail" class="com.bb.spring.email.TemplateEmailService">
    <property name="sender" ref="mailsender"></property>
    <property name="freeMarkerConfigurer" ref="freeMarker"></property>
</bean>
```

