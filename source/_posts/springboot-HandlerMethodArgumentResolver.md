---
title: springboot-HandlerMethodArgumentResolver
date: 2019-05-28 22:13:48
tags: [springboot,HandlerMethodArgumentResolver]
---

##### 自定义参数注解（解析）

在`controller`的请求方法中，有自定义的参数注解`@UserInfo`

```java
@RequestMapping(value = "/filter/test2")
public String test2 (@RequestBody String o, @UserInfo UserInfoVo userInfoVo) {
    System.out.println("Controller中获取的请求数据：" + o);
    System.out.println("自定义注解UserInfo的值：" + userInfoVo);
    return "OK";
}
```

<!--more-->

<br/>



##### 实现**参数解析器**如下：

```java
public class UserInfoMethodArgumentResolver implements HandlerMethodArgumentResolver {
    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        System.out.println("login" + parameter.hasParameterAnnotation(UserInfo.class));
        return parameter.hasParameterAnnotation(UserInfo.class);
    }

    @Override
    public Object resolveArgument(
        MethodParameter parameter, 
        ModelAndViewContainer mavContainer, 
        NativeWebRequest webRequest, 
        WebDataBinderFactory binderFactory
    ) throws Exception {
        HttpServletRequest request = null;
        request = webRequest.getNativeRequest(HttpServletRequest.class);
        String str = HttpHelper.getBodyString(request);
        
        System.out.println("参数解析器login获取的请求数据：" + str);
        UserInfoVo u = new UserInfoVo();
        u.setName("tom");
        u.setAge(18);
        return u;
    }
}
```

<br/>



##### WebMvcConfigurerAdapter中实现顺序如下：

```java
@Override
public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers) {
    super.addArgumentResolvers(argumentResolvers);
    argumentResolvers.add(new UserInfoMethodArgumentResolver());
}
```

<br/>



##### 自定义转换器

```java
package com.datastory.epsm;

import com.datastory.epsm.utils.JsonUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.BeanUtils;
import org.springframework.core.MethodParameter;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.support.WebDataBinderFactory;
import org.springframework.web.context.request.NativeWebRequest;
import org.springframework.web.context.request.ServletWebRequest;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.method.support.ModelAndViewContainer;
import org.springframework.web.servlet.HandlerMapping;

import javax.servlet.http.HttpServletRequest;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Map;

/**
 * 自定义转换器
 */
public class JsonMethodHandleResolver implements HandlerMethodArgumentResolver {

    private static final Logger LOGGER = LoggerFactory.getLogger(JsonMethodHandleResolver.class);

    public boolean supportsParameter(MethodParameter methodParameter) {
        LOGGER.info("请求类型：" + methodParameter.getParameterType());
        return true;
    }

    public Object resolveArgument (
        MethodParameter methodParameter, 
        ModelAndViewContainer modelAndViewContainer, 
        NativeWebRequest nativeWebRequest, 
        WebDataBinderFactory webDataBinderFactory
    ) throws Exception {
        LOGGER.info("--------------------resolveArgument----------------------");
        return this.handleResolver(methodParameter, nativeWebRequest);
    }

    private Object handleResolver(
        MethodParameter methodParameter, 
        NativeWebRequest nativeWebRequest
    ) {
        String requestBody = this.getParameter(methodParameter, nativeWebRequest);

        //如果是简单类型
        if (BeanUtils.isSimpleProperty(methodParameter.getParameterType())) {
            return requestBody;
        }

        //如果是json格式
        return JsonUtils.toObject(requestBody, methodParameter.getParameterType());
    }

    private Map<String, String> getPathVariables(NativeWebRequest webRequest) {
        HttpServletRequest httpServletRequest = webRequest.getNativeRequest(HttpServletRequest.class);
        return (Map<String, String>) httpServletRequest.getAttribute(HandlerMapping.URI_TEMPLATE_VARIABLES_ATTRIBUTE);
    }

    private String getParameter(
        MethodParameter methodParameter,
        NativeWebRequest nativeWebRequest
    ) {
        //判断类型是否是简单类型
        String requestBody;
        if (BeanUtils.isSimpleProperty(methodParameter.getParameterType())) {
            requestBody = nativeWebRequest.getParameter(methodParameter.getParameterName());
            if (StringUtils.isEmpty(requestBody)) {
                requestBody = getPathVariables(nativeWebRequest).get(methodParameter.getParameterName());
            }
            LOGGER.info("传入参数:{}={}", methodParameter.getParameterName(), requestBody);
        } else {
            String pathInfo = ((ServletWebRequest) nativeWebRequest).getRequest().getPathInfo();
            requestBody = this.getRequestBody(nativeWebRequest);
            LOGGER.info("传入参数:{}", requestBody);
        }
        return requestBody;
    }

    private String getRequestBody(NativeWebRequest webRequest) {
        HttpServletRequest servletRequest = webRequest.getNativeRequest(HttpServletRequest.class);
        StringBuilder out = new StringBuilder();
        String y;
        try {
            BufferedReader in = new BufferedReader(new InputStreamReader(servletRequest.getInputStream(), "UTF-8"));
            while ((y = in.readLine()) != null) {
                out.append(y);
            }
            in.close();
        } catch (IOException e) {
            LOGGER.error(e.getMessage());
        }
        return out.toString();
    }
}
```

