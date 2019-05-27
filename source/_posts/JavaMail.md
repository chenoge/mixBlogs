---
title: JavaMail
date: 2019-05-27 20:51:28
tags: [java,mail]
---

#### 一、RFC882文档简单说明

RFC882文档规定了如何编写一封简单的邮件(**纯文本邮件**)，一封简单的邮件包含**邮件头**和**邮件体**两个部分，邮件头和邮件体之间使用**空行**分隔。

- 邮件头包含的内容有：
  1. **from**字段：用于指明发件人
  2. **to**字段：用于指明收件人
  3. **subject**字段：用于说明邮件主题
  4. **cc**字段：抄送，将邮件发送给收件人的同时抄送给另一个收件人，收件人可以看到邮件抄送给了谁
  5. **bcc**字段：密送，将邮件发送给收件人的同时将邮件秘密发送给另一个收件人，收件人无法看到邮件密送给了谁

- 邮件体指的就是邮件的具体内容

<!--more-->

<br/>



#### 二、MIME协议简单介绍

**MIME协议是对RFC822文档的升级和补充**，它描述了如何生产一封复杂的邮件。通常我们把**MIME协议描述的邮件称之为MIME邮件**。**MIME协议描述的数据称之为MIME消息。**对于一封复杂邮件，如果包含了多个不同的数据，MIME协议规定了要使用分隔线对多段数据进行分隔，并使用Content-Type头字段对数据的类型、以及多个数据之间的关系进行描述。

![](https://chenoge.github.io/endBlogs/2019/05/27/JavaMail/1.png)

<br/>



```java
import java.io.FileOutputStream;
import java.util.Properties;
import javax.activation.DataHandler;
import javax.activation.FileDataSource;
import javax.mail.Message;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeBodyPart;
import javax.mail.internet.MimeMessage;
import javax.mail.internet.MimeMultipart;
import javax.mail.internet.MimeUtility;

public class Sendmail {

    /**
     * @param args
     * @throws Exception 
     */
    public static void main(String[] args) throws Exception {
        
        Properties prop = new Properties();
        prop.setProperty("mail.host", "smtp.sohu.com");
        prop.setProperty("mail.transport.protocol", "smtp");
        prop.setProperty("mail.smtp.auth", "true");
        
        //1、创建session
        Session session = Session.getInstance(prop);
        
        //开启Session的debug模式，这样就可以查看到程序发送Email的运行状态
        session.setDebug(true);
        
        //2、通过session得到transport对象
        Transport ts = session.getTransport();
        
        //3、连上邮件服务器
        ts.connect("smtp.sohu.com", "gacl", "邮箱密码");
        
        //4、创建邮件
        Message message = createMixedMail(session);
        
        //5、发送邮件
        ts.sendMessage(message, message.getAllRecipients());
        ts.close();
    }
    
    /**
    * @param session
    * @return
    * @throws Exception
    */ 
    public static MimeMessage createMixedMail(Session session) throws Exception {
        //创建邮件
        MimeMessage message = new MimeMessage(session);
        
        //设置邮件的基本信息
        message.setFrom(new InternetAddress("gacl@sohu.com"));
        message.setRecipient(
            Message.RecipientType.TO, 
            new InternetAddress("xdp_gacl@sina.cn")
        );
        message.setSubject("带附件和带图片的的邮件");
        
        //正文
        MimeBodyPart text = new MimeBodyPart();
        text.setContent("图片<img src='cid:aaa.jpg'>","text/html;charset=UTF-8");
        
        //图片
        MimeBodyPart image = new MimeBodyPart();
        image.setDataHandler(new DataHandler(new FileDataSource("src\\3.jpg")));
        image.setContentID("aaa.jpg");
        
        //附件1
        MimeBodyPart attach = new MimeBodyPart();
        DataHandler dh = new DataHandler(new FileDataSource("src\\4.zip"));
        attach.setDataHandler(dh);
        attach.setFileName(dh.getName());
        
        //附件2
        MimeBodyPart attach2 = new MimeBodyPart();
        DataHandler dh2 = new DataHandler(new FileDataSource("src\\波子.zip"));
        attach2.setDataHandler(dh2);
        attach2.setFileName(MimeUtility.encodeText(dh2.getName())); // 中文乱码
        
        //描述关系:正文和图片
        MimeMultipart mp1 = new MimeMultipart();
        mp1.addBodyPart(text);
        mp1.addBodyPart(image);
        mp1.setSubType("related");
        
        //描述关系:正文和附件
        MimeMultipart mp2 = new MimeMultipart();
        mp2.addBodyPart(attach);
        mp2.addBodyPart(attach2);
        
        //代表正文的bodypart
        MimeBodyPart content = new MimeBodyPart();
        content.setContent(mp1);
        mp2.addBodyPart(content);
        mp2.setSubType("mixed");
        
        message.setContent(mp2);
        message.saveChanges();
        
        message.writeTo(new FileOutputStream("E:\\MixedMail.eml"));
        //返回创建好的的邮件
        return message;
    }
}
```



