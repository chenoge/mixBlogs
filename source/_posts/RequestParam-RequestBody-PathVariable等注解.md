---
title: RequestParam、RequestBody、PathVariable等注解
date: 2019-03-17 19:44:34
tags: [RequestParam,RequestBody,PathVariable,RequestAttribute]
---

#### 注解类型

`handler method`参数绑定常用的注解，根据处理的**Request内容部分**不同分为四类：

1. 处理`requet uri` 部分的注解：`@PathVariable` 

​	注：指`uri template`中`variable`，不含`queryString`部分

2. 处理`request header`部分的注解：   `@RequestHeader`、`@CookieValue` 
3. 处理`request body`部分的注解：`@RequestParam`、` @RequestBody` 
4. 处理`attribute`类型是注解： `@SessionAttributes`、 `@ModelAttribute` 

<!--more-->

<br/>

##### `@PathVariable`

当使用`@RequestMapping URI template` 样式映射时， 即 `someUrl/{paramId}`, 这时的`paramId`可通过 `@Pathvariable`注解**绑定传过来的值到方法的参数上**。

```java
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {

    // @RequestMapping("/pets/{petId}")
    @RequestMapping(value = "/pets/{petId}", method = RequestMethod.POST)
    public void findPet(@PathVariable String ownerId, @PathVariable String petId) {
        // implementation omitted
    }
}
```

注：

若方法参数名称和需要绑定的`uri template中`变量名称不一致，需要在`@PathVariable("name")`指定`uri template`中的名称。

```java
@PathVariable("ownerId") String owner_id
```

<br/>



##### `@RequestHeader`

`@RequestHeader` 注解，可以把`Request`请求`header`部分的值绑定到方法的参数上

```http
Host                    localhost:8080
Accept                  text/html,application/xhtml+xml,application/xml;q=0.9
Accept-Language         fr,en-gb;q=0.7,en;q=0.3
Accept-Encoding         gzip,deflate
Accept-Charset          ISO-8859-1,utf-8;q=0.7,*;q=0.7
Keep-Alive              300
```

```java
@RequestMapping("/displayHeaderInfo.do")
public void displayHeaderInfo(
    @RequestHeader("Accept-Encoding") String encoding,
    @RequestHeader("Keep-Alive") long keepAlive) {
    //...
}
```

<br/>



##### `@CookieValue` 

`@CookieValue `可以把`Request header`中关于`cookie`的值绑定到方法的参数上

```http
JSESSIONID=415A4AC178C59DACE0B2C9CA727CDD84
```

```java
@RequestMapping("/displayHeaderInfo.do")
public void displayHeaderInfo(@CookieValue("JSESSIONID") String cookie) {
    //...
}
```

<br/>



##### `@RequestParam` 

- 常用来处理**简单类型的绑定**，通过`Request.getParameter()`获取的`String`可直接转换为简单类型的情况
  - 可以处理`get 方式`中`queryString`的值
  - 可以处理`post方式`中` body data`的值
- 用来处理`Content-Type`: 为 `application/x-www-form-urlencoded`编码的内容，提交方式`GET、POST` 
- 该注解有两个属性： `value、required` 
  - `value`用来指定要传入值的`id名称` 
  - `required`用来指示参数是否必须绑定

```java
@RequestMapping("/pets")
public class EditPetForm {
    @RequestMapping(method = RequestMethod.GET)
    public String setupForm(
        @RequestParam(value = "petId", required = false) int petId) {
        // ...
    }
}
```

注：

可以不使用`@RequestParam`，直接接收。此时要求`controller方法`中的**参数名称**要一致

```java
@RequestMapping("/pets")
public class EditPetForm {
    @RequestMapping(method = RequestMethod.GET)
    public String setupForm(int petId) {
        // ...
    }
}
```

<br/>



##### `@RequestBody` 

常用来处理`Content-Type`: 不是`application/x-www-form-urlencoded`编码的内容

- `application/json` 
- `application/xml` 

通过使用`HandlerAdapter`配置的`HttpMessageConverters`来解析`post data body`，然后绑定到相应的`bean`上的。

**字符串解析：** 

```javascript
$.ajax({
    url: "/login",
    type: "POST",
    data: '{"userName":"admin","pwd","admin123"}',
    content - type: "application/json charset=utf-8",
    success: function(data) {
        alert("request success ! ");
    }
});
```

```java
@requestMapping("/login")
public void login(@requestBody String userName, @requestBody String pwd) {
    System.out.println(userName + " ：" + pwd);
}
// 将JSON字符串中的两个变量的值分别赋予了两个字符串
```

<br/>



**对象解析：**

```java
@RequestMapping(value = "/something", method = RequestMethod.PUT)
public void handle(@RequestBody User user) {
    System.out.println(user.userName + " ：" + user.pwd);
}
// 将JSON字符串中的值赋予user中对应的属性上
```

注：

- `JSON`字符串中的`key`必须对应`user`中的**属性名**，否则是请求不过去的
- `@RequestBody`最多只能有一个，而`@RequestParam()`可以有多个

<br/>



##### `@SessionAttributes `

用来绑定`HttpSession`中的`attribute`对象的值，便于在方法中的参数里使用。该注解有`value、types`两个属性，可以通过名字和类型指定要使用的`attribute` 对象。

```java
@RequestMapping("/editPet.do")
// @SessionAttributes("pet")
@SessionAttributes(value = { "pet" }, types = { Integer.class })
public class EditPetForm {
    // ...
}
```

注：`@SessionAttributes`注解只能在类上使用，不能在方法上使用

<br/>



##### `@ModelAttribute、@RequestAttribute`

```java
@ModelAttribute
void beforeInvokingHandlerMethod(HttpServletRequest request) {
    request.setAttribute("foo", "hello world");
}

@RequestMapping(value = "/data/custom", method = RequestMethod.GET)
public @ResponseBody String custom(@RequestAttribute("foo") String foo) {
    return "Got 'foo' request attribute value '" + foo + "'";
}
```

```html
<div id="customArgs">
    <h3>Custom Resolvable Web Arguments</h3>
    <a id="customArg" class="textLink" href="/data/custom">Custom</a>
</div>
```

```javascript
$("a.textLink").click(function() {
    var link = $(this);
    $.ajax({
        url: link.attr("href"),
        dataType: "text",
        success: function(text) {
            alert(text)
        }
    });
});
```

```
Got 'foo' request attribute value 'hello world'
```

<br/>

