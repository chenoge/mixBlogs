---
title: springMVC-forward和redirect
date: 2019-05-31 10:43:43
tags: [spring,forward,redirect]
---

#### 一、跳转

```java
import javax.servlet.http.HttpServletRequest; 
import javax.servlet.http.HttpServletResponse; 
import org.springframework.stereotype.Controller; 
import org.springframework.web.bind.annotation.RequestMapping; 
   
@Controller 
public class Login{ 
    
    /**转发**/ 
    @RequestMapping("/login.do") 
    public String login(HttpServletRequest request, HttpServletResponse response){ 
        request.setAttribute("message", "hello");
        return "forward:/index.do";  // forward在跳转后可以取到message值 
    } 
    
    @RequestMapping("/index.do") 
    public String index(HttpServletRequest request, HttpServletResponse response){ 
        return "welcome";
    } 
       
    /**重定向**/ 
    @RequestMapping("/logout.do") 
    public String logout(HttpServletRequest request, HttpServletResponse response){ 
        request.setAttribute("message", "hello");
        return "redirect:/register.do";  // redirect在跳转后无法取到message值 
    }
    
    @RequestMapping("/register.do") 
    public String register(HttpServletRequest request, HttpServletResponse response){ 
        return "register";
    }
}
```

注：forward跳转后地址栏URL不会改变而redirect会改变

<!--more-->

<br/>



#### 二、区别

**redirect**方式相当于”response.sendRedirect()”. 这种方式浏览器地址栏最后显示的路径是转发后的新的路径。工作方式是这样的, **服务器端会首先发一个response给浏览器, 然后浏览器收到这个response后再发一个requeset给服务器, 然后服务器发新的response给浏览器. 这时页面收到的request对象是重新从浏览器发来的**。这种方式的特点：

- 在转发前后有两个不同的request对象,转发前后的两个控制器在request上的参数(request.getParameter())和request属性(request.getAttribute())不能共享;
- 如果转发前后的两个控制器都配置在spring 拦截器范围内,这样拦截器会拦截前后两个request,即会拦截两次;
- 最后返回到浏览器后,因为地址栏显示的是转发后的url,所以刷新页面时只会执行后面的url映射的控制器.

<br/>



**forward**方式相当于request.getRequestDispatcher().**forward**(request,response)，这种方式的外部特征是浏览器地址显示的路径是转发前的路径。工作方式是这样,**forward 发生在服务器内部,在前一个控制器处理完毕后,直接进入下一个控制器处理, 并将最后的response发给浏览器.** 这种方式的特点：

- 转发前后是同一个request,后一个控制器可共享前一个控制器的参数与属性;
- 因为是同一个request,拦截器只会拦截前一个url,如果前一个url在映射时未配置到拦截器拦截，则拦截后一个url，即只拦截一次;
- 最后返回到浏览器后,因为地址栏显示的是转发前的url,所以刷新页面时会依次执行前后两个控制器. 

<br/>

