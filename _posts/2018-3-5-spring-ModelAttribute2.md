之前项目中并自己并没有怎么使用到过@ModelAttribute这个注解，接手一个老项目的时候发现项目中大量使用@ModelAttribute这个注解，在这里就整理下这个注解常用的方式，也为自己做个记录，以免久了不用又忘记了 
@ModelAttribute使用大致有有两种，一种是是直接标记在方法上，一种是标记在方法的参数中，两种标记方法产生的效果也各不相同，这里就列举下两种标记所产生的效果

首先先做点简单的准备工作，写一个只包含一个button的jsp页面，这里可以看见，只是写了个简单按钮事件，跳转的modelTest.do这个路径

```java
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>

<%@taglib uri="http://www.springframework.org/tags/form" prefix="form" %>  
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <base href="<%=basePath%>">

    <title>My JSP 'index.jsp' starting page</title>
    <meta http-equiv="pragma" content="no-cache">
    <meta http-equiv="cache-control" content="no-cache">
    <meta http-equiv="expires" content="0">    
    <meta http-equiv="keywords" content="keyword1,keyword2,keyword3">
    <meta http-equiv="description" content="This is my page">
    <!--
    <link rel="stylesheet" type="text/css" href="styles.css">
    -->
    <script type="text/javascript" src="<%=basePath%>static/js/jquery.min.js"></script>
  </head>
  <script type="text/javascript">
        $(function(){
            $("#modelTest").on("click",function(){

                window.location.href="<%=basePath%>model/modelTest.do";
            })
        });
  </script>
  <body>

    <input type="button" id="modelTest" value="测试">


  </body>
</html>
```

# 注解标记在方法上
下面，再让我们写一个controller控制器，这里我们再控制器中写两个方法，其中一个使用@RequestMapping方法路径标记为modelTest.do，另外一个方法不标记路径，使用@ModelAttribute标记
```java
package com.lovo.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping(value="model")
public class ModelAttributeTest {

    @ModelAttribute
    public void init()
    {
        System.out.println("最先执行的方法");
    }

    @ModelAttribute
    public void init02()
    {
        System.out.println("最先执行的方法02");
    }

    @RequestMapping(value="modelTest.do")
    public String modelTest()
    {
        System.out.println("然后执行的方法");
        return "modelTest";
    }

    @ModelAttribute
    public void init03()
    {
        System.out.println("最先执行的方法03");
    }
}
```
部署后运行，点击页面测试按钮，查看控制台输出，这个时候你会发现，后台控制器并没有直接进入modelTest.do的路径，而是先执行了被@ModelAttribute标记的init方法。应该这么理解，当同一个controller中有任意一个方法被@ModelAttribute注解标记，页面请求只要进入这个控制器，不管请求那个方法，均会先执行被@ModelAttribute标记的方法，所以我们可以用@ModelAttribute注解的方法做一些初始化操作。当同一个controller中有多个方法被@ModelAttribute注解标记，所有被@ModelAttribute标记的方法均会被执行，按先后顺序执行，然后再进入请求的方法。

[!photo](http://img.blog.csdn.net/20170225232820631?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSGFycnlfWkhfV2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

下面方法做一些变形，变形为带有参数的返回，这样也是实际开发中经常会操作的 
首先像创建个pojo对象，对象包含userName，sex两个属性。并对JSP及控制器代码做一些修改

页面使用EL表达式接受返回参数
```java
<script type="text/javascript">
        $(function(){
            $("#modelTest").on("click",function(){

                window.location.href="<%=basePath%>model/modelTest.do";
            })
        });
  </script>
  <body>

    <input type="button" id="modelTest" value="测试">

    <input type="text" value="${pojo.userName }">
    <input type="text" value="${pojo.sex }">

  </body>
```
1.  @ModelAtterbute方法无返回值情况
```java
@Controller
@RequestMapping(value="model")
public class ModelAttributeTest {

    @ModelAttribute
    public void init(Model mode)
    {
        PojoTest pojo=new PojoTest(null, "小明", "男");
        mode.addAttribute("pojo", pojo);
    }

    @RequestMapping(value="modelTest.do")
    public String modelTest()
    {
        return "modelTest";
    }

}
```
访问ModelTest.jsp页面并点击测试

[!photo](http://img.blog.csdn.net/20170225235606065?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSGFycnlfWkhfV2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
出现如下结果
[!photo](http://img.blog.csdn.net/20170225235700722?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSGFycnlfWkhfV2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
从执行结果看出，当访问请求时，会首先访问init方法，然后再对test方法进行访问，并且是同一个请求，因为model模型数据的作用域与request相同，所以可以用此标记直接标记在方法上对实际要访问的方法进行一些初始化操作

2. @ModelAttribute标记方法有返回值
```java
@Controller
@RequestMapping(value="model")
public class ModelAttributeTest {

    @ModelAttribute
    public String init(Model mode)
    {
        System.out.println("进入init方法");
        PojoTest pojo=new PojoTest(null, "小明", "男");
        mode.addAttribute("pojo", pojo);
        return "model/befor.do";
    }

    @RequestMapping(value="befor.do")
    public String befor(){

        System.out.println("进入befor方法");
        return "index";

    }

    @RequestMapping(value="modelTest.do")
    public String modelTest()
    {
        System.out.println("进入modelTest方法");
        return "modelTest";
    }

}
```
这里稍微做了点变形，可以看到在被@ModelAttribute方法中设值了返回路径为befor方法，但是在在代码运行的过程中并不会跳转befor方法，而是在代码执行完成return之前直接跳转了实际请求的方法。
[!photo](http://img.blog.csdn.net/20170226002852409?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSGFycnlfWkhfV2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

3. 当@RequestMapping标记和@ModelAttribute同时标记在一个方法上
```java
@Controller
@RequestMapping(value="model")
public class ModelAttributeTest {

    @RequestMapping(value="modelTest.do")
    @ModelAttribute(value="pojo")
    public String modelTest()
    {
        System.out.println("进入modelTest方法");

        return "modelTest";
    }

}
```

点击测试页面发现进入控制器后返回，页面报404，这是因为当两个注解标记到同一个方法上时，逻辑视图名并不是返回值，而是返回请求的路径，根据model/modelTest.do生成逻辑视图。在这里我们修改下代码，把controller上的@RequestMapping标记去掉，并修改下页面的请求路径，让生成的视图路径和访问的页面路径相同
```java
@Controller
public class ModelAttributeTest {

    @RequestMapping(value="modelTest.do")
    @ModelAttribute(value="pojo")
    public String modelTest()
    {
        System.out.println("进入modelTest方法");

        return "modelTest";
    }

}

  <script type="text/javascript">
        $(function(){
            $("#modelTest").on("click",function(){

                window.location.href="<%=basePath%>modelTest.do";
            })
        });
  </script>
  <body>

    <input type="button" id="modelTest" value="测试">

    <input type="text" value="${pojo }">

  </body>
```
  点击测试页面，会发现当两个注解同时注解到一个方法上时，方法的返回值会变成model模型的返回值，key是标记的名
  [!photo](http://img.blog.csdn.net/20170226004759235?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSGFycnlfWkhfV2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast0)
  
  # @ModelAttribute标记在参数前
  从from表单或url地址中取值，这里就以url地址为例，为了避免url地址中文乱码问题，这里调用了encodeURL函数
  ```java
  <script type="text/javascript">
        $(function(){
            $("#modelTest").on("click",function(){

                window.location.href="<%=basePath%>model/modelTest.do?userName="+encodeURI('小明')+"&sex="+encodeURI('男');
            })
        });
  </script>
  <body>

    <input type="button" id="modelTest" value="测试">
    <input type="text" value="${pojo.userName }">
    <input type="text" value="${pojo.sex }">

  </body>
  ```
  
  ```java
  @Controller
@RequestMapping(value="model")
public class ModelAttributeTest {

    @RequestMapping(value="modelTest.do")
    public String modelTest(@ModelAttribute("pojo") PojoTest pojo) 
    {
        try {
            pojo.setUserName(new String(pojo.getUserName().getBytes("iso-8859-1"),"utf-8"));
            pojo.setSex(new String(pojo.getSex().getBytes("iso-8859-1"),"utf-8"));
        } catch (UnsupportedEncodingException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        System.out.println(pojo);
        return "modelTest";
    }

}
```
点击页面测试，页面文本框会显示URL地址传递过来的参数，因为SpringMVC会自动匹匹配页面传递过来的参数的name属性和后台控制器中的方法中的参数名，如果参数名相同，会自动匹配，如果控制器中方法是封装的bean,会自动匹配bean中的属性，其实这种取值方式不需要用@ModelAttribute注解，只要满足匹配要求，也能拿得到值
[!photo](http://img.blog.csdn.net/20170226011711946?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSGFycnlfWkhfV2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
从model对象中取值
```java
@Controller
@RequestMapping(value="model")
public class ModelAttributeTest {

    @ModelAttribute("pojo")
    public PojoTest init( PojoTest pojo)
    {
        pojo.setSex("男");
        return pojo;

    }

    @RequestMapping(value="modelTest.do")
    public String modelTest(@ModelAttribute("pojo") PojoTest pojo) 
    {
        pojo.setUserName("小明");
        return "modelTest";
    }

}
```
点击测试发现，modelTest拿到inint方法中的pojo对象，合并两次set的参数后返回页面
[!photo](http://img.blog.csdn.net/20170226023308348?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSGFycnlfWkhfV2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
