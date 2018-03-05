---
layout: post
title: Spring之ModelAttribute的运用
date: 2018-3-5
categories: blog
tags: [java,spring,ModelAttribute]
description: 。
---

# 情景1 @ModelAttribute注释一个方法的参数 

1. 从model中获取
```java
@Controller  
    public class HelloWorldController {  
  
        @ModelAttribute("user")  
        public User addAccount() {  
           return new User("jz","123");  
        }  
  
        @RequestMapping(value = "/helloWorld")  
        public String helloWorld(@ModelAttribute("user") User user) {  
           user.setUserName("jizhou");  
           return "helloWorld";  
        }  
    }
```
在这个例子里，@ModelAttribute("user") User user注释方法参数，参数user的值来源于addAccount()方法中的model属性。
此时如果方法体没有标注@SessionAttributes("user")，那么scope为request，如果标注了，那么scope为session

2. 从Form表单或URL参数中获取（实际上，不做此注释也能拿到user对象）
```java
@Controller  
    public class HelloWorldController {  
  
        @RequestMapping(value = "/helloWorld")  
        public String helloWorld(@ModelAttribute User user) {  
           return "helloWorld";  
        }  
    }
```
注意这时候这个User类一定要有没有参数的构造函数。

# 场景2 .@ModelAttribute注释方法
例子（1），（2），（3）类似，被@ModelAttribute注释的方法会在此controller每个方法执行前被执行，因此对于一个controller映射多个URL的用法来说，
要谨慎使用。
1. @ModelAttribute注释void返回值的方法 
```java
@Controller  
    public class HelloWorldController {  
  
        @ModelAttribute  
        public void populateModel(@RequestParam String abc, Model model) {  
           model.addAttribute("attributeName", abc);  
        }  
  
        @RequestMapping(value = "/helloWorld")  
        public String helloWorld() {  
           return "helloWorld";  
        }  
    }
```
这个例子，在获得请求/helloWorld 后，populateModel方法在helloWorld方法之前先被调用，它把请求参数（/helloWorld?abc=text）加入到一个名为attributeName的model属性中，在它执行后helloWorld被调用，返回视图名helloWorld和model已由@ModelAttribute方法生产好了。
这个例子中model属性名称和model属性对象有model.addAttribute()实现，不过前提是要在方法中加入一个Model类型的参数。

当URL或者post中不包含次参数时，会报错，其实不需要这个方法，完全可以把请求的方法写成，这样缺少此参数也不会出错
```java
@RequestMapping(value = "/helloWorld")  
         public String helloWorld(String abc) {  
            return "helloWorld";  
         }
```
2. @ModelAttribute注释返回具体类的方法 
```java
@ModelAttribute  
    public Account addAccount(@RequestParam String number) {  
        return accountManager.findAccount(number);  
     }  
```
这种情况，model属性的名称没有指定，它由返回类型隐含表示，如这个方法返回Account类型，那么这个model属性的名称是account,
这个例子中model属性名称有返回对象类型隐含表示，model属性对象就是方法的返回值。它无须要特定的参数。

3. @ModelAttribute(value="")注释返回具体类的方法 
```java
@Controller  
    public class HelloWorldController {  
  
        @ModelAttribute("attributeName")  
        public String addAccount(@RequestParam String abc) {  
           return abc;  
        }  
  
        @RequestMapping(value = "/helloWorld")  
        public String helloWorld() {  
           return "helloWorld";  
        }  
    }
```
这个例子中使用@ModelAttribute注释的value属性，来指定model属性的名称。model属性对象就是方法的返回值。它无须要特定的参数。
   
4. @ModelAttribute和@RequestMapping同时注释一个方法
```java
@Controller  
    public class HelloWorldController {  
  
        @RequestMapping(value = "/helloWorld.do")  
        @ModelAttribute("attributeName")  
        public String helloWorld() {  
           return "hi";  
        }  
    }
  ```
  这时这个方法的返回值并不是表示一个视图名称，而是model属性的值，视图名称由RequestToViewNameTranslator根据请求"/helloWorld.do"转换为逻辑视图helloWorld。
  Model属性名称有@ModelAttribute(value=””)指定，相当于在request中封装了key=attributeName，value=hi。
