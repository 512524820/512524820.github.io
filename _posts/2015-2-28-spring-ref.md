---
layout: post
title: spring中的ref用法
date: 2018-2-28
categories: blog
tags: [技术,spring,java]
description: 。
---

ref元素是用在property中，来设置需要引用的容器管理的其它Bean。
 
   它的用法：<ref  bean|local|parent="someBean">,这里主要分析一下这三个参数的作用。
 
   这次先看实例，再进行讲解。
 
·  先建立一个包：javamxj.spring.basic.ref  ，然后把以下5个文件放在这个包下。
HelloBean.java  
```java
package javamxj.spring.basic.ref;  
  
public class HelloBean {  
    private String hello;  
  
    public String getHello() {  
        return hello;  
    }  
  
    public void setHello(String hello) {  
        this.hello = hello;  
    }  
}  
```
HelloDate.java  
```java
package javamxj.spring.basic.ref;  
  
import java.util.Date;  
  
public class HelloDate {  
    private Date date;  
  
    private HelloBean hb;  
  
    public void setDate(Date date) {  
        this.date = date;  
    }  
  
    public void setHb(HelloBean hb) {  
        this.hb = hb;  
    }  
  
    public void sayHello() {  
        System.out.println(hb.getHello() + "  " + date.toLocaleString());  
    }  
  
}   
```
beans.xml
```java
<?xml version="1.0" encoding="GBK"?>   
<!DOCTYPE beans public "-//SPRING//DTD BEAN//EN" "http://www.springframework.org/dtd/spring-beans.dtd">  
<beans>  
      
    <bean id="helloBean" class="javamxj.spring.basic.ref.HelloBean">  
        <property name="hello" value="Hello! Child Bean." />  
    </bean>  
      
    <bean id="dateBean" name="#date" class="java.util.Date"/>  
      
    <bean id="hd1" class="javamxj.spring.basic.ref.HelloDate">  
        <property name="hb">  
            <ref bean="helloBeanParent"/>  
        </property>  
        <property name="date">  
            <ref bean="#date"/>  
            <!--<ref bean="dateBean"/>-->  
        </property>  
    </bean>  
      
    <bean id="hd2" class="javamxj.spring.basic.ref.HelloDate">  
        <property name="hb">  
            <ref local="helloBean"/>  
        </property>  
        <property name="date">  
            <ref local="dateBean"/>  
        </property>  
    </bean>  
      
    <bean id="hd3" class="javamxj.spring.basic.ref.HelloDate">  
        <property name="hb">  
            <ref parent="helloBean"/>  
        </property>  
        <property name="date">  
            <bean class="java.util.Date"/>  
        </property>  
    </bean>  
      
</beans>  
```

parent.xml  
```java
<?xml version="1.0" encoding="GBK"?>   
<!DOCTYPE beans public "-//SPRING//DTD BEAN//EN" "http://www.springframework.org/dtd/spring-beans.dtd">  
<beans>  
      
    <bean id="helloBean" class="javamxj.spring.basic.ref.HelloBean">  
        <property name="hello" value="Hello! Javamxj." />  
    </bean>  
      
    <bean id="helloBeanParent" class="javamxj.spring.basic.ref.HelloBean">  
        <property name="hello" value="Hello! Parent Bean." />  
    </bean>  
      
</beans>   
```

Main.java 
```java
package javamxj.spring.basic.ref;  
  
import org.springframework.beans.factory.BeanFactory;  
import org.springframework.beans.factory.xml.XmlBeanFactory;  
import org.springframework.core.io.ClassPathResource;  
  
public class Main {  
  
    public static void main(String[] args) {  
        BeanFactory parent = new XmlBeanFactory(new ClassPathResource(  
                "javamxj/spring/basic/ref/parent.xml"));  
        BeanFactory child = new XmlBeanFactory(new ClassPathResource(  
                "javamxj/spring/basic/ref/beans.xml"), parent);  
  
        HelloDate hd1 = (HelloDate) child.getBean("hd1");  
        HelloDate hd2 = (HelloDate) child.getBean("hd2");  
        HelloDate hd3 = (HelloDate) child.getBean("hd3");  
  
        hd1.sayHello();  
        hd2.sayHello();  
        hd3.sayHello();  
    }  
}   
```

运行Main.java，输出结果如下：
```java
Hello!  Parent  Bean.    2005-8-10  22:25:56
Hello!  Child  Bean.    2005-8-10  22:25:56
Hello!  Javamxj.    2005-8-10  22:25:56
```

OK！这里主要分析beans.xml、Main.java这两个文件。对于Main.java要注意的是如何加载“parent”的，重点看看beans.xml中ref元素的用法。
 
   首先定义两个bean：helloBean、dateBean，分别指向HelloBean类和Date类。然后定义了hd1、hd2、hd3等三个bean，都指向HelloDate类。
 
·hd1：
   property标签中的“helloBeanParent”并不存在于beans.xml中，而是在parent.xml中，使用<ref  bean="someBean">可以从其它bean配置文件中寻找需要加载的目标bean。bean属性的值可以同目标bean的id属性相同，也可以同它的name属性中任意的一个值相同。
 
·hd2：
     property标签中的“helloBean”如果不存在于beans.xml中，则XML解析器会提示错误。<ref  local="someBean">只能这个bean配置文件中寻找需要加载的目标bean，而且local属性值必须同目标bean的id属性相同。
 
·hd3：
     property标签中的“helloBean”同时存在于beans.xml和parent.xml中，使用<ref  bean="someBean">则会优先从beans.xml中寻找需要加载的目标bean，如果需要从parent.xml中加载目标bean，则需使用<ref  parent="someBean">。在设置date时，使用的是内联bean，这时可以不要任何id或name定义。
