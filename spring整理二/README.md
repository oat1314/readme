转载:http://c.biancheng.net/spring/

# Java Spring框架是什么？它有哪些好处？

[Spring](http://c.biancheng.net/spring/) 是另一个主流的 [Java](http://c.biancheng.net/java/) Web 开发框架，该框架是一个轻量级的应用框架，具有很高的凝聚力和吸引力。Spring 框架因其强大的功能以及卓越的性能而受到众多开发人员的喜爱。

Spring 是分层的 Java SE/EE full-stack 轻量级开源框架，以 IoC（Inverse of Control，控制反转）和 AOP（Aspect Oriented Programming，面向切面编程）为内核，使用基本的 JavaBean 完成以前只可能由 EJB 完成的工作，取代了 EJB 臃肿和低效的开发模式。

在实际开发中，通常服务器端采用三层体系架构，分别为表现层（web）、业务逻辑层（service）、持久层（dao）。

Spring 对每一层都提供了技术支持，在表现层提供了与 [Struts2](http://c.biancheng.net/struts2/) 框架的整合，在业务逻辑层可以管理事务和记录日志等，在持久层可以整合 [Hibernate](http://c.biancheng.net/hibernate/) 和 JdbcTemplate 等技术。

从设计上看，Spring 框架给予了 Java 程序员更高的自由度，对业界的常见问题也提供了良好的解决方案，因此，在开源社区受到了广泛的欢迎，并且被大部分公司作为 Java 项目开发的首选框架。

Spring 具有简单、可测试和松耦合等特点，不仅可以用于服务器端的开发，也可以应用于任何 Java 应用的开发中。Spring 框架的主要优点具体如下。

#### 1）方便解耦，简化开发

Spring 就是一个大工厂，可以将所有对象的创建和依赖关系的维护交给 Spring 管理。

#### 2）方便集成各种优秀框架

Spring 不排斥各种优秀的开源框架，其内部提供了对各种优秀框架（如 Struts2、Hibernate、MyBatis 等）的直接支持。

#### 3）降低 Java EE API 的使用难度

Spring 对 Java EE 开发中非常难用的一些 API（JDBC、JavaMail、远程调用等）都提供了封装，使这些 API 应用的难度大大降低。

#### 4）方便程序的测试

Spring 支持 JUnit4，可以通过注解方便地测试 Spring 程序。

#### 5）AOP 编程的支持

Spring 提供面向切面编程，可以方便地实现对程序进行权限拦截和运行监控等功能。

#### 6）声明式事务的支持

只需要通过配置就可以完成对事务的管理，而无须手动编程。



# Spring体系结构详解

[Spring](http://c.biancheng.net/spring/) 框架采用分层架构，根据不同的功能被划分成了多个模块，这些模块大体可分为 Data Access/Integration、Web、AOP、Aspects、Messaging、Instrumentation、Core Container 和 Test，如图 1 所示。

![Spring的体系结构](http://c.biancheng.net/uploads/allimg/190606/5-1Z606104H1294.gif)
图 1 Spring的体系结构


图 1 中包含了 Spring 框架的所有模块，这些模块可以满足一切企业级应用开发的需求，在开发过程中可以根据需求有选择性地使用所需要的模块。下面分别对这些模块的作用进行简单介绍。

#### 1. Data Access/Integration（数据访问／集成）

数据访问/集成层包括 JDBC、ORM、OXM、JMS 和 Transactions 模块，具体介绍如下。

- JDBC 模块：提供了一个 JDBC 的抽象层，大幅度减少了在开发过程中对数据库操作的编码。
- ORM 模块：对流行的对象关系映射 API，包括 JPA、JDO、[Hibernate](http://c.biancheng.net/hibernate/) 和 iBatis 提供了的集成层。
- OXM 模块：提供了一个支持对象/XML 映射的抽象层实现，如 JAXB、Castor、XMLBeans、JiBX 和 XStream。
- JMS 模块：指 [Java](http://c.biancheng.net/java/) 消息服务，包含的功能为生产和消费的信息。
- Transactions 事务模块：支持编程和声明式事务管理实现特殊接口类，并为所有的 POJO。

#### 2. Web 模块

Spring 的 Web 层包括 Web、[Servlet](http://c.biancheng.net/servlet/)、Struts 和 Portlet 组件，具体介绍如下。

- Web 模块：提供了基本的 Web 开发集成特性，例如多文件上传功能、使用的 Servlet 监听器的 IoC 容器初始化以及 Web 应用上下文。
- Servlet模块：包括 Spring 模型—视图—控制器（MVC）实现 Web 应用程序。
- Struts 模块：包含支持类内的 Spring 应用程序，集成了经典的 Struts Web 层。
- Portlet 模块：提供了在 Portlet 环境中使用 MV C实现，类似 Web-Servlet 模块的功能。

#### 3. Core Container（核心容器）

Spring 的核心容器是其他模块建立的基础，由 Beans 模块、Core 核心模块、Context 上下文模块和 Expression Language 表达式语言模块组成，具体介绍如下。

- Beans 模块：提供了 BeanFactory，是工厂模式的经典实现，Spring 将管理对象称为 Bean。
- Core 核心模块：提供了 Spring 框架的基本组成部分，包括 IoC 和 DI 功能。
- Context 上下文模块：建立在核心和 Beans 模块的基础之上，它是访问定义和配置任何对象的媒介。ApplicationContext 接口是上下文模块的焦点。
- Expression Language 模块：是运行时查询和操作对象图的强大的表达式语言。

#### 4. 其他模块

Spring的其他模块还有 AOP、Aspects、Instrumentation 以及 Test 模块，具体介绍如下。

- AOP 模块：提供了面向切面编程实现，允许定义方法拦截器和切入点，将代码按照功能进行分离，以降低耦合性。
- Aspects 模块：提供与 AspectJ 的集成，是一个功能强大且成熟的面向切面编程（AOP）框架。
- Instrumentation 模块：提供了类工具的支持和类加载器的实现，可以在特定的应用服务器中使用。
- Test 模块：支持 Spring 组件，使用 JUnit 或 TestNG 框架的测试。



# Spring目录结构和基础JAR包介绍

目前 [Spring](http://c.biancheng.net/spring/) 框架的最新版本是 5.1.8，本教程是基于 Spring 的稳定版本 3.2.13 进行讲解的。读者可以通过网址 [http://repo.spring.io/simple/libs-release-local/org/springframework/spring/](http://repo.spring.io/simple/libs-release-local/org/springframework/spring/3.2.2.RELEASE/) 下载名称为 springframework-3.2.13.RELEASE-dist.zip 的压缩包。在浏览器的地址栏中输入此下载地址后，浏览器的访问结果如图 1 所示。



![访问结果](http://c.biancheng.net/uploads/allimg/190628/5-1Z62Q03123546.png)
图 1 访问结果


从图 1 中找到所需要的 Spring 框架压缩包。单击此链接下载，下载完成后，解压文件的目录结构如图 2 所示。



![解压后目录](http://c.biancheng.net/uploads/allimg/190628/5-1Z62Q0343N18.png)
图 2 解压后目录


下面对图 2 所示的目录进行简单介绍，具体如表 1 所示。

| 名称   | 作用                                                         |
| ------ | ------------------------------------------------------------ |
| docs   | 包含 Spring 的 API 文档和开发规范                            |
| libs   | 包含开发需要的 JAR 包和源码包                                |
| schema | 包含开发所需要的 schema 文件，在这些文件中定义了 Spring 相关配置文件的约束 |


在 libs 目录中，包含了 Spring 框架提供的所有 JAR 文件，其中有四个 JAR 文件是 Spring 框架的基础包，分别对应 Spring 容器的四个模块，具体如表 2 所示。



| 名称                                 | 作用                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| spring-core-3.2.13.RELEASE.jar       | 包含 Spring 框架基本的核心工具类，Spring 其他组件都要用到这个包中的类，是其他组件的基本核心。 |
| spring-beans-3.2.13.RELEASE.jar      | 所有应用都要用到的，它包含访问配置文件、创建和管理 bean 以及进行 Inversion of Control（IoC）或者 Dependency Injection（DI）操作相关的所有类。 |
| spring-context-3.2.13.RELEASE.jar    | Spring 提供在基础 IoC 功能上的扩展服务，此外还提供许多企业级服务的支持，如邮件服务、任务调度、JNDI 定位、EJB 集成、远程访问、缓存以及各种视图层框架的封装等 |
| spring-expression-3.2.13.RELEASE.jar | 定义了 Spring 的表达式语言。 需要注意的是，在使用 Spring 开发时，除了 Spring 自带的 JAR 包以外，还需要一个第三方 JAR 包 commons.logging 处理日志信息 |


读者可以通过网址 http://commons.apache.org/proper/commons-logging/download_logging.cgi 下载。该 JAR 包现在最新版本为 commons-logging.1.2，下载完成后，解压即可找到。

使用 Spring 框架时，只需将 Spring 的四个基础包以及 commons-logging-1.2.jar 包复制到项目的 lib 目录，并发布到类路径中即可。



# Spring IoC容器：BeanFactory和ApplicationContext

在教程前面介绍 [Spring](http://c.biancheng.net/spring/) 框架时，已经提到过 Spring 的 IoC（控制反转）思想，本节来详细介绍一下 Spring 的 Ioc 容器。

IoC 是指在程序开发中，实例的创建不再由调用者管理，而是由 Spring 容器创建。Spring 容器会负责控制程序之间的关系，而不是由程序代码直接控制，因此，控制权由程序代码转移到了 Spring 容器中，控制权发生了反转，这就是 Spring 的 IoC 思想。

Spring 提供了两种 IoC 容器，分别为 BeanFactory 和 ApplicationContext，接下来将针对这两种 IoC 容器进行详细讲解。

## BeanFactory

BeanFactory 是基础类型的 IoC 容器，它由 org.springframework.beans.facytory.BeanFactory 接口定义，并提供了完整的 IoC 服务支持。简单来说，BeanFactory 就是一个管理 Bean 的工厂，它主要负责初始化各种 Bean，并调用它们的生命周期方法。

BeanFactory 接口有多个实现类，最常见的是 org.springframework.beans.factory.xml.XmlBeanFactory，它是根据 XML 配置文件中的定义装配 Bean 的。

创建 BeanFactory 实例时，需要提供 Spring 所管理容器的详细配置信息，这些信息通常采用 XML 文件形式管理。其加载配置信息的代码具体如下所示：

BeanFactory beanFactory = new XmlBeanFactory(new FileSystemResource("D://applicationContext.xml"));

## ApplicationContext

ApplicationContext 是 BeanFactory 的子接口，也被称为应用上下文。该接口的全路径为 org.springframework.context.ApplicationContext，它不仅提供了 BeanFactory 的所有功能，还添加了对 i18n（国际化）、资源访问、事件传播等方面的良好支持。

ApplicationContext 接口有两个常用的实现类，具体如下。

#### 1）ClassPathXmlApplicationContext

该类从类路径 ClassPath 中寻找指定的 XML 配置文件，找到并装载完成 ApplicationContext 的实例化工作，具体如下所示。

ApplicationContext applicationContext = new ClassPathXmlApplicationContext(String configLocation);

在上述代码中，configLocation 参数用于指定 Spring 配置文件的名称和位置，如 applicationContext.xml。

#### 2）FileSystemXmlApplicationContext

该类从指定的文件系统路径中寻找指定的 XML 配置文件，找到并装载完成 ApplicationContext 的实例化工作，具体如下所示。

ApplicationContext applicationContext = new FileSystemXmlApplicationContext(String configLocation);

它与 ClassPathXmlApplicationContext 的区别是：在读取 Spring 的配置文件时，FileSystemXmlApplicationContext 不再从类路径中读取配置文件，而是通过参数指定配置文件的位置，它可以获取类路径之外的资源，如“F：/workspaces/applicationContext.xml”。

在使用 Spring 框架时，可以通过实例化其中任何一个类创建 Spring 的 ApplicationContext 容器。

通常在 [Java](http://c.biancheng.net/java/) 项目中，会采用通过 ClassPathXmlApplicationContext 类实例化 ApplicationContext 容器的方式，而在 Web 项目中，ApplicationContext 容器的实例化工作会交由 Web 服务器完成。Web 服务器实例化 ApplicationContext 容器通常使用基于 ContextLoaderListener 实现的方式，它只需要在 web.xml 中添加如下代码：

```
<!--指定Spring配置文件的位置，有多个配置文件时，以逗号分隔-->
<context-param>   
    <param-name>contextConfigLocation</param-name>    
    <!--spring将加载spring目录下的applicationContext.xml文件-->    
    <param-value>        
        classpath:spring/applicationContext.xml    
    </param-value>
</context-param>
<!--指定以ContextLoaderListener方式启动Spring容器-->
<listener>    
    <listener-class>        
        org.springframework.web.context.ContextLoaderListener    
    </listener-class>
</listener>
```

需要注意的是，BeanFactory 和 ApplicationContext 都是通过 XML 配置文件加载 Bean 的。

二者的主要区别在于，如果 Bean 的某一个属性没有注入，则使用 BeanFacotry 加载后，在第一次调用 getBean() 方法时会抛出异常，而 ApplicationContext 则在初始化时自检，这样有利于检查所依赖的属性是否注入。

因此，在实际开发中，通常都选择使用 ApplicationContext，而只有在系统资源较少时，才考虑使用 BeanFactory。本教程中使用的就是 ApplicationContext。



# 第一个Spring程序

通过《[Spring IoC容器](http://c.biancheng.net/view/4248.html)》的学习，读者对 [Spring](http://c.biancheng.net/spring/) 的 IoC 容器已经有了一个初步的了解。下面通过具体的案例演示 IoC 容器的使用。

#### 1. 创建项目

在 MyEclipse 中创建 Web 项目 springDemo01，将 Spring 框架所需的 JAR 包复制到项目的 lib 目录中，并将添加到类路径下，添加后的项目如图 1 所示。



![Spring所需的JAR包](http://c.biancheng.net/uploads/allimg/190628/5-1Z62Q231051P.png)
图 1 Spring所需的JAR包

#### 2. 创建 PersonDao 接口

在项目的 src 目录下创建一个名为 com.mengma.ioc 的包，然后在该包中创建一个名为 PersonDao 的接口，并在接口中添加一个 add() 方法，如下所示。

```
package com.mengma.ioc;
public interface PersonDao {    
	public void add();
}
```

#### 3. 创建接口实现类 PersonDaoImpl

在 com.mengma.ioc 包下创建 PersonDao 的实现类 PersonDaoImpl，编辑后如下所示。

```
package com.mengma.ioc;public class PersonDaoImpl implements PersonDao {    
@Override    
public void add() {        
System.out.println("save()执行了...");    
}}
```

上述代码中，PersonDaoImpl 类实现了 PersonDao 接口中的 add() 方法，并且在方法调用时会执行输出语句。

#### 4. 创建 Spring 配置文件

在 src 目录下创建 Spring 的核心配置文件 applicationContext.xml，编辑后如下所示。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"    
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:p="http://www.springframework.org/schema/p"    
    xsi:schemaLocation="http://www.springframework.org/schema/beans    
		http://www.springframework.org/schema/beans/spring-beans-3.2.xsd">    
		<!-- 由 Spring容器创建该类的实例对象 -->    
		<bean id="personDao" class="com.mengma.ioc.PersonDaoImpl" />
</beans>
```

上述代码中，第 2～5 行代码是 Spring 的约束配置，第 7 行代码表示在 Spring 容器中创建一个 id 为 personDao 的 bean 实例，其中 id 表示文件中的唯一标识符，class 属性表示指定需要实例化 Bean 的实全限定类名（包名+类名）。

需要注意的是，Spring 的配置文件名称是可以自定义的，通常情况下，都会将配置文件命名为 applicationContext.xml（或 bean.xml）。

#### 5. 编写测试类

在 com.mengma.ioc 包下创建测试类 FirstTest，并在该类中添加一个名为 test1() 的方法，编辑后如下所示。

```
package com.mengma.ioc;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class FirstTest {    
@Test    
public void testl() {        
    // 定义Spring配置文件的路径        
    String xmlPath = "applicationContext.xml";        
    // 初始化Spring容器，加载配置文件        
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);        
    // 通过容器获取personDao实例        
    PersonDao personDao = (PersonDao) applicationContext.getBean("personDao");        
    // 调用 personDao 的 add ()方法       
    personDao.add();    
}}
```

上述代码中，首先定义了 Spring 配置文件的路径，然后创建 Spring 容器，接下来通过 Spring 容器获取了 personDao 实例，最后调用实例的 save() 方法。

#### 6. 运行项目并查看结果

使用 JUnit 测试运行 test1() 方法，运行成功后，控制台的输出结果如图 2 所示。

从图 2 的输出结果中可以看出，程序已经成功输出了“save()执行了...”语句。在程序执行时，对象的创建并不是通过 new 一个类完成的，而是由 Spring 容器管理实现的。这就是 Spring IoC 容器思想的工作机制。



![输出结果](http://c.biancheng.net/uploads/allimg/190628/5-1Z62Q32202P7.png)
图 2 输出结果



# Spring DI（依赖注入）的实现方式：属性注入和构造注入

依赖注入（Dependency Injection，DI）和控制反转含义相同，它们是从两个角度描述的同一个概念。

当某个 [Java](http://c.biancheng.net/java/) 实例需要另一个 Java 实例时，传统的方法是由调用者创建被调用者的实例（例如，使用 new 关键字获得被调用者实例），而使用 [Spring](http://c.biancheng.net/spring/) 框架后，被调用者的实例不再由调用者创建，而是由 Spring 容器创建，这称为控制反转。

Spring 容器在创建被调用者的实例时，会自动将调用者需要的对象实例注入给调用者，这样，调用者通过 Spring 容器获得被调用者实例，这称为依赖注入。

依赖注入主要有两种实现方式，分别是属性 setter 注入和构造方法注入。具体介绍如下。

#### 1）属性 setter 注入

指 IoC 容器使用 setter 方法注入被依赖的实例。通过调用无参构造器或无参 static 工厂方法实例化 bean 后，调用该 bean 的 setter 方法，即可实现基于 setter 的 DI。

#### 2）构造方法注入

指 IoC 容器使用构造方法注入被依赖的实例。基于构造器的 DI 通过调用带参数的构造方法实现，每个参数代表一个依赖。

下面通过属性 setter 注入的案例演示 Spring 容器是如何实现依赖注入的。具体步骤如下。

#### 1. 创建 PersonService 接口

在 springDemo01 项目的 com.mengma.ioc 包下创建一个名为 PersonService 的接口，该接口中包含一个 addPerson() 方法，如下所示。

```
package com.mengma.ioc;public interface PersonService {    public void addPerson();}
```

#### 2. 创建接口实现类 PersonServiceImpl

在 com.mengma.ioc 包下创建一个名为 PersonServiceImpl 的类，该类实现了 PersonService 接口，如下所示。

```
package com.mengma.ioc;
public class PersonServiceImpl implements PersonService {    
    // 定义接口声明    
    private PersonDao personDao;    
    
    // 提供set()方法，用于依赖注入    
    public void setPersonDao(PersonDao personDao) {        this.personDao = personDao;    }    
    
    // 实现PersonService接口的方法    
    @Override    
    public void addPerson() {        
        personDao.add(); 
        // 调用PersonDao中的add()方法        
        System.out.println("addPerson()执行了...");    
    }
}
```

上述代码中，首先声明了 personDao 对象，并为其添加 setter 方法，用于依赖注入，然后实现了 PersonDao 接口的 addPerson() 方法，并在方法中调用 save() 方法和输出一条语句。

#### 3. 在 applicationContext.xml 中添加配置信息

在 applicationContext.xml 配置文件中添加一个 <bean> 元素，用于实例化 PersonServiceImpl 类，并将 personDao 的实例注入到 personService 中，其实现代码如下所示：

```
<bean id="personService" class="com.mengma.ioc.PersonServiceImpl">    
    <!-- 将personDao实例注入personService实例中 -->    
    <property name="personDao" ref="personDao"/>
</bean>
```

#### 4. 编写测试方法

在 FirstTest 类中创建一个名为 test2() 的方法，编辑后如下所示：

```
@Test
public void test2() {    
    // 定义Spring配置文件的路径    
    String xmlPath = "applicationContext.xml";    
    // 初始化Spring容器，加载配置文件    
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);    
    // 通过容器获取personService实例    
    PersonService personService = (PersonService) applicationContext.getBean("personService");    
    // 调用personService的addPerson()方法    
    personService.addPerson();
}
```

#### 5. 运行项目并查看结果

使用 JUnit 测试运行 test2() 方法，运行成功后，控制台的输出结果如图 1 所示。



![运行结果](http://c.biancheng.net/uploads/allimg/190628/5-1Z62Q41044449.png)
图 1 运行结果


从图 1 的输出结果中可以看出，使用 Spring 容器获取 userService 的实例后，调用了该实例的 addPerson() 方法，在该方法中又调用了 PersonDao 实现类中的 add() 方法，并输出了结果。这就是 Spring 容器属性 setter 注入的方式，也是实际开发中较为常用的一种方式。



# 什么是IOC(控制反转)、DI(依赖注入)

原文地址（摘要了部分内容）：https://blog.csdn.net/qq_22654611/article/details/52606960/

https://blog.csdn.net/qq_42709262/article/details/81951402

学习过Spring框架的人一定都会听过Spring的IoC(控制反转) 、DI(依赖注入)这两个概念，对于初学Spring的人来说，总觉得IoC 、DI这两个概念是模糊不清的，是很难理解的，今天和大家分享网上的一些技术大牛们对Spring框架的IOC的理解以及谈谈我对Spring Ioc的理解。

## 一、分享Iteye的开涛对Ioc的精彩讲解

　　首先要分享的是Iteye的开涛这位技术牛人对Spring框架的IOC的理解，写得非常通俗易懂，以下内容全部来自原文，原文地址：http://jinnianshilongnian.iteye.com/blog/1413846

### 1.1、IoC是什么

　　**Ioc—Inversion of Control，即“控制反转”，不是什么技术，而是一种设计思想。**在Java开发中，**Ioc意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。**如何理解好Ioc呢？理解好Ioc的关键是要明确“谁控制谁，控制什么，为何是反转（有反转就应该有正转了），哪些方面反转了”，那我们来深入分析一下：

　　●**谁控制谁，控制什么：**传统Java SE程序设计，我们直接在对象内部通过new进行创建对象，是程序主动去创建依赖对象；而IoC是有专门一个容器来创建这些对象，即由Ioc容器来控制对 象的创建；**谁控制谁？当然是IoC 容器控制了对象；控制什么？那就是主要控制了外部资源获取（不只是对象包括比如文件等）。**

　　●**为何是反转，哪些方面反转了：**有反转就有正转，传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，也就是正转；而反转则是由容器来帮忙创建及注入依赖对象；为何是反转？**因为由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象，所以是反转；哪些方面反转了？依赖对象的获取被反转了。**

　　用图例说明一下，传统程序设计如图2-1，都是主动去创建相关对象然后再组合起来：

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2Jsb2cvMjg5MjMzLzIwMTUwMS8yNjE0MjEzNzgzMTgyOTIuanBn)

图1-1 传统应用程序示意图

　　当有了IoC/DI的容器后，在客户端类中不再主动去创建这些对象了，如图2-2所示:

![img](https://img-blog.csdn.net/20180822212803580?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNzA5MjYy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

图1-2有IoC/DI容器后程序结构示意图

### 1.2、IoC能做什么

　　IoC 不是一种技术，只是一种思想，一个重要的面向对象编程的法则，它能指导我们如何设计出松耦合、更优良的程序。传统应用程序都是由我们在类内部主动创建依赖对象，从而导致类与类之间高耦合，难于测试；有了IoC容器后，把创建和查找依赖对象的控制权交给了容器，由容器进行注入组合对象，所以对象与对象之间是 松散耦合，这样也方便测试，利于功能复用，更重要的是使得程序的整个体系结构变得非常灵活。

　　其实**IoC对编程带来的最大改变不是从代码上，而是从思想上，发生了“主从换位”的变化。应用程序原本是老大，要获取什么资源都是主动出击，但是在IoC/DI思想中，应用程序就变成被动的了，被动的等待IoC容器来创建并注入它所需要的资源了。**

　　**IoC很好的体现了面向对象设计法则之一—— 好莱坞法则：“别找我们，我们找你”；即由IoC容器帮对象找相应的依赖对象并注入，而不是由对象主动去找。**

### 1.3、IoC和DI

　　**DI—Dependency Injection，即“依赖注入”**：**组件之间依赖关系**由容器在运行期决定，形象的说，即**由容器动态的将某个依赖关系注入到组件之中**。**依赖注入的目的并非为软件系统带来更多功能，而是为了提升组件重用的频率，并为系统搭建一个灵活、可扩展的平台。**通过依赖注入机制，我们只需要通过简单的配置，而无需任何代码就可指定目标需要的资源，完成自身的业务逻辑，而不需要关心具体的资源来自何处，由谁实现。

　　理解DI的关键是：“谁依赖谁，为什么需要依赖，谁注入谁，注入了什么”，那我们来深入分析一下：

　　●**谁依赖于谁：**当然是**应用程序依赖于IoC容器**；

　　●**为什么需要依赖：****应用程序需要IoC容器来提供对象需要的外部资源**；

　　●**谁注入谁：**很明显是**IoC容器注入应用程序某个对象，应用程序依赖的对象**；

　　**●注入了什么：**就是**注入某个对象所需要的外部资源（包括对象、资源、常量数据）**。

　　**IoC和DI**由什么**关系**呢？其实它们**是同一个概念的不同角度描述**，由于控制反转概念比较含糊（可能只是理解为容器控制对象这一个层面，很难让人想到谁来维护对象关系），所以2004年大师级人物Martin Fowler又给出了一个新的名字：“依赖注入”，相对IoC 而言，**“****依赖注入”****明确描述了“被注入对象依赖IoC****容器配置依赖对象”。**

　　看过很多对Spring的Ioc理解的文章，好多人对Ioc和DI的解释都晦涩难懂，反正就是一种说不清，道不明的感觉，读完之后依然是一头雾水，感觉就是开涛这位技术牛人写得特别通俗易懂，他清楚地解释了IoC(控制反转) 和DI(依赖注入)中的每一个字，读完之后给人一种豁然开朗的感觉。我相信对于初学Spring框架的人对Ioc的理解应该是有很大帮助的。

## 二、分享Bromon的blog上对IoC与DI浅显易懂的讲解

### 2.1、IoC(控制反转)

　　首先想说说**IoC（Inversion of Control，控制反转）**。这是**spring的核心**，贯穿始终。**所谓IoC，对于spring框架来说，就是由spring来负责控制对象的生命周期和对象间的关系。**这是什么意思呢，举个简单的例子，我们是如何找女朋友的？常见的情况是，我们到处去看哪里有长得漂亮身材又好的mm，然后打听她们的兴趣爱好、qq号、电话号、ip号、iq号………，想办法认识她们，投其所好送其所要，然后嘿嘿……这个过程是复杂深奥的，我们必须自己设计和面对每个环节。传统的程序开发也是如此，在一个对象中，如果要使用另外的对象，就必须得到它（自己new一个，或者从JNDI中查询一个），使用完之后还要将对象销毁（比如Connection等），对象始终会和其他的接口或类藕合起来。

　　那么IoC是如何做的呢？有点像通过婚介找女朋友，在我和女朋友之间引入了一个第三者：婚姻介绍所。婚介管理了很多男男女女的资料，我可以向婚介提出一个列表，告诉它我想找个什么样的女朋友，比如长得像李嘉欣，身材像林熙雷，唱歌像周杰伦，速度像卡洛斯，技术像齐达内之类的，然后婚介就会按照我们的要求，提供一个mm，我们只需要去和她谈恋爱、结婚就行了。简单明了，如果婚介给我们的人选不符合要求，我们就会抛出异常。整个过程不再由我自己控制，而是有婚介这样一个类似容器的机构来控制。**Spring所倡导的开发方式**就是如此，**所有的类都会在spring容器中登记，告诉spring你是个什么东西，你需要什么东西，然后spring会在系统运行到适当的时候，把你要的东西主动给你，同时也把你交给其他需要你的东西。所有的类的创建、销毁都由 spring来控制，也就是说控制对象生存周期的不再是引用它的对象，而是spring。对于某个具体的对象而言，以前是它控制其他对象，现在是所有对象都被spring控制，所以这叫控制反转。**

### 2.2、DI(依赖注入)

　　**IoC的一个重点是在系统运行中，动态的向某个对象提供它所需要的其他对象。这一点是通过DI（Dependency Injection，依赖注入）来实现的**。比如对象A需要操作数据库，以前我们总是要在A中自己编写代码来获得一个Connection对象，有了 spring我们就只需要告诉spring，A中需要一个Connection，至于这个Connection怎么构造，何时构造，A不需要知道。在系统运行时，spring会在适当的时候制造一个Connection，然后像打针一样，注射到A当中，这样就完成了对各个对象之间关系的控制。A需要依赖 Connection才能正常运行，而这个Connection是由spring注入到A中的，依赖注入的名字就这么来的。那么DI是如何实现的呢？ Java 1.3之后一个重要特征是反射（reflection），它允许程序在运行的时候动态的生成对象、执行对象的方法、改变对象的属性，spring就是通过反射来实现注入的。

　　理解了IoC和DI的概念后，一切都将变得简单明了，剩下的工作只是在spring的框架中堆积木而已。



# Spring Bean的配置及常用属性

作为 [Spring](http://c.biancheng.net/spring/) 核心机制的依赖注入，改变了传统的编程习惯，对组件的实例化不再由应用程序完成，转而交由 Spring 容器完成，在需要时注入应用程序中，从而对组件之间依赖关系进行了解耦。这一切都离不开 Spring 配置文件中使用的 <bean> 元素。

Spring 容器可以被看作一个大工厂，而 Spring 容器中的 Bean 就相当于该工厂的产品。如果希望这个大工厂能够生产和管理 Bean，这时则需要告诉容器需要哪些 Bean，以及需要以何种方式将这些 Bean 装配到一起。

Spring 配置文件支持两种不同的格式，分别是 XML 文件格式和 Properties 文件格式。

通常情况下，Spring 会以 XML 文件格式作为 Spring 的配置文件，这种配置方式通过 XML 文件注册并管理 Bean 之间的依赖关系。

XML 格式配置文件的根元素是 <beans>，该元素包含了多个 <bean> 子元素，每一个 <bean> 子元素定义了一个 Bean，并描述了该 Bean 如何被装配到 Spring 容器中。

定义 Bean 的示例代码如下所示：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"    
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:p="http://www.springframework.org/schema/p"    
    xsi:schemaLocation="http://www.springframework.org/schema/beans    
    http://www.springframework.org/schema/beans/spring-beans-3.2.xsd">    
    <!-- 使用id属性定义person1，其对应的实现类为com.mengma.person1 -->    
    <bean id="person1" class="com.mengma.damain.Person1" />    
    <!--使用name属性定义person2，其对应的实现类为com.mengma.domain.Person2-->    
    <bean name="Person2" class="com.mengma.domain.Person2"/>
</beans>
```

在上述代码中，分别使用 id 和 name 属性定义了两个 Bean，并使用 class 元素指定了 Bean 对应的实现类。

<bean> 元素中包含很多属性，其常用属性如表 1 所示。

| 属性名称        | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| id              | 是一个 Bean 的唯一标识符，Spring 容器对 Bean 的配置和管理都通过该属性完成 |
| name            | Spring 容器同样可以通过此属性对容器中的 Bean 进行配置和管理，name 属性中可以为 Bean 指定多个名称，每个名称之间用逗号或分号隔开 |
| class           | 该属性指定了 Bean 的具体实现类，它必须是一个完整的类名，使用类的全限定名 |
| scope           | 用于设定 Bean 实例的作用域，其属性值有 singleton（单例）、prototype（原型）、request、session 和 global Session。其默认值是 singleton |
| constructor-arg | <bean>元素的子元素，可以使用此元素传入构造参数进行实例化。该元素的 index 属性指定构造参数的序号（从 0 开始），type 属性指定构造参数的类型 |
| property        | <bean>元素的子元素，用于调用 Bean 实例中的 Set 方法完成属性赋值，从而完成依赖注入。该元素的 name 属性指定 Bean 实例中的相应属性名 |
| ref             | <property> 和 <constructor-arg> 等元素的子元索，该元素中的 bean 属性用于指定对 Bean 工厂中某个 Bean 实例的引用 |
| value           | <property> 和 <constractor-arg> 等元素的子元素，用于直接指定一个常量值 |
| list            | 用于封装 List 或数组类型的依赖注入                           |
| set             | 用于封装 Set 类型属性的依赖注入                              |
| map             | 用于封装 Map 类型属性的依赖注入                              |
| entry           | <map> 元素的子元素，用于设置一个键值对。其 key 属性指定字符串类型的键值，ref 或 value 子元素指定其值 |



# Spring实例化Bean的三种方法



在面向对象的程序中，要想调用某个类的成员方法，就需要先实例化该类的对象。在 [Spring](http://c.biancheng.net/spring/) 中，实例化 Bean 有三种方式，分别是构造器实例化、静态工厂方式实例化和实例工厂方式实例化。本节将针对这三种方式分别进行讲解。

## 构造器实例化

构造器实例化是指 Spring 容器通过 Bean 对应的类中默认的构造函数实例化 Bean。下面通过案例演示如何使用构造器实例化 Bean。

#### 1. 创建项目并导入 JAR 包

在 MyEclipse 中创建一个名称为 springDemo02 的 Web 项目，然后将 Spring 支持和依赖的 JAR 包复制到项目的 lib 目录中，并发布到类路径下。

#### 2. 创建实体类

在项目的 src 目录下创建一个名为 com.mengma.instance.constructor 的包，在该包下创建一个实体类 Person1，如下所示。

```
package com.mengma.instance.constructor;
public class Person1 {}
```

#### 3. 创建 Spring 配置文件

在 com.mengma.instance.constructor 包下创建 Spring 的配置文件 applicationContext.xml，编辑后如下所示。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"    
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:p="http://www.springframework.org/schema/p"    
    xsi:schemaLocation="http://www.springframework.org/schema/beans    
    http://www.springframework.org/schema/beans/spring-beans-3.2.xsd">    
    <bean id="person1" class="com.mengma.instance.constructor.Person1" />
</beans>
```

在上述配置中，定义了一个 id 为 person1 的 Bean，其中 class 属性指定了其对应的类为 Person1。

#### 4. 创建测试类

在 com.mengma.instance.constructor 包下创建一个名为 InstanceTest1 的测试类，编辑后如下所示。

```
package com.mengma.instance.constructor;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class InstanceTest1 {    
@Test    
public void test() {        
    // 定义Spring配置文件的路径        
    String xmlPath = "com/mengma/instance/constructor/ApplicationContext.xml";        
    // 初始化Spring容器，加载配置文件，并对bean进行实例化        
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);        
    // 通过容器获取id为person1的实例        
    System.out.println(applicationContext.getBean("person1"));    
    }
}
```

上述文件中，首先在 test() 方法中定义了 Spring 配置文件的路径，然后 Spring 容器会加载配置文件。在加载的同时，Spring 容器会通过实现类 Person1 中默认的无参构造函数对 Bean 进行实例化。

#### 5. 运行程序并查看结果

使用 JUnit 测试运行 test() 方法，运行成功后，控制台的输出结果如图 1 所示。



![输出结果](http://c.biancheng.net/uploads/allimg/190628/5-1Z62Q55309E3.png)
图 1 输出结果


从图 1 的输出结果中可以看出，Spring 容器已经成功对 Bean 进行了实例化，并输出了结果。

注意：为了方便读者的学习，本节中的所有配置文件和 [Java](http://c.biancheng.net/java/) 文件都根据知识点放置在同一个包中。在实际开发中，为了方便管理和维护，建议将这些文件根据类别放置在不同目录中。

## 静态工厂方式实例化

在 Spring 中，也可以使用静态工厂的方式实例化 Bean。此种方式需要提供一个静态工厂方法创建 Bean 的实例。下面通过案例演示如何使用静态工厂方式实例化 Bean。

#### 1. 创建实体类

在项目的 src 目录下创建一个名为 com.mengma.instance.static_factory 的包，并在该包下创建一个实体类 Person2，该类与 Person1 相同，不需要添加任何成员。

#### 2. 创建静态工厂类

在 com.mengma.instance.static_factory 包下创建一个名为 MyBeanFactory 的类，并在该类中创建一个名为 createBean() 的静态方法，用于创建 Bean 的实例，如下所示。

```
package com.mengma.instance.static_factory;
public class MyBeanFactory {    
// 创建Bean实例的静态工厂方法    
public static Person2 createBean() {       
    return new Person2();    
    }
}
```

#### 3. 创建 Spring 配置文件

在 com.mengma.instance.static_factory 包下创建 Spring 的配置文件 applicationContext.xml，编辑后如下所示。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"    
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:p="http://www.springframework.org/schema/p"    
    xsi:schemaLocation="http://www.springframework.org/schema/beans    
    http://www.springframework.org/schema/beans/spring-beans-3.2.xsd">    
    <bean id="person2" class="com.mengma.instance.static_factory.MyBeanFactory"  factory-method="createBean" />
</beans>
```

上述代码中，定义了一个 id 为 person2 的 Bean，其中 class 属性指定了其对应的工厂实现类为 MyBeanFactory，而 factory-method 属性用于告诉 Spring 容器调用工厂类中的 createBean() 方法获取 Bean 的实例。

#### 4. 创建测试类

在 com.mengma.instance.static_factory 包下创建一个名为 InstanceTest2 的测试类，编辑后如下所示。

```
package com.mengma.instance.static_factory;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class InstanceTest2 {    
@Test    
public void test() {        
    // 定义Spring配置文件的路径        
    String xmlPath = "com/mengma/instance/static_factory/applicationContext.xml"; 
    // 初始化Spring容器，加载配置文件，并对bean进行实例化        
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);        
    // 通过容器获取id为person2实例        
    System.out.println(applicationContext.getBean("person2"));    
    }
}
```

#### 5. 运行程序并查看结果

使用 JUnit 测试运行 test() 方法，运行成功后，控制台的输出结果如图 2 所示。

从图 2 的输出结果中可以看出，使用静态工厂的方式也成功对 Bean 进行了实例化。



![输出结果](http://c.biancheng.net/uploads/allimg/190628/5-1Z62Q6120D55.png)
图 2 输出结果

## 实例工厂方式实例化

在 Spring 中，还有一种实例化 Bean 的方式就是采用实例工厂。在这种方式中，工厂类不再使用静态方法创建 Bean 的实例，而是直接在成员方法中创建 Bean 的实例。

同时，在配置文件中，需要实例化的 Bean 也不是通过 class 属性直接指向其实例化的类，而是通过 factory-bean 属性配置一个实例工厂，然后使用 factory-method 属性确定使用工厂中的哪个方法。下面通过案例演示实例工厂方式的使用。

#### 1. 创建实体类

在项目的 src 目录下创建一个名为 com.mengma.instance.factory 的包，在该包下创建一个 Person3 类，该类与 Person1 类相同，不需要添加任何成员。

#### 2. 创建实例工厂类

在 com.mengma.instance.factory 包下创建一个名为 MyBeanFactory 的类，编辑后如下所示。

```
package com.mengma.instance.factory;
public class MyBeanFactory {  

public MyBeanFactory() {        
System.out.println("person3工厂实例化中");    
}    

// 创建Bean的方法    
public Person3 createBean() {        
    return new Person3();    
    }
}
```

上述代码中，使用默认无参的构造方法输出 person3 工厂实例化中语句，使用 createBean 成员方法创建 Bean 的实例。

#### 3. 创建 Spring 配置文件

在 com.mengma.instance.factory 包下创建 Spring 的配置文件 applicationContext.xml，如下所示。

```
    <?xml version="1.0" encoding="UTF-8"?><beans xmlns="http://www.springframework.org/schema/beans"    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:p="http://www.springframework.org/schema/p"    
    xsi:schemaLocation="http://www.springframework.org/schema/beans    
    http://www.springframework.org/schema/beans/spring-beans-3.2.xsd">    
    <!-- 配置实例工厂 -->    
    <bean id="myBeanFactory" class="com.mengma.instance.factory.MyBeanFactory" />    
    <!-- factory-bean属性指定一个实例工厂，factory-method属性确定使用工厂中的哪个方法 -->    
    <bean id="person3" factory-bean="myBeanFactory" factory-method="createBean" />
</beans>
```

上述代码中，首先配置了一个实例工厂 Bean，然后配置了需要实例化的 Bean。在 id 为 person3 的 Bean 中，使用 factory-bean 属性指定一个实例工厂，该属性值就是实例工厂的 id 属性值。使用 factory-method 属性确定使用工厂中的 createBean() 方法。

#### 4. 创建测试类

在 com.mengma.instance.factory 包下创建一个名为 InstanceTest3 的测试类，编辑后如下所示。

```
package com.mengma.instance.factory;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class InstanceTest3 {   

@Test    
public void test() {        
    // 定义Spring配置文件的路径        
    String xmlPath = "com/mengma/instance/factory/applicationContext.xml"; 
    // 初始化Spring容器，加载配置文件，并对bean进行实例化             
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);        
    // 通过容器获取id为person3实例        
    System.out.println(applicationContext.getBean("person3"));    
    }
}
```

#### 5. 运行程序并查看结果

使用 JUnit 测试运行 test() 方法，运行成功后，控制台的输出结果如图 3 所示。



![输出结果](http://c.biancheng.net/uploads/allimg/190628/5-1Z62Q63022139.png)
图 3 输出结果


从图 3 的输出结果中可以看出，使用实例工厂的方式也同样对 Bean 进行了实例化。



# Spring中Bean的作用域



[Java一对一答疑，帮助有志青年！使用QQ在线辅导，哪里不懂问哪里，整个过程都是一对一，学习更有针对性。和作者直接交流，不但提升技能，还提升 Level；当你决定加入我们，你已然超越了 90% 的程序员。猛击这里了解详情。](http://c.biancheng.net/view/7552.html)

本节先简单介绍了 [Spring](http://c.biancheng.net/spring/) 中 bean 的 5 种作用域，然后详细介绍 singleton 和 prototype 这两种最常用的作用域。

## 作用域的种类

Spring 容器在初始化一个 Bean 的实例时，同时会指定该实例的作用域。Spring3 为 Bean 定义了五种作用域，具体如下。

#### 1）singleton

单例模式，使用 singleton 定义的 Bean 在 Spring 容器中只有一个实例，这也是 Bean 默认的作用域。

#### 2）prototype

原型模式，每次通过 Spring 容器获取 prototype 定义的 Bean 时，容器都将创建一个新的 Bean 实例。

#### 3）request

在一次 HTTP 请求中，容器会返回该 Bean 的同一个实例。而对不同的 HTTP 请求，会返回不同的实例，该作用域仅在当前 HTTP Request 内有效。

#### 4）session

在一次 HTTP Session 中，容器会返回该 Bean 的同一个实例。而对不同的 HTTP 请求，会返回不同的实例，该作用域仅在当前 HTTP Session 内有效。

#### 5）global Session

在一个全局的 HTTP Session 中，容器会返回该 Bean 的同一个实例。该作用域仅在使用 portlet context 时有效。

在上述五种作用域中，singleton 和 prototype 是最常用的两种，接下来将对这两种作用域进行详细讲解。

## singleton 作用域

singleton 是 Spring 容器默认的作用域，当一个 Bean 的作用域为 singleton 时，Spring 容器中只会存在一个共享的 Bean 实例，并且所有对 Bean 的请求，只要 id 与该 Bean 定义相匹配，就只会返回 Bean 的同一个实例。

通常情况下，这种单例模式对于无会话状态的 Bean（如 DAO 层、Service 层）来说，是最理想的选择。

在 Spring 配置文件中，可以使用 <bean> 元素的 scope 属性，将 Bean 的作用域定义成 singleton，其配置方式如下所示：

<bean id="person" class="com.mengma.scope.Person" scope="singleton"/>

在项目的 src 目录下创建一个名为 com.mengma.scope 的包，在该包下创建 Person 类，类中不需要添加任何成员，然后创建 Spring 的配置文件 applicationContext.xml，将上述 Bean 的定义方式写入配置文件中，最后创建一个名为 PersonTest 的测试类，编辑后如下所示。

```
package com.mengma.scope;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class PersonTest {   

@Test    
public void test() {        
    // 定义Spring配置文件路径        
    String xmlPath = "com/mengma/scope/applicationContext.xml";        
    // 初始化Spring容器，加载配置文件，并对bean进行实例化        
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);        
    // 输出获得实例        
    System.out.println(applicationContext.getBean("person"));        
    System.out.println(applicationContext.getBean("person"));    
    }
}
```

使用 JUnit 测试运行 test() 方法，运行成功后，控制台的输出结果如图 1 所示。



![输出结果](http://c.biancheng.net/uploads/allimg/190701/5-1ZF109232LS.png)
图 1 输出结果


从图 1 中可以看到，两次输出的结果相同，这说明 Spring 容器只创建了一个 Person 类的实例。由于 Spring 容器默认作用域是 singleton，如果不设置 scope="singleton"，则其输出结果也将是一个实例。

## prototype 作用域

使用 prototype 作用域的 Bean 会在每次请求该 Bean 时都会创建一个新的 Bean 实例。因此对需要保持会话状态的 Bean（如 [Struts2](http://c.biancheng.net/struts2/) 的 Action 类）应该使用 prototype 作用域。

在 Spring 配置文件中，要将 Bean 定义为 prototype 作用域，只需将 <bean> 元素的 scope 属性值定义成 prototype，其示例代码如下所示：

<bean id="person" class="com.mengma.scope.Person" scope="prototype"/>

将《singleton作用域》部分中的配置文件更改成上述代码形式后，再次运行 test() 方法，控制台的输出结果如图 2 所示。



![输出结果](http://c.biancheng.net/uploads/allimg/190701/5-1ZF109314N61.png)
图 2 输出结果


从图 2 的输出结果中可以看到，两次输出的结果并不相同，这说明在 prototype 作用域下，Spring 容器创建了两个不同的 Person 实例。



# Spring Bean的生命周期

[Spring](http://c.biancheng.net/spring/) 容器可以管理 singleton 作用域 Bean 的生命周期，在此作用域下，Spring 能够精确地知道该 Bean 何时被创建，何时初始化完成，以及何时被销毁。

而对于 prototype 作用域的 Bean，Spring 只负责创建，当容器创建了 Bean 的实例后，Bean 的实例就交给客户端代码管理，Spring 容器将不再跟踪其生命周期。每次客户端请求 prototype 作用域的 Bean 时，Spring 容器都会创建一个新的实例，并且不会管那些被配置成 prototype 作用域的 Bean 的生命周期。

了解 Spring 生命周期的意义就在于，可以利用 Bean 在其存活期间的指定时刻完成一些相关操作。这种时刻可能有很多，但一般情况下，会在 Bean 被初始化后和被销毁前执行一些相关操作。

在 Spring 中，Bean 的生命周期是一个很复杂的执行过程，我们可以利用 Spring 提供的方法定制 Bean 的创建过程。

当一个 Bean 被加载到 Spring 容器时，它就具有了生命，而 Spring 容器在保证一个 Bean 能够使用之前，会进行很多工作。Spring 容器中 Bean 的生命周期流程如图 1 所示。



![Bean的生命周期](http://c.biancheng.net/uploads/allimg/190701/5-1ZF1100325116.png)
图 1 Bean 的生命周期

Bean 生命周期的整个执行过程描述如下。

1）根据配置情况调用 Bean 构造方法或工厂方法实例化 Bean。

2）利用依赖注入完成 Bean 中所有属性值的配置注入。

3）如果 Bean 实现了 BeanNameAware 接口，则 Spring 调用 Bean 的 setBeanName() 方法传入当前 Bean 的 id 值。

4）如果 Bean 实现了 BeanFactoryAware 接口，则 Spring 调用 setBeanFactory() 方法传入当前工厂实例的引用。

5）如果 Bean 实现了 ApplicationContextAware 接口，则 Spring 调用 setApplicationContext() 方法传入当前 ApplicationContext 实例的引用。

6）如果 BeanPostProcessor 和 Bean 关联，则 Spring 将调用该接口的预初始化方法 postProcessBeforeInitialzation() 对 Bean 进行加工操作，此处非常重要，Spring 的 AOP 就是利用它实现的。

7）如果 Bean 实现了 InitializingBean 接口，则 Spring 将调用 afterPropertiesSet() 方法。

8）如果在配置文件中通过 init-method 属性指定了初始化方法，则调用该初始化方法。

9）如果 BeanPostProcessor 和 Bean 关联，则 Spring 将调用该接口的初始化方法 postProcessAfterInitialization()。此时，Bean 已经可以被应用系统使用了。

10）如果在 <bean> 中指定了该 Bean 的作用范围为 scope="singleton"，则将该 Bean 放入 Spring IoC 的缓存池中，将触发 Spring 对该 Bean 的生命周期管理；如果在 <bean> 中指定了该 Bean 的作用范围为 scope="prototype"，则将该 Bean 交给调用者，调用者管理该 Bean 的生命周期，Spring 不再管理该 Bean。

11）如果 Bean 实现了 DisposableBean 接口，则 Spring 会调用 destory() 方法将 Spring 中的 Bean 销毁；如果在配置文件中通过 destory-method 属性指定了 Bean 的销毁方法，则 Spring 将调用该方法对 Bean 进行销毁。

Spring 为 Bean 提供了细致全面的生命周期过程，通过实现特定的接口或 <bean> 的属性设置，都可以对 Bean 的生命周期过程产生影响。虽然可以随意配置 <bean> 的属性，但是建议不要过多地使用 Bean 实现接口，因为这样会导致代码和 Spring 的聚合过于紧密。



**一、生命周期流程图：**

　　Spring Bean的完整生命周期从创建Spring容器开始，直到最终Spring容器销毁Bean，这其中包含了一系列关键点。

![img](https://images0.cnblogs.com/i/580631/201405/181453414212066.png)

![img](https://images0.cnblogs.com/i/580631/201405/181454040628981.png)

　

若容器注册了以上各种接口，程序那么将会按照以上的流程进行。下面将仔细讲解各接口作用。

 

**二、各种接口方法分类**

Bean的完整生命周期经历了各种方法调用，这些方法可以划分为以下几类：

1、Bean自身的方法　　：　　这个包括了Bean本身调用的方法和通过配置文件中<bean>的init-method和destroy-method指定的方法

2、Bean级生命周期接口方法　　：　　这个包括了BeanNameAware、BeanFactoryAware、InitializingBean和DiposableBean这些接口的方法

3、容器级生命周期接口方法　　：　　这个包括了InstantiationAwareBeanPostProcessor 和 BeanPostProcessor 这两个接口实现，一般称它们的实现类为“后处理器”。

4、工厂后处理器接口方法　　：　　这个包括了AspectJWeavingEnabler, ConfigurationClassPostProcessor, CustomAutowireConfigurer等等非常有用的工厂后处理器　　接口的方法。工厂后处理器也是容器级的。在应用上下文装配配置文件之后立即调用。

　　

**三、演示**

我们用一个简单的Spring Bean来演示一下Spring Bean的生命周期。

1、首先是一个简单的Spring Bean，调用Bean自身的方法和Bean级生命周期接口方法，为了方便演示，它实现了BeanNameAware、BeanFactoryAware、InitializingBean和DiposableBean这4个接口，同时有2个方法，对应配置文件中<bean>的init-method和destroy-method。如下：



```
 1 package springBeanTest;
 2 
 3 import org.springframework.beans.BeansException;
 4 import org.springframework.beans.factory.BeanFactory;
 5 import org.springframework.beans.factory.BeanFactoryAware;
 6 import org.springframework.beans.factory.BeanNameAware;
 7 import org.springframework.beans.factory.DisposableBean;
 8 import org.springframework.beans.factory.InitializingBean;
 9 
10 /**
11  * @author qsk
12  */
13 public class Person implements BeanFactoryAware, BeanNameAware,
14         InitializingBean, DisposableBean {
15 
16     private String name;
17     private String address;
18     private int phone;
19 
20     private BeanFactory beanFactory;
21     private String beanName;
22 
23     public Person() {
24         System.out.println("【构造器】调用Person的构造器实例化");
25     }
26 
27     public String getName() {
28         return name;
29     }
30 
31     public void setName(String name) {
32         System.out.println("【注入属性】注入属性name");
33         this.name = name;
34     }
35 
36     public String getAddress() {
37         return address;
38     }
39 
40     public void setAddress(String address) {
41         System.out.println("【注入属性】注入属性address");
42         this.address = address;
43     }
44 
45     public int getPhone() {
46         return phone;
47     }
48 
49     public void setPhone(int phone) {
50         System.out.println("【注入属性】注入属性phone");
51         this.phone = phone;
52     }
53 
54     @Override
55     public String toString() {
56         return "Person [address=" + address + ", name=" + name + ", phone="
57                 + phone + "]";
58     }
59 
60     // 这是BeanFactoryAware接口方法
61     @Override
62     public void setBeanFactory(BeanFactory arg0) throws BeansException {
63         System.out
64                 .println("【BeanFactoryAware接口】调用BeanFactoryAware.setBeanFactory()");
65         this.beanFactory = arg0;
66     }
67 
68     // 这是BeanNameAware接口方法
69     @Override
70     public void setBeanName(String arg0) {
71         System.out.println("【BeanNameAware接口】调用BeanNameAware.setBeanName()");
72         this.beanName = arg0;
73     }
74 
75     // 这是InitializingBean接口方法
76     @Override
77     public void afterPropertiesSet() throws Exception {
78         System.out
79                 .println("【InitializingBean接口】调用InitializingBean.afterPropertiesSet()");
80     }
81 
82     // 这是DiposibleBean接口方法
83     @Override
84     public void destroy() throws Exception {
85         System.out.println("【DiposibleBean接口】调用DiposibleBean.destory()");
86     }
87 
88     // 通过<bean>的init-method属性指定的初始化方法
89     public void myInit() {
90         System.out.println("【init-method】调用<bean>的init-method属性指定的初始化方法");
91     }
92 
93     // 通过<bean>的destroy-method属性指定的初始化方法
94     public void myDestory() {
95         System.out.println("【destroy-method】调用<bean>的destroy-method属性指定的初始化方法");
96     }
97 }
```

 

2、接下来是演示BeanPostProcessor接口的方法，如下：

```
 1 package springBeanTest;
 2 
 3 import org.springframework.beans.BeansException;
 4 import org.springframework.beans.factory.config.BeanPostProcessor;
 5 
 6 public class MyBeanPostProcessor implements BeanPostProcessor {
 7 
 8     public MyBeanPostProcessor() {
 9         super();
10         System.out.println("这是BeanPostProcessor实现类构造器！！");
11         // TODO Auto-generated constructor stub
12     }
13 
14     @Override
15     public Object postProcessAfterInitialization(Object arg0, String arg1)
16             throws BeansException {
17         System.out
18         .println("BeanPostProcessor接口方法postProcessAfterInitialization对属性进行更改！");
19         return arg0;
20     }
21 
22     @Override
23     public Object postProcessBeforeInitialization(Object arg0, String arg1)
24             throws BeansException {
25         System.out
26         .println("BeanPostProcessor接口方法postProcessBeforeInitialization对属性进行更改！");
27         return arg0;
28     }
29 }
```

如上，BeanPostProcessor接口包括2个方法postProcessAfterInitialization和postProcessBeforeInitialization，这两个方法的第一个参数都是要处理的Bean对象，第二个参数都是Bean的name。返回值也都是要处理的Bean对象。这里要注意。

 

3、InstantiationAwareBeanPostProcessor 接口本质是BeanPostProcessor的子接口，一般我们继承Spring为其提供的适配器类InstantiationAwareBeanPostProcessor Adapter来使用它，如下：

```
 1 package springBeanTest;
 2 
 3 import java.beans.PropertyDescriptor;
 4 
 5 import org.springframework.beans.BeansException;
 6 import org.springframework.beans.PropertyValues;
 7 import org.springframework.beans.factory.config.InstantiationAwareBeanPostProcessorAdapter;
 8 
 9 public class MyInstantiationAwareBeanPostProcessor extends
10         InstantiationAwareBeanPostProcessorAdapter {
11 
12     public MyInstantiationAwareBeanPostProcessor() {
13         super();
14         System.out
15                 .println("这是InstantiationAwareBeanPostProcessorAdapter实现类构造器！！");
16     }
17 
18     // 接口方法、实例化Bean之前调用
19     @Override
20     public Object postProcessBeforeInstantiation(Class beanClass,
21             String beanName) throws BeansException {
22         System.out
23                 .println("InstantiationAwareBeanPostProcessor调用postProcessBeforeInstantiation方法");
24         return null;
25     }
26 
27     // 接口方法、实例化Bean之后调用
28     @Override
29     public Object postProcessAfterInitialization(Object bean, String beanName)
30             throws BeansException {
31         System.out
32                 .println("InstantiationAwareBeanPostProcessor调用postProcessAfterInitialization方法");
33         return bean;
34     }
35 
36     // 接口方法、设置某个属性时调用
37     @Override
38     public PropertyValues postProcessPropertyValues(PropertyValues pvs,
39             PropertyDescriptor[] pds, Object bean, String beanName)
40             throws BeansException {
41         System.out
42                 .println("InstantiationAwareBeanPostProcessor调用postProcessPropertyValues方法");
43         return pvs;
44     }
45 }
```

这个有3个方法，其中第二个方法postProcessAfterInitialization就是重写了BeanPostProcessor的方法。第三个方法postProcessPropertyValues用来操作属性，返回值也应该是PropertyValues对象。

 

4、演示工厂后处理器接口方法，如下：

```
 1 package springBeanTest;
 2 
 3 import org.springframework.beans.BeansException;
 4 import org.springframework.beans.factory.config.BeanDefinition;
 5 import org.springframework.beans.factory.config.BeanFactoryPostProcessor;
 6 import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
 7 
 8 public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {
 9 
10     public MyBeanFactoryPostProcessor() {
11         super();
12         System.out.println("这是BeanFactoryPostProcessor实现类构造器！！");
13     }
14 
15     @Override
16     public void postProcessBeanFactory(ConfigurableListableBeanFactory arg0)
17             throws BeansException {
18         System.out
19                 .println("BeanFactoryPostProcessor调用postProcessBeanFactory方法");
20         BeanDefinition bd = arg0.getBeanDefinition("person");
21         bd.getPropertyValues().addPropertyValue("phone", "110");
22     }
23 
24 }
```

 

5、配置文件如下beans.xml，很简单，使用ApplicationContext,处理器不用手动注册：

```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
    xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans-3.2.xsd">

    <bean id="beanPostProcessor" class="springBeanTest.MyBeanPostProcessor">
    </bean>

    <bean id="instantiationAwareBeanPostProcessor" class="springBeanTest.MyInstantiationAwareBeanPostProcessor">
    </bean>

    <bean id="beanFactoryPostProcessor" class="springBeanTest.MyBeanFactoryPostProcessor">
    </bean>
    
    <bean id="person" class="springBeanTest.Person" init-method="myInit"
        destroy-method="myDestory" scope="singleton" p:name="张三" p:address="广州"
        p:phone="15900000000" />

</beans>
```

 

 

6、下面测试一下：



```
 1 package springBeanTest;
 2 
 3 import org.springframework.context.ApplicationContext;
 4 import org.springframework.context.support.ClassPathXmlApplicationContext;
 5 
 6 public class BeanLifeCycle {
 7 
 8     public static void main(String[] args) {
 9 
10         System.out.println("现在开始初始化容器");
11         
12         ApplicationContext factory = new ClassPathXmlApplicationContext("springBeanTest/beans.xml");
13         System.out.println("容器初始化成功");    
14         //得到Preson，并使用
15         Person person = factory.getBean("person",Person.class);
16         System.out.println(person);
17         
18         System.out.println("现在开始关闭容器！");
19         ((ClassPathXmlApplicationContext)factory).registerShutdownHook();
20     }
21 }
```

关闭容器使用的是实际是AbstractApplicationContext的钩子方法。

我们来看一下结果：

```
现在开始初始化容器
2014-5-18 15:46:20 org.springframework.context.support.AbstractApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@19a0c7c: startup date [Sun May 18 15:46:20 CST 2014]; root of context hierarchy
2014-5-18 15:46:20 org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
信息: Loading XML bean definitions from class path resource [springBeanTest/beans.xml]
这是BeanFactoryPostProcessor实现类构造器！！
BeanFactoryPostProcessor调用postProcessBeanFactory方法
这是BeanPostProcessor实现类构造器！！
这是InstantiationAwareBeanPostProcessorAdapter实现类构造器！！
2014-5-18 15:46:20 org.springframework.beans.factory.support.DefaultListableBeanFactory preInstantiateSingletons
信息: Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@9934d4: defining beans [beanPostProcessor,instantiationAwareBeanPostProcessor,beanFactoryPostProcessor,person]; root of factory hierarchy
InstantiationAwareBeanPostProcessor调用postProcessBeforeInstantiation方法
【构造器】调用Person的构造器实例化
InstantiationAwareBeanPostProcessor调用postProcessPropertyValues方法
【注入属性】注入属性address
【注入属性】注入属性name
【注入属性】注入属性phone
【BeanNameAware接口】调用BeanNameAware.setBeanName()
【BeanFactoryAware接口】调用BeanFactoryAware.setBeanFactory()
BeanPostProcessor接口方法postProcessBeforeInitialization对属性进行更改！
【InitializingBean接口】调用InitializingBean.afterPropertiesSet()
【init-method】调用<bean>的init-method属性指定的初始化方法
BeanPostProcessor接口方法postProcessAfterInitialization对属性进行更改！
InstantiationAwareBeanPostProcessor调用postProcessAfterInitialization方法
容器初始化成功
Person [address=广州, name=张三, phone=110]
现在开始关闭容器！
【DiposibleBean接口】调用DiposibleBean.destory()
【destroy-method】调用<bean>的destroy-method属性指定的初始化方法
```

/**
\*  ————————如果觉得本博文还行，别忘了推荐一下哦，谢谢！
\*  作者：钱书康
\*  欢迎转载，请保留此段声明。
\*  出处：http://www.cnblogs.com/zrtqsk/
*/

# Spring基于XML装配Bean

Bean 的装配可以理解为依赖关系注入，Bean 的装配方式也就是 Bean 的依赖注入方式。[Spring](http://c.biancheng.net/spring/) 容器支持多种形式的 Bean 的装配方式，如基于 XML 的 Bean 装配、基于 Annotation 的 Bean 装配和自动装配等。

Spring 基于 XML 的装配通常采用两种实现方式，即设值注入（Setter Injection）和构造注入（Constructor Injection）。本节将讲解如何在 XML 配置文件中使用这两种注入方式。

在 Spring 实例化 Bean 的过程中，首先会调用默认的构造方法实例化 Bean 对象，然后通过 [Java](http://c.biancheng.net/java/) 的反射机制调用 setXxx() 方法进行属性的注入。因此，设值注入要求一个 Bean 的对应类必须满足以下两点要求。

- 必须提供一个默认的无参构造方法。
- 必须为需要注入的属性提供对应的 setter 方法。


使用设值注入时，在 Spring 配置文件中，需要使用 <bean> 元素的子元素 <property> 元素为每个属性注入值。而使用构造注入时，在配置文件中，主要使用 <constructor-arg> 标签定义构造方法的参数，可以使用其 value 属性（或子元素）设置该参数的值。下面通过案例演示基于 XML 方式的 Bean 的装配。

#### 1. 创建 Person 类

在项目 springDemo02 中的 src 目录下，创建一个名称为 com.mengma.assembly 的包，在该包下创建一个 Person 类，如下所示。

```
package com.mengma.assembly;

public class Person {    
    private String name;    
    private int age;    
    public String getName() {        
    return name;    
    }    
    public void setName(String name) {        
    this.name = name;    
    }    
    public int getAge() {        
        return age;    
     }    
    public void setAge(int age) {        
    	this.age = age;    
    }    
    // 重写toString()方法    
    public String toString() {        
    	return "Person[name=" + name + ",age=" + age + "]";    
	}    
    // 默认无参的构造方法    
    public Person() {        
		super();    
	}    
	// 有参的构造方法    
	public Person(String name, int age) {        
        super();        
        this.name = name;        
        this.age = age;    
	}
}
```

上述代码中，定义了 name 和 age 两个属性，并为其提供了 getter 和 setter 方法，由于要使用构造注入，所以需要提供有参的构造方法。为了能更清楚地看到输出结果，这里还重写了 toString() 方法。

#### 2. 创建 Spring 配置文件

在 com.mengma.assembly 包下创建一个名为 applicationContext.xml 的配置文件，如下所示。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"    
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xmlns:p="http://www.springframework.org/schema/p"    
xsi:schemaLocation="http://www.springframework.org/schema/beans   
http://www.springframework.org/schema/beans/spring-beans-3.2.xsd">    
<!-- 使用设值注入方式装配Person实例 -->    
    <bean id="person1" class="com.mengma.assembly.Person">        
        <property name="name" value="zhangsan" />        
        <property name="age" value="20" />    
    </bean>    
<!-- 使用构造方法装配Person实例 -->    
    <bean id="person2" class="com.mengma.assembly.Person">        
        <constructor-arg index="0" value="lisi" />        
        <constructor-arg index="1" value="21" />    
    </bean>
</beans>
```

上述代码中，首先使用了设值注入方式装配 Person 类的实例，其中 <property> 子元素用于调用 Bean 实例中的 setXxx() 方法完成属性赋值。然后使用了构造方式装配了 Person 类的实例，其中 <constructor-arg> 元素用于定义构造方法的参数，其属性 index 表示其索引（从 0 开始），value 属性用于设置注入的值。

#### 3. 创建测试类

在 com.mengma.assembly 包下创建一个名称为 XmlBeanAssemblyTest 的测试类，编辑后如下所示。

```
package com.mengma.assembly;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class XmlBeanAssemblyTest {    
    @Test    
    public void test() {        
        // 定义Spring配置文件路径        
        String xmlPath = "com/mengma/assembly/applicationContext.xml";        
        // 初始化Spring容器，加载配置文件，并对bean进行实例化        
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);        
        // 设值方式输出结果        
        System.out.println(applicationContext.getBean("person1"));        
        // 构造方式输出结果        
        System.out.println(applicationContext.getBean("person2"));    
    }
}
```

上述代码中，分别获取并输出了 id 为 person1 和 person2 的实例。

#### 4. 运行项目并查看结果

使用 JUnit 测试运行 test() 方法，运行成功后，控制台的输出结果如图 1 所示。



![输出结果](http://c.biancheng.net/uploads/allimg/190701/5-1ZF1110514c6.png)
图 1 输出结果


从图 1 的输出结果中可以看出，使用设值注入和构造注入两种方式都成功装配了 Person 实例。



# Spring基于Annotation装配Bean

在 [Spring](http://c.biancheng.net/spring/) 中，尽管使用 XML 配置文件可以实现 Bean 的装配工作，但如果应用中 Bean 的数量较多，会导致 XML 配置文件过于臃肿，从而给维护和升级带来一定的困难。

[Java](http://c.biancheng.net/java/) 从 JDK 5.0 以后，提供了 Annotation（注解）功能，Spring 也提供了对 Annotation 技术的全面支持。Spring3 中定义了一系列的 Annotation（注解），常用的注解如下。

#### 1）@Component

可以使用此注解描述 Spring 中的 Bean，但它是一个泛化的概念，仅仅表示一个组件（Bean），并且可以作用在任何层次。使用时只需将该注解标注在相应类上即可。

#### 2）@Repository

用于将数据访问层（DAO层）的类标识为 Spring 中的 Bean，其功能与 @Component 相同。

#### 3）@Service

通常作用在业务层（Service 层），用于将业务层的类标识为 Spring 中的 Bean，其功能与 @Component 相同。

#### 4）@Controller

通常作用在控制层（如 [Struts2](http://c.biancheng.net/struts2/) 的 Action），用于将控制层的类标识为 Spring 中的 Bean，其功能与 @Component 相同。

#### 5）@Autowired

用于对 Bean 的属性变量、属性的 Set 方法及构造函数进行标注，配合对应的注解处理器完成 Bean 的自动配置工作。默认按照 Bean 的类型进行装配。

#### 6）@Resource

其作用与 Autowired 一样。其区别在于 @Autowired 默认按照 Bean 类型装配，而 @Resource 默认按照 Bean 实例名称进行装配。

@Resource 中有两个重要属性：name 和 type。

Spring 将 name 属性解析为 Bean 实例名称，type 属性解析为 Bean 实例类型。如果指定 name 属性，则按实例名称进行装配；如果指定 type 属性，则按 Bean 类型进行装配。

如果都不指定，则先按 Bean 实例名称装配，如果不能匹配，则再按照 Bean 类型进行装配；如果都无法匹配，则抛出 NoSuchBeanDefinitionException 异常。

#### 7）@Qualifier

与 @Autowired 注解配合使用，会将默认的按 Bean 类型装配修改为按 Bean 的实例名称装配，Bean 的实例名称由 @Qualifier 注解的参数指定。

#### 1. 创建 DAO 层接口

在 src 目录下创建一个名为 com.mengma.annotation 的包，在该包下创建一个名为 PersonDao 的接口，并添加一个 add() 方法，如下所示。

```
package com.mengma.annotation;

public interface PersonDao {    
	public void add();
}
```

#### 2. 创建 DAO 层接口的实现类

在 com.mengma.annotation 包下创建 PersonDao 接口的实现类 PersonDaoImpl，编辑后如下所示。

```
package com.mengma.annotation;

import org.springframework.stereotype.Repository;

@Repository("personDao")
public class PersonDaoImpl implements PersonDao {    
    @Override    
    public void add() {        
        System.out.println("Dao层的add()方法执行了...");    
    }
}
```

上述代码中，首先使用 @Repository 注解将 PersonDaoImpl 类标识为 Spring 中的 Bean，其写法相当于配置文件中 <bean id="personDao"class="com.mengma.annotation.PersonDaoImpl"/> 的书写。然后在 add() 方法中输出一句话，用于验证是否成功调用了该方法。

#### 3. 创建 Service 层接口

在 com.mengma.annotation 包下创建一个名为 PersonService 的接口，并添加一个 add() 方法，如下所示。

```
package com.mengma.annotation;

public interface PersonService {    
	public void add();
}
```

#### 4. 创建 Service 层接口的实现类

在 com.mengma.annotation 包下创建 PersonService 接口的实现类 PersonServiceImpl，编辑后如下所示。

```
package com.mengma.annotation;

import javax.annotation.Resource;
import org.springframework.stereotype.Service;

@Service("personService")
public class PersonServiceImpl implements PersonService {    
    @Resource(name = "personDao")    
    private PersonDao personDao;  
    
    public PersonDao getPersonDao() {        
    return personDao;    
} 

@Override    
public void add() {        
    personDao.add();
    // 调用personDao中的add()方法        
    System.out.println("Service层的add()方法执行了...");    
    }
}
```

上述代码中，首先使用 @Service 注解将 PersonServiceImpl 类标识为 Spring 中的 Bean，其写法相当于配置文件中 

<bean id="personService"class="com.mengma.annotation.PersonServiceImpl"/> 的书写。

然后使用 @Resource 注解标注在属性 personDao 上（也可标注在 personDao 的 setPersonDao() 方法上），这相当于配置文件中 <property name="personDao"ref="personDao"/> 的写法。最后在该类的 add() 方法中调用 personDao 中的 add() 方法，并输出一句话。

#### 5. 创建 Action

在 com.mengma.annotation 包下创建一个名为 PersonAction 的类，编辑后如下所示。

```
package com.mengma.annotation;

import javax.annotation.Resource;
import org.springframework.stereotype.Controller;

@Controller("personAction")
public class PersonAction {    

	@Resource(name = "personService")    
    private PersonService personService;    
    
    public PersonService getPersonService() {        
    	return personService;    
    }    
	public void add() {        
    	personService.add(); 
        // 调用personService中的add()方法        
        System.out.println("Action层的add()方法执行了...");    
    }
}
```

上述代码中，首先使用 @Controller 注解标注 PersonAction 类，其写法相当于在配置文件中编写 <bean id="personAction"class="com.mengma.annotation.PersonAction"/>。

然后使用了 @Resource 注解标注在 personService 上，这相当于在配置文件内编写 <property name="personService"ref="personService"/>。

最后在其 add() 方法中调用了 personService 中的 add() 方法，并输出一句话。

#### 6. 创建 Spring 配置文件

在 com.mengma.annotation 包下创建一个名为 applicationContext.xml 的配置文件，如下所示。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"    
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    
xmlns:aop="http://www.springframework.org/schema/aop"   
xmlns:p="http://www.springframework.org/schema/p"   
xmlns:tx="http://www.springframework.org/schema/tx"    
xmlns:context="http://www.springframework.org/schema/context"    
xsi:schemaLocation="            
                http://www.springframework.org/schema/beans            
                http://www.springframework.org/schema/beans/spring-beans-2.5.xsd            
                http://www.springframework.org/schema/aop           
                http://www.springframework.org/schema/aop/spring-aop-2.5.xsd           
                http://www.springframework.org/schema/tx           
                http://www.springframework.org/schema/tx/spring-tx-2.5.xsd           
                http://www.springframework.org/schema/context            
                http://www.springframework.org/schema/context/spring-context.xsd">   

<!--使用context命名空间，通知spring扫描指定目录，进行注解的解析-->    
	<context:component-scan base-package="com.mengma.annotation"/>
</beans>
```

与之前的配置文件相比，上述代码的<beans>元素中增加了第 7 行、第 15 行和第 16 行中包含有 context 的代码，然后在第 18 行代码中，使用 context 命名空间的 component-scan 元素进行注解的扫描，其 base-package 属性用于通知 spring 所需要扫描的目录。

#### 7. 创建测试类

在 com.mengma.annotation 包下创建一个名为 AnnotationTest 的测试类，编辑后如下所示。

```
package com.mengma.annotation;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AnnotationTest {   

@Test   
public void test() {        
    // 定义Spring配置文件路径        
    String xmlPath = "com/mengma/annotation/applicationContext.xml";        
    // 初始化Spring容器，加载配置文件，并对bean进行实例化        
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);        
    // 获得personAction实例        
    PersonAction personAction = (PersonAction) applicationContext.getBean("personAction");        
    // 调用personAction中的add()方法        
    personAction.add();    
    }
}
```

上述代码中，首先通过加载配置文件并获取 personAction 的实例，然后调用该实例的 add() 方法。

#### 8. 运行程序并查看结果

使用 JUnit 测试运行 test() 方法，运行成功后，输出结果如图 1 所示。



![输出结果](http://c.biancheng.net/uploads/allimg/190701/5-1ZF113214U56.png)
图 1 输出结果


从图 1 的输出结果中可以看出，DAO 层、Service 层和 Action 层的 add() 方法都成功输出了结果。由此可知，使用 Annotation 装配 Bean 的方式已经成功实现了。



# Spring自动装配Bean

除了使用 XML 和 Annotation 的方式装配 Bean 以外，还有一种常用的装配方式——自动装配。自动装配就是指 [Spring](http://c.biancheng.net/spring/) 容器可以自动装配（autowire）相互协作的 Bean 之间的关联关系，将一个 Bean 注入其他 Bean 的 Property 中。

要使用自动装配，就需要配置 <bean> 元素的 autowire 属性。autowire 属性有五个值，具体说明如表 1 所示。



| 名称        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| byName      | 根据 Property 的 name 自动装配，如果一个 Bean 的 name 和另一个 Bean 中的 Property 的 name 相同，则自动装配这个 Bean 到 Property 中。 |
| byType      | 根据 Property 的数据类型（Type）自动装配，如果一个 Bean 的数据类型兼容另一个 Bean 中 Property 的数据类型，则自动装配。 |
| constructor | 根据构造方法的参数的数据类型，进行 byType 模式的自动装配。   |
| autodetect  | 如果发现默认的构造方法，则用 constructor 模式，否则用 byType 模式。 |
| no          | 默认情况下，不使用自动装配，Bean 依赖必须通过 ref 元素定义。 |

下面通过修改《[Spring基于Annotation装配Bean](http://c.biancheng.net/view/4265.html)》中的案例演示如何实现自动装配。首先将 applicationContext.xml 配置文件修改成自动装配形式，如下所示。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"    
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"     
    xmlns:aop="http://www.springframework.org/schema/aop"   
    xmlns:p="http://www.springframework.org/schema/p"     
    xmlns:tx="http://www.springframework.org/schema/tx"    
    xmlns:context="http://www.springframework.org/schema/context"    
    xsi:schemaLocation="              
                http://www.springframework.org/schema/beans            
                http://www.springframework.org/schema/beans/spring-beans-2.5.xsd             
                http://www.springframework.org/schema/aop             
                http://www.springframework.org/schema/aop/spring-aop-2.5.xsd             
                http://www.springframework.org/schema/tx             
                http://www.springframework.org/schema/tx/spring-tx-2.5.xsd            
                http://www.springframework.org/schema/context            
                http://www.springframework.org/schema/context/spring-context.xsd">   
                
<bean id="personDao" class="com.mengma.annotation.PersonDaoImpl" />    
<bean id="personService" class="com.mengma.annotation.PersonServiceImpl"  autowire="byName" />   
<bean id="personAction" class="com.mengma.annotation.PersonAction"  autowire="byName" />
</beans>
```

在上述配置文件中，用于配置 personService 和 personAction 的 <bean> 元素中除了 id 和 class 属性以外，还增加了 autowire 属性，并将其属性值设置为 byName（按属性名称自动装配）。

默认情况下，配置文件中需要通过 ref 装配 Bean，但设置了 autowire="byName"，Spring 会在配置文件中自动寻找与属性名字 personDao 相同的 <bean>，找到后，通过调用 setPersonDao（PersonDao personDao）方法将 id 为 personDao 的 Bean 注入 id 为 personService 的 Bean 中，这时就不需要通过 ref 装配了。

使用 JUnit 再次运行测试类中的 test() 方法，控制台的显示结果如图 1 所示。



![运行结果](http://c.biancheng.net/uploads/allimg/190701/5-1ZF113214U56.png)
图 1 运行结果


从图 1 的输出结果中可以看出，使用自动装配的方式同样完成了依赖注入。



# Spring AOP（面向切面编程）是什么？

面向切面编程（AOP）和面向对象编程（OOP）类似，也是一种编程模式。[Spring](http://c.biancheng.net/spring/) AOP 是基于 AOP 编程模式的一个框架，它的使用有效减少了系统间的重复代码，达到了模块间的松耦合目的。

AOP 的全称是“Aspect Oriented Programming”，即面向切面编程，它将业务逻辑的各个部分进行隔离，使开发人员在编写业务逻辑时可以专心于核心业务，从而提高了开发效率。

AOP 采取横向抽取机制，取代了传统纵向继承体系的重复性代码，其应用主要体现在事务处理、日志管理、权限控制、异常处理等方面。

目前最流行的 AOP 框架有两个，分别为 Spring AOP 和 AspectJ。

Spring AOP 使用纯 [Java](http://c.biancheng.net/java/) 实现，不需要专门的编译过程和类加载器，在运行期间通过代理方式向目标类植入增强的代码。

AspectJ 是一个基于 Java 语言的 AOP 框架，从 Spring 2.0 开始，Spring AOP 引入了对 AspectJ 的支持。AspectJ 扩展了 Java 语言，提供了一个专门的编译器，在编译时提供横向代码的植入。

为了更好地理解 AOP，就需要对 AOP 的相关术语有一些了解，这些专业术语主要包含 Joinpoint、Pointcut、Advice、Target、Weaving、Proxy 和 Aspect，它们的含义如下表所示。



| 名称                | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| Joinpoint（连接点） | 指那些被拦截到的点，在 Spring 中，可以被动态代理拦截目标类的方法。 |
| Pointcut（切入点）  | 指要对哪些 Joinpoint 进行拦截，即被拦截的连接点。            |
| Advice（通知）      | 指拦截到 Joinpoint 之后要做的事情，即对切入点增强的内容。    |
| Target（目标）      | 指代理的目标对象。                                           |
| Weaving（植入）     | 指把增强代码应用到目标上，生成代理对象的过程。               |
| Proxy（代理）       | 指生成的代理对象。                                           |
| Aspect（切面）      | 切入点和通知的结合。                                         |



# Spring JDK动态代理（附带实例）

JDK 动态代理是通过 JDK 中的 java.lang.reflect.Proxy 类实现的。下面通过具体的案例演示 JDK 动态代理的使用。

#### 1. 创建项目

在 MyEclipse 中创建一个名称为 springDemo03 的 Web 项目，将 [Spring](http://c.biancheng.net/spring/) 支持和依赖的 JAR 包复制到 Web 项目的 WEB-INF/lib 目录中，并发布到类路径下。

#### 2. 创建接口 CustomerDao

在项目的 src 目录下创建一个名为 com.mengma.dao 的包，在该包下创建一个 CustomerDao 接口，编辑后如下所示。

```
package com.mengma.dao;

public interface CustomerDao {    
    public void add(); // 添加    
    public void update(); // 修改    
    public void delete(); // 删除    
    public void find(); // 查询
}
```

#### 3. 创建实现类 CustomerDaoImpl

在 com.mengma.dao 包下创建 CustomerDao 接口的实现类 CustomerDaoImpl，并实现该接口中的所有方法，如下所示。

```
package com.mengma.dao;

public class CustomerDaoImpl implements CustomerDao {   

    @Override    
    public void add() {        
    	System.out.println("添加客户...");    
    }

    @Override    
    public void update() {        
        System.out.println("修改客户...");    
    }    

    @Override    
    public void delete() {        
        System.out.println("删除客户...");    
    }    

    @Override    
    public void find() {        
        System.out.println("修改客户...");    
    }

}
```

#### 4. 创建切面类 MyAspect

在 src 目录下，创建一个名为 com.mengma.jdk 的包，在该包下创建一个切面类 MyAspect，编辑后如下所示。

```
package com.mengma.jdk;

public class MyAspect {    
    public void myBefore() {        
        System.out.println("方法执行之前");    
    }   
    public void myAfter() {        
        System.out.println("方法执行之后");    
    }
}
```

上述代码中，在切面中定义了两个增强的方法，分别为 myBefore() 方法和 myAfter() 方法，用于对目标类（CustomerDaoImpl）进行增强。

#### 5. 创建代理类 MyBeanFactory

在 com.mengma.jdk 包下创建一个名为 MyBeanFactory 的类，在该类中使用 java.lang.reflect.Proxy 实现 JDK 动态代理，如下所示。

```
package com.mengma.jdk;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import com.mengma.dao.CustomerDao;
import com.mengma.dao.CustomerDaoImpl;

public class MyBeanFactory {    
public static CustomerDao getBean() {        
    // 准备目标类        
    final CustomerDao customerDao = new CustomerDaoImpl();        
    // 创建切面类实例        
    final MyAspect myAspect = new MyAspect();        
    // 使用代理类，进行增强        
	return (CustomerDao) Proxy.newProxyInstance(                
		MyBeanFactory.class.getClassLoader(),new Class[] { CustomerDao.class }, 
			new InvocationHandler() {                    
                public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {                       
                    myAspect.myBefore(); // 前增强                       
                    Object obj = method.invoke(customerDao, args);                        
                    myAspect.myAfter(); // 后增强                        
                    return obj;                    
                    }                
                });    
	}
}
```

上述代码中，定义了一个静态的 getBean() 方法，这里模拟 Spring 框架的 IoC 思想，通过调用 getBean() 方法创建实例，第 14 行代码创建了 customerDao 实例。

第 16 行代码创建的切面类实例用于调用切面类中相应的方法；第 18～26 行就是使用代理类对创建的实例 customerDao 中的方法进行增强的代码，其中 Proxy 的 newProxyInstance() 方法的第一个参数是当前类的类加载器，第二参数是所创建实例的实现类的接口，第三个参数就是需要增强的方法。

在目标类方法执行的前后，分别执行切面类中的 myBefore() 方法和 myAfter() 方法。

#### 6. 创建测试类 JDKProxyTest

在 com.mengma.jdk 包下创建一个名为 JDKProxyTest 的测试类，如下所示。

```
package com.mengma.jdk;

import org.junit.Test;
import com.mengma.dao.CustomerDao;

public class JDKProxyTest {    
@Test    
public void test() {       
        // 从工厂获得指定的内容（相当于spring获得，但此内容时代理对象）        
        CustomerDao customerDao = MyBeanFactory.getBean();        
        // 执行方法        
        customerDao.add();        
        customerDao.update();        
        customerDao.delete();        
        customerDao.find();    
    }
}
```

上述代码中，在调用 getBean() 方法时，获取的是 CustomerDao 类的代理对象，然后调用了该对象中的方法。

#### 7. 运行项目并查看结果

使用 JUnit 测试运行 test() 方法，运行成功后，控制台的输出结果如图 1 所示。

从图 1 的输出结果中可以看出，在调用目标类的方法前后，成功调用了增强的代码，由此说明，JDK 动态代理已经实现。



![运行结果](http://c.biancheng.net/uploads/allimg/190701/5-1ZF1153314439.png)
图 1 运行结果



# Spring CGLlB动态代理（附带实例）

通过《[Spring JDK动态代理](http://c.biancheng.net/view/4269.html)》教程的学习可以知道，JDK 动态代理使用起来非常简单，但是它也有一定的局限性，这是因为 JDK 动态代理必须要实现一个或多个接口，如果不希望实现接口，则可以使用 CGLIB 代理。

CGLIB（Code Generation Library）是一个高性能开源的代码生成包，它被许多 AOP 框架所使用，其底层是通过使用一个小而快的字节码处理框架 ASM（[Java](http://c.biancheng.net/java/) 字节码操控框架）转换字节码并生成新的类。因此 CGLIB 要依赖于 ASM 的包，解压 [Spring](http://c.biancheng.net/spring/) 的核心包 spring-core-3.2.2.RELEASE.jar，文件目录如图 1 所示。



![spring-core-3.2.2.RELEASE.jar文件](http://c.biancheng.net/uploads/allimg/190701/5-1ZF1153955939.png)
图 1 spring-core-3.2.2.RELEASE.jar文件


在图 1 中可以看出，解压的核心包中包含 cglib 和 asm，也就是说 Spring3.2.13 版本的核心包已经集成了 CGLIB 所需要的包，所以在开发中不需要另外导入 ASM 的 JAR 包了。下面通过案例演示实现 CGLIB 的代理过程。

#### 1. 创建目标类 GoodsDao

在 com.mengma.dao 包下创建目标类 GoodsDao，在类中定义增、删、改、查方法，并在每个方法编写输出语句，如下所示。

```
package com.mengma.dao;
public class GoodsDao {    
    public void add() {        
        System.out.println("添加商品...");    
    }    
    public void update() {        
        System.out.println("修改商品...");    
    }    
    public void delete() {        
        System.out.println("删除商品...");    
    }    
    public void find() {        
        System.out.println("修改商品...");    
    }
}
```

#### 2. 创建代理类 MyBeanFactory

在 src 目录下创建一个名为 com.mengma.cglib 的包，该包下创建类 MyBeanFactory，如下所示。

```
package com.mengma.cglib;

import java.lang.reflect.Method;
import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.MethodInterceptor;
import org.springframework.cglib.proxy.MethodProxy;
import com.mengma.dao.GoodsDao;import com.mengma.jdk.MyAspect;

public class MyBeanFactory {    
public static GoodsDao getBean() {        
    // 准备目标类        
    final GoodsDao goodsDao = new GoodsDao();        
    // 创建切面类实例        
    final MyAspect myAspect = new MyAspect();        
    // 生成代理类，CGLIB在运行时，生成指定对象的子类，增强        
    Enhancer enhancer = new Enhancer();        
    // 确定需要增强的类        
    enhancer.setSuperclass(goodsDao.getClass());        
    // 添加回调函数        
    enhancer.setCallback(new MethodInterceptor() {            
    	// intercept 相当于 jdk invoke，前三个参数与 jdk invoke—致            
        @Override            
        public Object intercept(Object proxy, Method method, Object[] args,MethodProxy methodProxy) throws Throwable {                			myAspect.myBefore(); // 前增强               
                Object obj = method.invoke(goodsDao, args); // 目标方法执行                
                myAspect.myAfter(); // 后增强                
                return obj;            
                }        
            });        
    // 创建代理类        
    GoodsDao goodsDaoProxy = (GoodsDao) enhancer.create();        
    return goodsDaoProxy;    
	}
}
```

上述代码中，应用了 CGLIB 的核心类 Enhancer。在第 19 行代码调用了 Enhancer 类的 setSuperclass() 方法，确定目标对象。

第 21 行代码调用 setCallback() 方法添加回调函数；第 24 行代码的 intercept() 方法相当于 JDK 动态代理方式中的 invoke() 方法，该方法会在目标方法执行的前后，对切面类中的方法进行增强；第 33～34 行代码调用 Enhancer 类的 create() 方法创建代理类，最后将代理类返回。

#### 3. 创建测试类

在 com.mengma.cglib 包下创建测试类 CGLIBProxyTest，编辑后如下所示。

```
package com.mengma.cglib;

import org.junit.Test;
import com.mengma.dao.GoodsDao;
public class CGLIBProxyTest {  

@Test    
public void test() {        
    // 从工厂获得指定的内容（相当于spring获得，但此内容时代理对象）        
    GoodsDao goodsDao = MyBeanFactory.getBean();        
        // 执行方法        
        goodsDao.add();        
        goodsDao.update();        
        goodsDao.delete();        
        goodsDao.find();    
	}
}
```

上述代码中，调用 getBean() 方法时，依然获取的是 goodsDao 的代理对象，然后调用该对象的方法。使用 JUnit 测试运行 test() 方法，运行成功后，控制台的输出结果如图 2 所示。



![输出结果](http://c.biancheng.net/uploads/allimg/190701/5-1ZF1155TX91.png)
图 2 输出结果


从图 2 的输出结果中可以看出，在调用目标类的方法前后，也成功调用了增强的代码，由此说明，使用 CGLIB 代理的方式同样实现了手动代理。



# Spring通知类型及使用ProxyFactoryBean创建AOP代理

在《[Spring JDK动态代理](http://c.biancheng.net/view/4269.html)》和《[Spring CGLlB动态代理](http://c.biancheng.net/view/4271.html)》中，讲解了 AOP 手动代理的两种方式，下面通过讲解 [Spring](http://c.biancheng.net/spring/) 的通知介绍 Spring 是如何创建 AOP 代理的。

## Spring 通知类型

通过前面的学习可以知道，通知（Advice）其实就是对目标切入点进行增强的内容，Spring AOP 为通知（Advice）提供了 org.aopalliance.aop.Advice 接口。

Spring 通知按照在目标类方法的连接点位置，可以分为以下五种类型，如表 1 所示。



| 名称                                                        | 说明                                                         |
| ----------------------------------------------------------- | ------------------------------------------------------------ |
| org.springframework.aop.MethodBeforeAdvice（前置通知）      | 在方法之前自动执行的通知称为前置通知，可以应用于权限管理等功能。 |
| org.springframework.aop.AfterReturningAdvice（后置通知）    | 在方法之后自动执行的通知称为后置通知，可以应用于关闭流、上传文件、删除临时文件等功能。 |
| org.aopalliance.intercept.MethodInterceptor（环绕通知）     | 在方法前后自动执行的通知称为环绕通知，可以应用于日志、事务管理等功能。 |
| org.springframework.aop.ThrowsAdvice（异常通知）            | 在方法抛出异常时自动执行的通知称为异常通知，可以应用于处理异常记录日志等功能。 |
| org.springframework.aop.IntroductionInterceptor（引介通知） | 在目标类中添加一些新的方法和属性，可以应用于修改旧版本程序（增强类）。 |

## 声明式 Spring AOP

Spring 创建一个 AOP 代理的基本方法是使用 org.springframework.aop.framework.ProxyFactoryBean，这个类对应的切入点和通知提供了完整的控制能力，并可以生成指定的内容。

ProxyFactoryBean 类中的常用可配置属性如表 2 所示。

| 属性名称         | 描 述                                                        |
| ---------------- | ------------------------------------------------------------ |
| target           | 代理的目标对象                                               |
| proxyInterfaces  | 代理要实现的接口，如果有多个接口，则可以使用以下格式赋值： <list>   <value ></value>   ... </list> |
| proxyTargetClass | 是否对类代理而不是接口，设置为 true 时，使用 CGLIB 代理      |
| interceptorNames | 需要植入目标的 Advice                                        |
| singleton        | 返回的代理是否为单例，默认为 true（返回单实例）              |
| optimize         | 当设置为 true 时，强制使用 CGLIB                             |


在 Spring 通知中，环绕通知是一个非常典型的应用。下面通过环绕通知的案例演示 Spring 创建 AOP 代理的过程。

#### 1. 导入 JAR 包

在核心 JAR 包的基础上，再向 springDemo03 项目的 lib 目录中导入 AOP 的 JAR 包，具体如下。

- spring-aop-3.2.13.RELEASE.jar：是 Spring 为 AOP 提供的实现，在 Spring 的包中已经提供。
- com.springsource.org.aopalliance-1.0.0.jar：是 AOP 提供的规范，可以在 Spring 的官网网址 https://repo.spring.io/webapp/#/search/quick/ 中进行搜索并下载。

#### 2. 创建切面类 MyAspect

在 src 目录下创建一个名为 com.mengma.factorybean 的包，在该包下创建切面类 MyAspect，如下所示。

```
package com.mengma.factorybean;

import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;

//需要实现接口，确定哪个通知，及告诉Spring应该执行哪个方法
public class MyAspect implements MethodInterceptor {   

public Object invoke(MethodInvocation mi) throws Throwable {        
        System.out.println("方法执行之前");        
        // 执行目标方法        
        Object obj = mi.proceed();        
        System.out.println("方法执行之后");        
        return obj;    
	}
}
```

上述代码中，MyAspect 类实现了 MethodInterceptor 接口，并实现了接口的 invoke() 方法。MethodInterceptor 接口是 Spring AOP 的 JAR 包提供的，而 invoke() 方法用于确定目标方法 mi，并告诉 Spring 要在目标方法前后执行哪些方法，这里为了演示效果在目标方法前后分别向控制台输出了相应语句。

#### 3. 创建 Spring 配置文件

在 com.mengma.factorybean 包下创建配置文件 applicationContext.xml，如下所示。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"    
xmlns:xsi="http:/www.w3.org/2001/XMLSchema-instance"    
xsi:schemaLocation="http://www.springframework.org/schema/beans    
http://www.springframework.org/schema/beans/spring-beans.xsd">   

    <!--目标类 -->    
    <bean id="customerDao" class="com.mengma.dao.CustomerDaoImpl" />    
    <!-- 通知 advice -->    
    <bean id="myAspect" class="com.mengma.factorybean.MyAspect" />    
    <!--生成代理对象 -->   
    <bean id="customerDaoProxy"     class="org.springframework.aop.framework.ProxyFactoryBean">    
        <!--代理实现的接口 -->       
        <property name="proxyInterfaces" value="com.mengma.dao.CustomerDao" />        
        <!--代理的目标对象 -->        
        <property name="target" ref="customerDao" />        
        <!--用通知增强目标 -->       
        <property name="interceptorNames" value="myAspect" />       
        <!-- 如何生成代理，true:使用cglib; false :使用jdk动态代理 -->        
        <property name="proxyTargetClass" value="true" />    
    </bean>
    
</beans>
```

上述代码中，首先配置目标类和通知，然后使用 ProxyFactoryBean 类生成代理对象；第 14 行代码配置了代理实现的接口；第 16 行代码配置了代理的目标对象；第 18 行代码配置了需要植入目标的通知；当第 20 行代码中的 value 属性值为 true 时，表示使用 CGLIB 代理，属性值为 false 时，表示使用 JDK 动态代理。

#### 4. 创建测试类

在 com.mengma.factorybean 包下创建一个名为 FactoryBeanTest 的测试类，编辑后如下所示。

```
package com.mengma.factorybean;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.mengma.dao.CustomerDao;
public class FactoryBeanTest { 

@Test    
public void test() {        
        String xmlPath = "com/mengma/factorybean/applicationContext.xml";        
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);        
        CustomerDao customerDao = (CustomerDao) applicationContext.getBean("customerDaoProxy");        
        customerDao.add();        
        customerDao.update();        
        customerDao.delete();        
        customerDao.find();    
    }
}
```

#### 5. 运行项目并查看结果

使用 JUnit 测试运行 test() 方法，运行成功后，控制台的输出结果如图 1 所示。



![运行结果](http://c.biancheng.net/uploads/allimg/190702/5-1ZF2101IT59.png)
图 1 运行结果



# Spring使用AspectJ开发AOP：基于XML和基于Annotation

AspectJ 是一个基于 [Java](http://c.biancheng.net/java/) 语言的 AOP 框架，它扩展了 Java 语言。[Spring](http://c.biancheng.net/spring/) 2.0 以后，新增了对 AspectJ 方式的支持，新版本的 Spring 框架，建议使用 AspectJ 方式开发 AOP。

使用 AspectJ 开发 AOP 通常有两种方式：

- 基于 XML 的声明式。
- 基于 Annotation 的声明式。


接下来将对这两种 AOP 的开发方式进行讲解。

## 基于XML的声明式

基于 XML 的声明式是指通过 Spring 配置文件的方式定义切面、切入点及声明通知，而所有的切面和通知都必须定义在 <aop:config> 元素中。

下面通过案例演示 Spring 中如何使用基于 XML 的声明式实现 AOP 的开发。

#### 1. 导入 JAR 包

使用 AspectJ 除了需要导入 Spring AOP 的 JAR 包以外，还需要导入与 AspectJ 相关的 JAR 包，具体如下。

- spring-aspects-3.2.13.RELEASE.jar：Spring 为 AspectJ 提供的实现，在 Spring 的包中已经提供。
- com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar：是 AspectJ 提供的规范，可以在官方网址 https://repo.spring.io/webapp/#/search/quick/ 中搜索并下载。

#### 2. 创建切面类 MyAspect

在 src 目录下创建一个名为 com.mengma.aspectj.xml 的包，在该包下创建切面类 MyAspect，编辑后如下所示。

```
package com.mengma.aspectj.xml;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;

//切面类
public class MyAspect {    

    // 前置通知    
    public void myBefore(JoinPoint joinPoint) {        
        System.out.print("前置通知，目标：");        
        System.out.print(joinPoint.getTarget() + "方法名称:");        
        System.out.println(joinPoint.getSignature().getName());    
    }    

    // 后置通知   
    public void myAfterReturning(JoinPoint joinPoint) {        
    	System.out.print("后置通知，方法名称：" + joinPoint.getSignature().getName());    
    }    

    // 环绕通知    
    public Object myAround(ProceedingJoinPoint proceedingJoinPoint)  throws Throwable {        
        System.out.println("环绕开始"); // 开始        
        Object obj = proceedingJoinPoint.proceed(); // 执行当前目标方法        
        System.out.println("环绕结束"); // 结束        
        return obj;    
    }    

    // 异常通知    
    public void myAfterThrowing(JoinPoint joinPoint, Throwable e) {        
    	System.out.println("异常通知" + "出错了" + e.getMessage());    
    }    

    // 最终通知    
    public void myAfter() {        
    	System.out.println("最终通知");    
    }
}
```

上述代码中，分别定义了几种不同的通知类型方法，在这些方法中，通过 JoinPoint 参数可以获得目标对象的类名、目标方法名和目标方法参数等。需要注意的是，环绕通知必须接收一个类型为 ProceedingJoinPoint 的参数，返回值必须是 Object 类型，且必须抛出异常。异常通知中可以传入 Throwable 类型的参数，用于输出异常信息。

#### 3. 创建 Spring 配置文件

在 com.mengma.aspectj.xml 包下创建 applicationContext.xml 的配置文件，如下所示。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"    
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xmlns:aop="http://www.springframework.org/schema/aop"    
xsi:schemaLocation="http://www.springframework.org/schema/beans            
    http://www.springframework.org/schema/beans/spring-beans-2.5.xsd              
    http://www.springframework.org/schema/aop            
    http://www.springframework.org/schema/aop/spring-aop-2.5.xsd">   
<!--目标类 -->    
<bean id="customerDao" class="com.mengma.dao.CustomerDaoImpl" />    
<!--切面类 -->    
<bean id="myAspect" class="com.mengma.aspectj.xml.MyAspect"></bean>    
<!--AOP 编程 -->    
<aop:config>        
    <aop:aspect ref="myAspect">            
        <!-- 配置切入点，通知最后增强哪些方法 -->            
        <aop:pointcut expression="execution ( * com.mengma.dao.*.* (..))" 
        	id="myPointCut" />            
        <!--前置通知，关联通知 Advice和切入点PointCut -->            
        <aop:before method="myBefore" pointeut-ref="myPointCut" />            
        <!--后置通知，在方法返回之后执行，就可以获得返回值returning 属性 -->            
        <aop:after-returning method="myAfterReturning"  
        	pointcut-ref="myPointCut" returning="returnVal" />           
        <!--环绕通知 -->           
        <aop:around method="myAround" pointcut-ref="myPointCut" />            
        <!--抛出通知：用于处理程序发生异常，可以接收当前方法产生的异常 -->            
        <!-- *注意：如果程序没有异常，则不会执行增强 -->           
        <!-- * throwing属性：用于设置通知第二个参数的名称，类型Throwable -->            
        <aop:after-throwing method="myAfterThrowing"  
        	pointcut-ref="myPointCut" throwing="e" />            
        <!--最终通知：无论程序发生任何事情，都将执行 -->            
        <aop:after method="myAfter" pointcut-ref="myPointCut" />        
    </aop:aspect>    
</aop:config>
</beans>
```

上述代码中，首先在第 4、7、8 行代码中分别导入了 AOP 的命名空间。第 12 行代码指定了切面类。

第 17、18 行代码配置了切入点，通知需要增强哪些方法，expression="execution（*com.mengma.dao.*.*（..））的意思是增强 com.mengma.dao 包下所有的方法。

第 20～32 行代码用于关联通知（Advice）和切入点（PointCut）。以第 20 行代码前置通知为例，<aop:before> 标签的 method 属性用于指定通知，pointcut-ref 属性用于指定切入点，也就是要增强的方法，其他几种通知的配置可以参考代码注释。

#### 4. 创建测试类

在 com.mengma.aspectj.xml 包下创建测试类 XMLTest，如下所示。

```
package com.mengma.aspectj.xml;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.mengma.dao.CustomerDao;

public class XMLTest {    
    @Test    
    public void test() {        
        String xmlPath = "com/mengma/aspectj/xml/applicationContext.xml";        
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);        
        // 从spring容器获取实例        
        CustomerDao customerDao = (CustomerDao) applicationContext.getBean("customerDao");       
        // 执行方法        
        customerDao.add();    
    }
}
```

#### 5. 运行项目并查看结果

使用 JUnit 测试运行 test() 方法，运行成功后，控制台的输出结果如图 1 所示。



![运行结果](http://c.biancheng.net/uploads/allimg/190702/5-1ZF2111045964.png)
图 1 运行结果


为了更好地演示异常通知，接下来在 CustomerDaoImpl 类的 add() 方法中添加一行会抛出异常的代码，如“int i=1/0；”，重新运行 XMLTest 测试类，可以看到异常通知执行了，此时控制台的输出结果如图 2 所示。



![运行结果](http://c.biancheng.net/uploads/allimg/190702/5-1ZF2111305930.png)
图 2 运行结果


从图 1 和图 2 的输出结果中可以看出，基于 XML 声明式的 AOP 开发已经成功实现。



## 基于 Annotation 的声明式

在 Spring 中，尽管使用 XML 配置文件可以实现 AOP 开发，但是如果所有的相关的配置都集中在配置文件中，势必会导致 XML 配置文件过于臃肿，从而给维护和升级带来一定的困难。

为此，AspectJ 框架为 AOP 开发提供了另一种开发方式——基于 Annotation 的声明式。AspectJ 允许使用注解定义切面、切入点和增强处理，而 Spring 框架则可以识别并根据这些注解生成 AOP 代理。

关于 Annotation 注解的介绍如表 1 所示。

| 名称            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| @Aspect         | 用于定义一个切面。                                           |
| @Before         | 用于定义前置通知，相当于 BeforeAdvice。                      |
| @AfterReturning | 用于定义后置通知，相当于 AfterReturningAdvice。              |
| @Around         | 用于定义环绕通知，相当于MethodInterceptor。                  |
| @AfterThrowing  | 用于定义抛出通知，相当于ThrowAdvice。                        |
| @After          | 用于定义最终final通知，不管是否异常，该通知都会执行。        |
| @DeclareParents | 用于定义引介通知，相当于IntroductionInterceptor（不要求掌握）。 |


下面使用注解的方式重新实现《基于XML的声明式》部分的功能。

#### 1. 创建切面类 MyAspect

在 src 目录下创建一个名为 com.mengma.aspectj.annotation 的包，在该包下创建一个切面类 MyAspect，如下所示。

```
package com.mengma.aspectj.annotation;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

//切面类
@Aspect
@Component
public class MyAspect {    
    // 用于取代：<aop:pointcut    
    // expression="execution(*com.mengma.dao..*.*(..))" id="myPointCut"/>    
    // 要求：方法必须是private，没有值，名称自定义，没有参数    

    @Pointcut("execution(*com.mengma.dao..*.*(..))")    
    private void myPointCut() {    }    

    // 前置通知    
    @Before("myPointCut()")    
        public void myBefore(JoinPoint joinPoint) {        
        System.out.print("前置通知，目标：");        
        System.out.print(joinPoint.getTarget() + "方法名称:");        
        System.out.println(joinPoint.getSignature().getName());    
    }   

    // 后置通知    
    @AfterReturning(value = "myPointCut()")    
    public void myAfterReturning(JoinPoint joinPoint) {        
            System.out.print("后置通知，方法名称：" + joinPoint.getSignature().getName());    
        }    
    // 环绕通知    
    @Around("myPointCut()")    
    public Object myAround(ProceedingJoinPoint proceedingJoinPoint)  throws Throwable {       
            System.out.println("环绕开始"); // 开始        
            Object obj = proceedingJoinPoint.proceed(); // 执行当前目标方法        
            System.out.println("环绕结束");  // 结束        
            return obj;    
        }    

    // 异常通知    
    @AfterThrowing(value = "myPointCut()", throwing = "e")    
    public void myAfterThrowing(JoinPoint joinPoint, Throwable e) {        
          System.out.println("异常通知" + "出错了" + e.getMessage());    
   }    
   // 最终通知    
   @After("myPointCut()")    
   public void myAfter() {        
      System.out.println("最终通知");   
  }
}
```

上述代码中， @Aspect 注解用于声明这是一个切面类，该类作为组件使用，所以要添加 @Component 注解才能生效。 @Poincut 注解用于配置切入点，取代 XML 文件中配置切入点的代码。

在每个通知相应的方法上都添加了注解声明，并且将切入点方法名“myPointCut”作为参数传递给要执行的方法，如需其他参数（如异常通知的异常参数），可以根据代码提示传递相应的属性值。

#### 2. 为目标类添加注解

在 com.mengma.dao.CustomerDaoImpl 目标类中添加注解 @Repository（"customerDao"）。

#### 3. 创建Spring配置文件

在 com.mengma.aspectj.annotation 包下创建 applicationContext.xml 配置文件，如下所示。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"    
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    
xmlns:aop="http://www.springframework.org/schema/aop"    
xmlns:context="http://www.springframework.org/schema/context"    
xsi:schemaLocation="http://www.springframework.org/schema/beans           
    http://www.springframework.org/schema/beans/spring-beans-2.5.xsd           
    http://www.springframework.org/schema/aop           
    http://www.springframework.org/schema/aop/spring-aop-2.5.xsd           
    http://www.springframework.org/schema/context            
    http://www.springframework.org/schema/context/spring-context.xsd">    
    <!--扫描含com.mengma包下的所有注解-->    
    <context:component-scan base-package="com.mengma"/>    
    <!-- 使切面开启自动代理 -->    
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```

上述代码中，首先导入了 AOP 命名空间及其配套的约束，使切面类中的 @AspectJ 注解能够正常工作；第 13 行代码添加了扫描包，使注解生效。需要注意的是，这里还包括目标类 com.mengma.dao.CustomerDaoImpl 的注解，所以 base-package 的值为 com.mengma；第 15 行代码的作用是切面开启自动代理。

#### 4. 创建测试类

在 com.mengma.aspectj.annotation 包下创建一个名为 AnnotationTest 的测试类，如下所示。

```
package com.mengma.aspectj.annotation;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.mengma.dao.CustomerDao;

public class AnnotationTest {    
	@Test    
	public void test() {        
        String xmlPath = "com/mengma/aspectj/xml/applicationContext.xml";        
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);        
        // 从spring容器获取实例       
        CustomerDao customerDao = (CustomerDao) applicationContext.getBean("customerDao");        
        // 执行方法        
        customerDao.add();    
	}
}
```

#### 5. 运行项目并查看结果

使用 JUnit 测试运行 test() 方法，运行成功后，控制台的输出结果如图 3 所示。



![运行结果](http://c.biancheng.net/uploads/allimg/190702/5-1ZF21H105L4.png)
图 3 运行结果


删除 add() 方法中的“int i=1/0；”，重新运行 test() 方法，此时控制台的输出结果如图 4 所示。



![运行结果](http://c.biancheng.net/uploads/allimg/190702/5-1ZF21H35N39.png)
图 4 运行结果


从图 3 和图 4 的输出结果中可以看出，已成功使用 Annotation 的方式实现了 AOP 开发。与其他方式相比，基于 Annotation 方式实现 AOP 的效果是最方便的方式，所以实际开发中推荐使用注解的方式。



# Spring JDBCTemplate简介

[Spring](http://c.biancheng.net/spring/) 框架针对数据库开发中的应用提供了 JDBCTemplate 类，该类是 Spring 对 JDBC 支持的核心，它提供了所有对数据库操作功能的支持。

Spring 框架提供的JDBC支持主要由四个包组成，分别是 core（核心包）、object（对象包）、dataSource（数据源包）和 support（支持包），org.springframework.jdbc.core.JdbcTemplate 类就包含在核心包中。作为 Spring JDBC 的核心，JdbcTemplate 类中包含了所有数据库操作的基本方法。

JdbcTemplate 类继承自抽象类 JdbcAccessor，同时实现了 JdbcOperations 接口。其直接父类 JdbcAccessor 为子类提供了一些访问数据库时使用的公共属性，具体介绍如下。

#### 1）DataSource

其主要功能是获取数据库连接，具体实现时还可以引入对数据库连接的缓冲池和分布式事务的支持，它可以作为访问数据库资源的标准接口。

#### 2）SQLExceptionTranslator

org.springframework.jdbc.support.SQLExceptionTranslator 接口负责对 SQLException 进行转译工作。通过必要的设置或者获取 SQLExceptionTranslator 中的方法，可以使 JdbcTemplate 在需要处理 SQLException 时，委托 SQLExceptionTranslator 的实现类完成相关的转译工作。

JdbcOperations 接口定义了在 JdbcTemplate 类中可以使用的操作集合，包括添加、修改、查询和删除等操作。

Spring 中 JDBC 的相关信息是在 Spring 配置文件中完成的，其配置模板如下所示：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"    
xmlns:xsi="http:/www.w3.org/2001/XMLSchema-instance"    
xsi:schemaLocation="http://www.springframework.org/schema/beans    
http://www.springframework.org/schema/beans/spring-beans.xsd">        
    <!-- 配置数据源 -->     
    <bean id="dataSource" class="org.springframework.jdbc.dataSource.DriverManagerDataSource">        
    	<!--数据库驱动-->        
        <property name="driverClassName" value="com.mysql.jdbc.Driver" />         
        <!--连接数据库的url-->        
        <property name= "url" value="jdbc:mysql://localhost/spring" />        
        <!--连接数据库的用户名-->        
        <property name="username" value="root" />        
        <!--连接数据库的密码-->        
        <property name="password" value="root" />    
    </bean>    
    <!--配置JDBC模板-->    
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.jdbcTemplate">        
        <!--默认必须使用数据源-->        
        <property name="dataSource" ref="dataSource"/>    
    </bean>    
    <!--配置注入类-->    
    <bean id="xxx" class="xxx">        
        <property name="jdbcTemplate" ref="jdbcTemplate"/>    
    </bean>    
    ...
</beans>
```

在上述代码中，定义了三个 Bean，分别是 dataSource、jdbcTemplate 和需要注入类的 Bean。其中 dataSource 对应的是 DriverManagerDataSource 类，用于对数据源进行配置；jdbcTemplate 对应 JdbcTemplate 类，该类中定义了 JdbcTemplate 的相关配置。

在 dataSource 中，定义了四个连接数据库的属性，如表 1 所示。



| 属性名          | 含义                                            |
| --------------- | ----------------------------------------------- |
| driverClassName | 所使用的驱动名称，对应驱动 JAR 包中的 Driver 类 |
| url             | 数据源所在地址                                  |
| username        | 访问数据库的用户名                              |
| password        | 访问数据库的密码                                |


表 1 中的属性值需要根据数据库类型或者机器配置的不同进行相应设置。如果数据库类型不同，则需要更改驱动名称。如果数据库不在本地，则需要将 localhost 替换成相应的主机 IP。

在定义 jdbcTemplate 时，需要将 dataSource 注入 jdbcTemplate 中。而在其他的类中要使用 jdbcTemplate，也需要将 jdbcTemplate 注入使用类中（通常注入 dao 类中）。

在 JdbcTemplate 类中，提供了大量的查询和更新数据库的方法，如 query()、update() 等。关于这些方法的具体使用，将在下一节的案例中讲解，此处读者了解即可。



# Spring事务管理接口：PlatformTransactionManager、TransactionDefinition和TransactionStatus

[Spring](http://c.biancheng.net/spring/) 的事务管理是基于 AOP 实现的，而 AOP 是以方法为单位的。Spring 的事务属性分别为传播行为、隔离级别、只读和超时属性，这些属性提供了事务应用的方法和描述策略。

在 [Java](http://c.biancheng.net/java/) EE 开发经常采用的分层模式中，Spring 的事务处理位于业务逻辑层，它提供了针对事务的解决方案。

在 Spring 解压包的 libs 目录中，包含一个名称为 spring-tx-3.2.13.RELEASE.jar 的文件，该文件是 Spring 提供的用于事务管理的 JAR 包，其中包括事务管理的三个核心接口：PlatformTransactionManager、TransactionDefinition 和 TransactionStatus。

将该 JAR 包的后缀名 jar 改成 zip 的形式后，解压压缩包，进入解压文件夹中的 \org\springframework\transaction 目录后，该目录中的文件如图 1 所示。

![事务管理核心接口](http://c.biancheng.net/uploads/allimg/190702/5-1ZF214555W93.png)
图 1 事务管理核心接口


在图 1 中，方框所标注的三个文件就是本节将要讲解的核心接口。这三个核心接口的作用及其提供的方法如下。

#### 1. PlatformTransactionManager

PlatformTransactionManager 接口是 Spring 提供的平台事务管理器，用于管理事务。该接口中提供了三个事务操作方法，具体如下。

- TransactionStatus getTransaction（TransactionDefinition definition）：用于获取事务状态信息。
- void commit（TransactionStatus status）：用于提交事务。
- void rollback（TransactionStatus status）：用于回滚事务。


在项目中，Spring 将 xml 中配置的事务详细信息封装到对象 TransactionDefinition 中，然后通过事务管理器的 getTransaction() 方法获得事务的状态（TransactionStatus），并对事务进行下一步的操作。

#### 2. TransactionDefinition

TransactionDefinition 接口是事务定义（描述）的对象，它提供了事务相关信息获取的方法，其中包括五个操作，具体如下。

- String getName()：获取事务对象名称。
- int getIsolationLevel()：获取事务的隔离级别。
- int getPropagationBehavior()：获取事务的传播行为。
- int getTimeout()：获取事务的超时时间。
- boolean isReadOnly()：获取事务是否只读。


在上述五个方法的描述中，事务的传播行为是指在同一个方法中，不同操作前后所使用的事务。传播行为的种类如表 1 所示。



| 属性名称                  | 值            | 描 述                                                        |
| ------------------------- | ------------- | ------------------------------------------------------------ |
| PROPAGATION_REQUIRED      | required      | 支持当前事务。如果 A 方法已经在事务中，则 B 事务将直接使用。否则将创建新事务 |
| PROPAGATION_SUPPORTS      | supports      | 支持当前事务。如果 A 方法已经在事务中，则 B 事务将直接使用。否则将以非事务状态执行 |
| PROPAGATION_MANDATORY     | mandatory     | 支持当前事务。如果 A 方法没有事务，则抛出异常                |
| PROPAGATION_REQUIRES_NEW  | requires_new  | 将创建新的事务，如果 A 方法已经在事务中，则将 A 事务挂起     |
| PROPAGATION_NOT_SUPPORTED | not_supported | 不支持当前事务，总是以非事务状态执行。如果 A 方法已经在事务中，则将其挂起 |
| PROPAGATION_NEVER         | never         | 不支持当前事务，如果 A 方法在事务中，则抛出异常              |
| PROPAGATION.NESTED        | nested        | 嵌套事务，底层将使用 Savepoint 形成嵌套事务                  |


在事务管理过程中，传播行为可以控制是否需要创建事务以及如何创建事务。

通常情况下，数据的查询不会改变原数据，所以不需要进行事务管理，而对于数据的增加、修改和删除等操作，必须进行事务管理。如果没有指定事务的传播行为，则 Spring3 默认的传播行为是 required。

#### 3. TransactionStatus

TransactionStatus 接口是事务的状态，它描述了某一时间点上事务的状态信息。其中包含六个操作，具体如表 2 所示。



| 名称                       | 说明               |
| -------------------------- | ------------------ |
| void flush()               | 刷新事务           |
| boolean hasSavepoint()     | 获取是否存在保存点 |
| boolean isCompleted()      | 获取事务是否完成   |
| boolean isNewTransaction() | 获取是否是新事务   |
| boolean isRollbackOnly()   | 获取是否回滚       |
| void setRollbackOnly()     | 设置事务回滚       |



# Spring声明式事务管理（基于XML方式实现）

[Spring](http://c.biancheng.net/spring/) 的事务管理有两种方式：一种是传统的编程式事务管理，即通过编写代码实现的事务管理；另一种是基于 AOP 技术实现的声明式事务管理。由于在实际开发中，编程式事务管理很少使用，所以我们只对 Spring 的声明式事务管理进行详细讲解。

Spring 声明式事务管理在底层采用了 AOP 技术，其最大的优点在于无须通过编程的方式管理事务，只需要在配置文件中进行相关的规则声明，就可以将事务规则应用到业务逻辑中。

Spring 实现声明式事务管理主要有两种方式：

- 基于 XML 方式的声明式事务管理。
- 通过 Annotation 注解方式的事务管理。


本节通过银行转账的案例讲解如何使用 XML 的方式实现 Spring 的声明式事务处理。

#### 1. 创建项目

在 MyEclipse 中创建一个名为 springDemo03 的 Web 项目，将 Spring 支持和依赖的 JAR 包复制到 Web 项目的 lib 目录中，并添加到类路径下。所添加的 JAR 包如图 1 所示。



![需要导入的JAR包](http://c.biancheng.net/uploads/allimg/190702/5-1ZF2151551605.png)
图 1 需要导入的JAR包


从图 1 中可以看出，这里增加导入了 spring-tx-3.2.13.RELEASE.jar（事务管理），以及 [MySQL](http://c.biancheng.net/mysql/) 驱动、JDBC 和 C3P0 的 JAR 包。

#### 2. 创建数据库、表以及插入数据

在 MySQL 中创建一个名为 spring 的数据库，然后在该数据库中创建一个 account 表，并向表中插入两条数据，其 SQL 执行语句如下所示：

```
CREATE DATABASE spring;USE spring;CREATE TABLE account (    id INT (11) PRIMARY KEY AUTO_INCREMENT,    username VARCHAR(20) NOT NULL,    money INT DEFAULT NULL);INSERT INTO account VALUES (1,'zhangsan',1000);INSERT INTO account VALUES (2,'lisi',1000);
```

执行后的 account 表中的数据如图 2 所示。

![执行结果](http://c.biancheng.net/uploads/allimg/190702/5-1ZF2151GY63.PNG)
图 2 执行结果

#### 3. 创建 c3p0-db.properties

在项目的 src 下创建一个名为 c3p0-db.properties 的配置文件，这里使用 C3P0 数据源，需要在该文件中添加如下配置：

jdbc.driverClass = com.mysql.jdbc.Driver
jdbc.jdbcUrl = jdbc:mysql://localhost:3306/spring
jdbc.user = root
jdbc.password = root

#### 4. 实现 DAO

#### 1）创建 AccountDao 接口

在项目的 src 目录下创建一个名为 com.mengma.dao 的包，在该包下创建一个接口 AccountDao，并在接口中创建汇款和收款的方法，如下所示。

```
package com.mengma.dao;

public interface AccountDao {    
    // 汇款    
    public void out(String outUser, int money);    
    // 收款    
    public void in(String inUser, int money);
}
```

上述代码中，定义了 out() 和 in() 两个方法，分别用于表示汇款和收款。

#### 2）创建DAO层接口实现类

在项目的 src 目录下创建一个名为 com.mengma.dao.impl 的包，在该包下创建实现类 AccountDaoImpl，如下所示。

```
package com.mengma.dao.impl;

import org.springframework.jdbc.core.JdbcTemplate;
import com.mengma.dao.AccountDao;

public class AccountDaoImpl implements AccountDao {    
    private JdbcTemplate jdbcTemplate;    
    public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {        
   	 this.jdbcTemplate = jdbcTemplate;    
    }    
    // 汇款的实现方法    
    public void out(String outUser, int money) {        
        this.jdbcTemplate.update("update account set money =money-?"                
        + "where username =?", money, outUser);    
    }    
    // 收款的实现方法   
    public void in(String inUser, int money) {        
        this.jdbcTemplate.update("update account set money =money+?"                
        + "where username =?", money, inUser);    
    }
}
```

上述代码中，使用 JdbcTemplate 类的 update() 方法实现了更新操作。

#### 5. 实现 Service

#### 1）创建 Service 层接口

在项目的 src 目录下创建一个名为 com.mengma.service 的包，在该包下创建接口 AccountService，如下所示。

```
package com.mengma.service;

public interface AccountService {    
    // 转账    
    public void transfer(String outUser, String inUser, int money);
}
```

#### 2）创建 Service 层接口实现类

在项目的 src 目录下创建一个名为 com.mengma.service.impl 的包，在该包下创建实现类 AccountServiceImpl，如下所示。

```
package com.mengma.service.impl;

import com.mengma.dao.AccountDao;

public class AccountServiceImpl {    
	private AccountDao accountDao;   
    
    public void setAccountDao(AccountDao accountDao) {        
    	this.accountDao = accountDao;    
    }    
    public void transfer(String outUser, String inUser, int money) {        
        this.accountDao.out(outUser, money);        
        this.accountDao.in(inUser, money);    
    }
}
```

上述代码中可以看出，该类实现了 AccountService 接口，并对转账的方法进行了实现，根据参数的不同调用 DAO 层相应的方法。

#### 6. 创建 Spring 配置文件

在项目的 src 目录下创建 Spirng 配置文件 applicationContext.xml，编辑后如下所示。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"    
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    
xmlns:context="http://www.springframework.org/schema/context"   
xmlns:tx="http://www.springframework.org/schema/tx"    
xmlns:aop="http://www.springframework.org/schema/aop"    
xsi:schemaLocation="http://www.springframework.org/schema/beans            
http://www.springframework.org/schema/beans/spring-beans-2.5.xsd             
http://www.springframework.org/schema/context            
http://www.springframework.org/schema/context/spring-context.xsd          
http://www.springframework.org/schema/tx         
http://www.springframework.org/schema/tx/spring-tx-2.5.xsd           
http://www.springframework.org/schema/aop           
http://www.springframework.org/schema/aop/spring-aop-2.5.xsd">   
	<!-- 加载properties文件 -->   
	<context:property-placeholder location="classpath:c3p0-db.properties" />    
	<!-- 配置数据源，读取properties文件信息 -->    
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">       
        <property name="driverClass" value="${jdbc.driverClass}" />       
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}" />      
        <property name="user" value="${jdbc.user}" />      
        <property name="password" value="${jdbc.password}" />   
    </bean>    
    <!-- 配置jdbc模板 -->  
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">     
   		<property name="dataSource" ref="dataSource" />   
    </bean>    
	<!-- 配置dao -->   
    <bean id="accountDao" class="com.mengma.dao.impl.AccountDaoImpl">   
    	<property name="jdbcTemplate" ref="jdbcTemplate" />   
    </bean>    
    <!-- 配置service -->   
    <bean id="accountService" class="com.mengma.service.impl.AccountServiceImpl">      
    	<property name="accountDao" ref="accountDao" />  
    </bean>    
	<!-- 事务管理器，依赖于数据源 -->  
    <bean id="txManager"  class="org.springframework.jdbc.datasource.DataSourceTransactionManager">       
    	<property name="dataSource" ref="dataSource" />    
    </bean>   
	<!-- 编写通知：对事务进行增强（通知），需要编写切入点和具体执行事务的细节 -->   
    <tx:advice id="txAdvice" transaction-manager="txManager">       
        <tx:attributes>           
            <!-- 给切入点方法添加事务详情，name表示方法名称，*表示任意方法名称，propagation用于设置传播行为，read-only表示隔离级别，是否只读 -->
            <tx:method name="find*" propagation="SUPPORTS"      	         
        		rollback-for="Exception" />           
        	<tx:method name="*" propagation="REQUIRED" isolation="DEFAULT"          
        		read-only="false" />     
        </tx:attributes>   
    </tx:advice>    
	<!-- aop编写，让Spring自动对目标生成代理，需要使用AspectJ的表达式 -->    
    <aop:config>      
        <!-- 切入点 -->      
        <aop:pointcut expression="execution(* com.mengma.service.*.*(..))"          
        id="txPointCut" />       
        <!-- 切面：将切入点与通知整合 -->        
        <aop:advisor pointcut-ref="txPointCut" advice-ref="txAdvice" />   
    </aop:config>
</beans>
```

上述代码中，首先在 <beans> 标记的第 6、13 和 14 行代码分别添加了 AOP 所需的命名空间声明。第 42～50 行代码使用 <tx:advice> 标记配置事务通知内容。

第 52～58 行代码使用 <aop:config> 标记定义切面，其中第 54 行代码应用了 AspectJ 表达式，代表 com.mengma.service 包下所有类的所有方法都应用事务规则，第 57 行代码使用 <aop:advistor> 标记将切入点与事务通知整合，基于 AOP 的声明式事务配置完成。

#### 7. 创建测试类

在项目的 src 目录下创建 com.mengma.test 的包，在该包下创建测试类 AccountTest，如下所示。

```
package com.mengma.test;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.mengma.service.AccountService;

public class AccountTest {    
    @Test    
    public void test() {        
        // 获得Spring容器，并操作       
        String xmlPath = "applicationContext.xml";        
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);        
        AccountService accountService = (AccountService) applicationContext.getBean("accountService");  
        accountService.transfer("zhangsan", "lisi", 100);    
    }
}
```

上述代码中模拟了银行转账业务，从 zhangsan 的账户向 lisi 的账户中转入 100 元。使用 JUnit 测试运行 test() 方法，运行成功后，查询 account 表，如图 3 所示。

从图 3 的查询结果中可以看出，zhangsan 成功向 lisi 转账 100 元。



![查询结果](http://c.biancheng.net/uploads/allimg/190702/5-1ZF216321Q37.PNG)
图 3 查询结果


下面通过修改案例模拟转账失败的情况，在的 transfer() 方法中添加一行代码“int i=1/0；”模拟系统断电的情况，具体代码如下所示：

```
public void transfer(String outUser, String inUser, int money) {    
    this.accountDao.out(outUser, money);    
    //模拟断电    
    int i = 1/0;    
    this.accountDao.in(inUser, money);
 }
```

重新测试运行 test() 方法，JUnit 控制台输出的信息如图 4 所示。



![控制台输出结果](http://c.biancheng.net/uploads/allimg/190702/5-1ZF2163P15Q.png)
图 4 控制台输出结果


从图 4 中可以看出，在执行测试方法时，出现了除以 0 的异常信息。此时再次查询 account 表，其查询结果如图 5 所示。

从图 5 的查询结果中可以看出，表中的数据并没有发生变化。由于程序在执行过程中抛出了异常，事务不能正常被提交，所以转账失败。由此可知，Spring 的事务管理生效了。



![查询结果](http://c.biancheng.net/uploads/allimg/190702/5-1ZF216321Q37.PNG)
图 5 查询结果



# Spring声明式事务管理（基于Annotation注解方式实现）

在 [Spring](http://c.biancheng.net/spring/) 中，除了使用基于 XML 的方式可以实现声明式事务管理以外，还可以通过 Annotation 注解的方式实现声明式事务管理。

使用 Annotation 的方式非常简单，只需要在项目中做两件事，具体如下。

#### 1）在 Spring 容器中注册驱动，代码如下所示：

<tx:annotation-driven transaction-manager="txManager"/>

#### 2）在需要使用事务的业务类或者方法中添加注解 @Transactional，并配置 @Transactional 的参数。关于 @Transactional 的参数如图 1 所示。

![@Transactional参数列表](http://c.biancheng.net/uploads/allimg/190703/5-1ZF3092H4F3.png)
图 1 @Transactional参数列表


下面通过修改《[Spring基于XML实现事务管理](http://c.biancheng.net/view/4287.html)》教程中银行转账的案例讲解如何使用 Annotation 注解的方式实现 Spring 声明式事务管理。

#### 1. 注册驱动

修改 Spring 配置文件 applicationContext.xml，修改后如下所示。

```
<?xml version="1.0" encoding="UTF-8"?><beans xmlns="http://www.springframework.org/schema/beans"    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    
xmlns:context="http://www.springframework.org/schema/context"    
xmlns:tx="http://www.springframework.org/schema/tx"   
xmlns:aop="http://www.springframework.org/schema/aop"    
xsi:schemaLocation="http://www.springframework.org/schema/beans           
            http://www.springframework.org/schema/beans/spring-beans-2.5.xsd             
            http://www.springframework.org/schema/context            
            http://www.springframework.org/schema/context/spring-context.xsd           
            http://www.springframework.org/schema/tx           
            http://www.springframework.org/schema/tx/spring-tx-2.5.xsd           
            http://www.springframework.org/schema/aop            
            http://www.springframework.org/schema/aop/spring-aop-2.5.xsd">    
    <!-- 加载properties文件 -->    
    <context:property-placeholder location="classpath:c3p0-db.properties" />    
    <!-- 配置数据源，读取properties文件信息 -->    
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">       
        <property name="driverClass" value="${jdbc.driverClass}" />        
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}" />       
        <property name="user" value="${jdbc.user}" />      
        <property name="password" value="${jdbc.password}" />    
    </bean>    
    <!-- 配置jdbc模板 -->   
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">       
   	 <property name="dataSource" ref="dataSource" />   
    </bean>    
    <!-- 配置dao -->   
    <bean id="accountDao" class="com.mengma.dao.impl.AccountDaoImpl">      
    	<property name="jdbcTemplate" ref="jdbcTemplate" />   
    </bean>   
    <!-- 配置service -->  
    <bean id="accountService" class="com.mengma.service.impl.AccountServiceImpl">      
    	<property name="accountDao" ref="accountDao" />  
    </bean>   
    <!-- 事务管理器，依赖于数据源 -->   
    <bean id="txManager"       
    		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">       
    	<property name="dataSource" ref="dataSource" />    
    </bean>    
    <!-- 注册事务管理驱动 -->    
    <tx:annotation-driven transaction-manager="txManager"/>
</beans>
```


上述代码中可以看出，与原来的配置文件相比，这里只修改了事务管理器部分，新添加并注册了事务管理器的驱动。

需要注意的是，在学习 AOP 注解方式开发时，需要在配置文件中开启注解处理器，指定扫描哪些包下的注解，这里没有开启注解处理器是因为在第 33～35 行手动配置了 AccountServiceImpl，而 @Transactional 注解就配置在该类中，所以会直接生效。

#### 2. 添加 @Transactional 注解

修改 AccountServiceImpl，在文件中添加 @Transactional 注解及参数，添加后如下所示。

```
package com.mengma.service.impl;

import org.springframework.transaction.annotation.Isolation;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;
import com.mengma.dao.AccountDao;

@Transactional(propagation = Propagation.REQUIRED, isolation = Isolation.DEFAULT, readOnly = false)
public class AccountServiceImpl {   
    private AccountDao accountDao;   

    public void setAccountDao(AccountDao accountDao) {        
   	 this.accountDao = accountDao;   
    }    
    
    public void transfer(String outUser, String inUser, int money) {     
        this.accountDao.out(outUser, money);       
        // 模拟断电       
        int i = 1 / 0;        
        this.accountDao.in(inUser, money);   
	}
}
```

需要注意的是，在使用 @Transactional 注解时，参数之间用“，”进行分隔。

使用 JUnit 测试再次运行 test() 方法时，控制台同样会输出如图 2 所示的异常信息，这说明使用基于 Annotation 注解的方式同样实现了 Spring 的声明式事务管理。如果注释掉模拟断电的代码进行测试，则转账操作可以正常完成。



![运行结果](http://c.biancheng.net/uploads/allimg/190702/5-1ZF2163P15Q.png)
图 2 运行结果