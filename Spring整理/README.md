<<<<<<< HEAD
## **1. spring框架概述**[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_0)

### 1.1 什么是spring[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_1)

- Spring是一个开源框架，Spring是于2003 年兴起的一个轻量级的Java 开发框架，由Rod Johnson 在其著作Expert One-On-One J2EE Development and Design中阐述的部分理念和原型衍生而来。它是为了解决企业应用开发的复杂性而创建的。框架的主要优势之一就是其分层架构，分层架构允许使用者选择使用哪一个组件，同时为 J2EE 应用程序开发提供集成的框架。Spring使用基本的JavaBean来完成以前只可能由EJB完成的事情。然而，Spring的用途不仅限于服务器端的开发。从简单性、可测试性和松耦合的角度而言，任何Java应用都可以从Spring中受益。Spring的核心是控制反转（IoC）和面向切面（AOP）。简单来说，Spring是一个分层的JavaSE/EE full-stack(一站式) 轻量级开源框架。 [^摘自]: (百度百科)
- 轻量级：与EJB对比，依赖资源少，销毁的资源少。
- 分层： 一站式，每一个层都提供的解决方案

### 1.2 spring由来[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_2)

- Expert One-to-One J2EE Design and Development
- Expert One-to-One J2EE Development without EJB

### 1.3 spring核心[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_3)

- Spring的核心是 **控制反转（IoC）** 和 **面向切面（AOP）**

### 1.4 spring优点[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_4)

- 方便解耦，简化开发 （高内聚低耦合）
  - Spring就是一个大工厂（容器），可以将所有对象创建和依赖关系维护，交给Spring管理
  - spring工厂是用于生成bean
- AOP编程的支持
  - Spring提供面向切面编程，可以方便的实现对程序进行权限拦截、运行监控等功能
- 声明式事务的支持
  - 只需要通过配置就可以完成对事务的管理，而无需手动编程
- 方便程序的测试
  - Spring对Junit4支持，可以通过注解方便的测试Spring程序
- 方便集成各种优秀框架
  - Spring不排斥各种优秀的开源框架，其内部提供了对各种优秀框架（如：Struts、Hibernate、MyBatis、Quartz等）的直接支持
- 降低JavaEE API的使用难度
  - Spring 对JavaEE开发中非常难用的一些API（JDBC、JavaMail、远程调用等），都提供了封装，使这些API应用难度大大降低

### 1.5 spring体系结构[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_5)

- Spring框架是一个分层架构，它包含一系列的功能要素并被分为大约20个模块。这些模块分为Core Container、Data Access/Integration、Web、AOP（Aspect Oriented Programming）、Instrumentation的测试部分，如图所示：![Spring体系结构图](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716093445114-344400221.png)
- 核心容器：beans、core、context、expression

## **2. 入门案例：IoC**[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_6)

### 2.1 导入jar包[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_7)

- 在写案例之前，需要导入5个jar包，4+1：4个核心（beans、core、context、expression） + 1个依赖（commons-loggins...jar)![5个jar包](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716093714897-656039556.png)

### 2.2 目标类[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_8)

- 提供UserService接口和实现类
- 提供UserService接口和实现类
  之前开发中，直接new一个对象即可。
  学习spring之后，将由Spring创建对象实例--> IoC 控制反转（Inverse of Control）之后需要实例对象时，从spring工厂（容器）中获得，需要将实现类的全限定名称配置到xml文件中。

```java
Copy    public interface UserService {

        public void addUser();
    }
    public class UserServiceImpl implements UserService{
        @Override
        public void addUser() {
            System.out.println("add a User");
        }
    }
```

### 2.3 配置文件[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_9)

- 位置：任意，开发中一般在classpath下（src）
- 名称：任意，开发中常用applicationContext.xml
- 内容：添加schema约束
  约束文件位置：spring-framework-xxx.RELEASE\docs\spring-framework-reference\html\xsd-config.html

```xml
Copy    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
                               http://www.springframework.org/schema/beans/spring-beans.xsd">
        <!-- 配置service 
		    <bean> 配置需要创建的对象
			    id ：用于之后从spring容器获得实例时使用的
			    class ：需要创建实例的全限定类名
	    -->
        <bean id="userService" class="com.springlearning.ioc.UserServiceImpl"></bean>
</beans>
```

### 2.4 测试[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_10)

```java
Copy        @Test
	public void demo02(){
		//从spring容器获得
		//1 获得容器
		String xmlPath = "com/springlearning/ioc/beans.xml";
		ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);
		//2获得内容 --不需要自己new，都是从spring容器获得
		UserService userService = (UserService) applicationContext.getBean("userServiceId");
		userService.addUser();
	}
```

## **3. 入门案例：DI**[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_11)

- DI Dependency Injection,依赖注入
  is a: 是一个，继承。
  has a：有一个，成员变量，依赖。

```java
Copy    class B{  
        private A a; //B类依赖A类  
    }  
```

依赖：一个对象需要使用另外一个对象
注入：通过setter方法进行另一个对象实例设置

- 例如:

```java
Copy  class BookServiceImpl{
        //之前开发：接口 = 实现类  （service和dao耦合）
	//private BookDao bookDao = new BookDaoImpl();
 	//spring之后 （解耦：service实现类使用dao接口，不知道具体的实现类）
	private BookDao bookDao;
	setter方法
   }
```

模拟spring执行过程
创建service实例：BookService bookService = new BookServiceImpl() -->IoC <bean>
创建dao实例：BookDao bookDao = new BookDaoImple() -->IoC
将dao设置给service：bookService.setBookDao(bookDao); -->DI <property>

### 3.1 目标类[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_12)

- 创建BookService接口和实现类
- 创建BookDao接口和实现类
- 将dao和service配置 xml文件
- 使用api测试

#### 3.1.1 dao[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_13)

```java
Copypublic interface BookDao {

    public void addBook();
}
public class BookDaoImpl implements BookDao {
    @Override
    public void addBook() {
        System.out.println("add a book");
    }
}
```

#### 3.1.2 service[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_14)

```java
Copypublic interface BookService {
    void addBook();
}
public class BookServiceImpl implements BookService {
    //方式1: 之前 ， 接口=实现类
    //private BookDao bookDao = new BookDaoImpl();
    //方式2: 接口+ setter
    private BookDao bookDao;
    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
    @Override
    public void addBook() {
        bookDao.addBook();
    }
}
```

### 3.2 配置文件[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_15)

```xml
Copy<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--
	模拟spring执行过程
		创建service实例：BookService bookService = new BookServiceImpl()	IoC  <bean>
		创建dao实例：BookDao bookDao = new BookDaoImpl()			IoC
		将dao设置给service：bookService.setBookDao(bookDao);		DI   <property>

		<property> 用于进行属性注入
			name： bean的属性名，通过setter方法获得
				setBookDao ##> BookDao  ##> bookDao
			ref ：另一个bean的id值的引用
	 -->
    <!-- 创建service -->
    <bean id="bookService" class="com.springlearning.di.BookServiceImpl">
        <property name="bookDao" ref="bookDao"></property>
    </bean>
    <!-- 创建dao实例 -->
    <bean id="bookDao" class="com.springlearning.di.BookDaoImpl"></bean>
</beans>
```

### 3.3 测试[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_16)

```java
Copy@Test
public void demo01(){
    String xmlPath = "com/springlearning/di/beans.xml";
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);
    BookService bookService = (BookService) applicationContext.getBean("bookService");
    bookService.addBook();
}
```

## **4. 核心API**[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_17)

- api整体了解，之后不使用，在学习过程需要。
  ![api](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716093808563-1471929885.png)
- BeanFactory ：这是一个工厂，用于生成任意bean。
  采取延迟加载，第一次getBean时才会初始化Bean
- ApplicationContext：是BeanFactory的子接口，功能更强大。（国际化处理、事件传递、Bean自动装配、各种不同应用层的Context实现）。当配置文件被加载，就进行对象实例化。
  ClassPathXmlApplicationContext 用于加载classpath（类路径、src）下的xml
  加载xml运行时位置 --> /WEB-INF/classes/...xml
  FileSystemXmlApplicationContext 用于加载指定盘符下的xml
  加载xml运行时位置 --> /WEB-INF/...xml
  通过java web ServletContext.getRealPath() 获得具体盘符

```java
Copy@Test
    public void demo02(){
        String xmlPath = "com/springlearning/di/beans.xml";
        BeanFactory beanFactory = new XmlBeanFactory(new ClassPathResource(xmlPath));
        BookService bookService = (BookService) beanFactory.getBean("bookService");
        bookService.addBook();
    }
```

## **5. 装配Bean 基于XML**[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_18)

### 5.1 实例化方式[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_19)

- 3种bean实例化方式：默认构造、静态工厂、实例工厂

#### 5.1.1 默认构造[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_20)

```xml
Copy<bean id="" class="">  必须提供默认构造
```

#### 5.1.2 静态工厂[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_21)

- 常用与spring整合其他框架（工具）
- 静态工厂：用于生成实例对象，所有的方法必须是static

```xml
Copy<bean id=""  class="工厂全限定类名"  factory-method="静态方法">
```

##### 5.1.2.1 工厂

```java
Copypublic class MyBeanFactory {
    /**
     * 创建实例
     * @return
     */
    public static UserService createService(){
        return new UserServiceImpl();
    }
}
```

##### 5.1.2.2 spring配置

```xml
Copy<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!-- 将静态工厂创建的实例交予spring
		class 确定静态工厂全限定类名
		factory-method 确定静态方法名
	-->
    <bean id="userService" class="com.springlearning.bean_xml.MyBeanFactory" factory-method="createService"></bean>
</beans>
```

#### 5.1.3 实例工厂[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_22)

- 实例工厂：必须先有工厂实例对象，通过实例对象创建对象。提供所有的方法都是“非静态”的。

##### 5.1.3.1 工厂

```java
Copy/**
 * @ClassName：MyBeanFactory
 * @author: donkey-boy
 * @desc 实例工厂，所有方法非静态
 * @date:2019/7/12 14:51
 */
public class MyBeanFactory {
    /**
     * 创建实例
     * @return
     */
    public UserService createService(){
        return new UserServiceImpl();
    }
}
```

##### 5.1.3.2 spring 配置

```xml
Copy<bean id="myBeanFactory" class="com.springlearning.bean_xml.MyBeanFactory"></bean>
    <!-- 获得userservice
        * factory-bean 确定工厂实例
        * factory-bean 确定普通方法
     -->
<bean id="userService" factory-bean="myBeanFactory" factory-method="createService"></bean>
```

### 5.2 Bean 种类[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_23)

- 普通bean：之前操作的都是普通bean。<bean id="" class="A"> ，spring直接创建A实例，并返回
- FactoryBean：是一个特殊的bean，具有工厂生成对象能力，只能生成特定的对象。
  bean必须使用 FactoryBean接口，此接口提供方法 getObject() 用于获得特定bean。
  <bean id="" class="FB"> 先创建FB实例，使用调用getObject()方法，并返回方法的返回值
  FB fb = new FB();
  return fb.getObject();
- BeanFactory 和 FactoryBean 对比？
  BeanFactory：工厂，用于生成任意bean。
  FactoryBean：特殊bean，用于生成另一个特定的bean。例如：ProxyFactoryBean ，此工厂bean用于生产代理。<bean id="" class="....ProxyFactoryBean"> 获得代理对象实例。AOP使用

### 5.3 作用域[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_24)

- 作用域：用于确定spring创建bean实例个数
  ![作用域](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716093833688-679391486.png)
- 取值：
  singleton 单例，默认
  prototype 多例，每执行一次getBean将获得一个实例。例如：struts整合spring，配置action多例。
- 配置信息

```xml
Copy<bean id="" class=""  scope="">
<bean id="userService" class="*" 
		scope="prototype" ></bean>
```

### 5.4 生命周期[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_25)

#### 5.4.1 初始化和销毁[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_26)

- 目标方法执行前后执行后，将进行初始化或销毁。

```xml
Copy<bean id="" class="" init-method="初始化方法名称"  destroy-method="销毁的方法名称">
```

##### 5.4.1.1 目标类

```java
Copypublic class UserServiceImpl implements UserService {
    @Override
    public void addUser() {
        System.out.println("add a User");
    }

    public void myInit(){
        System.out.println("初始化");
    }
    public void myDestroy(){
        System.out.println("销毁");
    }
}
```

##### 5.4.1.2 spring配置

```xml
Copy<!--
	init-method 用于配置初始化方法,准备数据等
	destroy-method 用于配置销毁方法,清理资源等
-->
<bean id="userService" class="com.springlearning.lifecycle.UserServiceImpl" init-method="myInit" destroy-method="myDestroy"></bean>
```

##### 5.4.1.3 测试

```java
Copy@Test
    public void demo02()  {
        String xmlPath = "com/springlearning/lifecycle/beans.xml";
        ClassPathXmlApplicationContext  applicationContext = new ClassPathXmlApplicationContext(xmlPath);
        UserService userService = (UserService) applicationContext.getBean("userService");
        userService.addUser();

        //要求：1.容器必须close，销毁方法执行; 2.必须是单例的
//		applicationContext.getClass().getMethod("close").invoke(applicationContext);
        // * 此方法接口中没有定义，实现类提供
        applicationContext.close();
    }
```

#### 5.4.2 BeanPostProcessor 后处理Bean[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_27)

- spring 提供一种机制，只要实现此接口BeanPostProcessor，并将实现类提供给spring容器，spring容器将自动执行，在初始化方法前执行before()，在初始化方法后执行after() 。 配置<bean class="">
  ![后处理Bean](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716093901328-1700487806.jpg)
- Factory hook(勾子) that allows for custom modification of new bean instances, e.g. checking for marker interfaces or wrapping them with proxies.
- spring提供工厂勾子，用于修改实例对象，可以生成代理对象，是AOP底层。
  模拟
  A a =new A();
  a = B.before(a) --> 将a的实例对象传递给后处理bean，可以生成代理对象并返回。
  a.init();
  a = B.after(a);
  a.addUser(); //生成代理对象，目的在目标方法前后执行（例如：开启事务、提交事务）
  a.destroy()

##### 5.4.2.1 编写实现类

```java
Copypublic class MyBeanPostProcessor implements BeanPostProcessor {

    public Object postProcessBeforeInitialization(Object bean,String beanName) throws BeansException{
        System.out.println("前方法： "+ beanName);
        return  bean;
    }

    public Object postProcessAfterInitialization(final Object bean ,String beanName) throws  BeansException{
        System.out.println("后方法： "+ beanName);
        return Proxy.newProxyInstance(
                MyBeanPostProcessor.class.getClassLoader(),
                bean.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
                        System.out.println("-----开启事物");
                        Object obj = method.invoke(bean,objects);
                        System.out.println("-----提交事物");
                        return obj;
                    }
                }
        );
    }
}
```

##### 5.4.2.2 配置

```xml
Copy<!-- 将后处理的实现类注册给spring -->
    <bean class="com.springlearning.lifecycle.MyBeanPostProcessor"></bean>
```

- 问题1：后处理bean作用某一个目标类，还是所有目标类？
  所有
- 问题2：如何只作用一个？
  通过“参数2”beanName进行控制

### 5.5 属性依赖注入[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_28)

- 依赖注入方式：手动装配 和 自动装配
- 手动装配：一般进行配置信息都采用手动
  基于xml装配：构造方法、setter方法
  基于注解装配：
- 自动装配：struts和spring 整合可以自动装配
  byType：按类型装配
  byName：按名称装配
  constructor构造装配，
  auto： 不确定装配。

#### 5.5.1 构造方法[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_29)

##### 5.5.1.1 目标类

```java
Copypublic class User {

    private Integer id;
    private String username;
    private Integer age;

    public User(Integer id, String username) {
        super();
        this.id = id;
        this.username = username;
    }
    public User(String username,Integer age) {
        super();
        this.username = username;
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", age=" + age +
                '}';
    }
}
```

##### 5.5.1.2 spring配置

```xml
Copy<!-- 构造方法注入
		* <constructor-arg> 用于配置构造方法一个参数argument
			name ：参数的名称
			value：设置普通数据
			ref：引用数据，一般是另一个bean id值

			index ：参数的索引号，从0开始 。如果只有索引，匹配到了多个构造方法时，默认使用第一个。
			type ：确定参数类型
		例如：使用名称name
			<constructor-arg name="username" value="jack"></constructor-arg>
			<constructor-arg name="age" value="18"></constructor-arg>
		例如2：【类型type 和  索引 index】
			<constructor-arg index="0" type="java.lang.String" value="1"></constructor-arg>
			<constructor-arg index="1" type="java.lang.Integer" value="2"></constructor-arg>
	-->
    <bean id="user" class="com.springlearning.constructor.User" >
        <constructor-arg index="0" type="java.lang.String" value="1"></constructor-arg>
        <constructor-arg index="1" type="java.lang.Integer" value="2"></constructor-arg>
    </bean>
```

#### 5.5.2 setter 方法[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_30)

```xml
Copy     <!-- setter方法注入 
		* 普通数据 
			<property name="" value="值">
			等效
			<property name="">
				<value>值
		* 引用数据
			<property name="" ref="另一个bean">
			等效
			<property name="">
				<ref bean="另一个bean"/>
	
	-->
    <bean id="user" class="com.springlearning.setter.User" >
        <property name="id" value="1"></property>
        <property name="username">
            <value>gzy</value>
        </property>
        <property name="age" value="22" ></property>
        <property name="address" ref="address"></property>
    </bean>
    <bean id="address" class="com.springlearning.setter.Address">
        <property name="addr" value="huhhot"></property>
        <property name="tel" value="1234"></property>
    </bean>
```

#### 5.5.3 P命名空间[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_31)

- 对“setter方法注入”进行简化，替换<property name="属性名">，而是在
  <bean p:属性名="普通值" p:属性名-ref="引用值">
- p命名空间使用前提，必须添加命名空间
  ![xml](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716093932905-1230731366.jpg)

```xml
Copy    <bean id="user" class="com.springlearning.setter.User"
        p:username="gzy" p:age="22"
          p:address-ref="address"
    >
    </bean>
    <bean id="address" class="com.springlearning.setter.Address"
          p:addr="baotou" p:tel="123">
    </bean>
```

#### 5.5.4 SpEL[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_32)

- <property>进行统一编程，所有的内容都使用value

  <property name="" value="#{表达式}">

  # {123}、#{'jack'} ： 数字、字符串

  # {beanId} ：另一个bean引用

  # {beanId.propName} ：操作数据

  # {beanId.toString()} ：执行方法

  # {T(类).字段|方法} ：静态方法或字段

```xml
Copy <bean id="user" class="com.springlearning.setter.User"
        p:username="gzy" p:age="22"
          p:address-ref="address"
    >
        <property name="id" value="#{123}"></property>
    </bean>
    <bean id="address" class="com.springlearning.setter.Address"
          p:addr="baotou" p:tel="123">
    </bean>
```

#### 5.5.4 集合注入[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_33)

```xml
Copy<!-- 
		集合的注入都是给<property>添加子标签
			数组：<array>
			List：<list>
			Set：<set>
			Map：<map> ，map存放k/v 键值对，使用<entry>描述
			Properties：<props>  <prop key=""></prop>  【】
			
		普通数据：<value>
		引用数据：<ref>
	-->
	 <bean id="collData" class="com.springlearning.coll.CollData">
       <property name="array">
           <array>
               <value>alice</value>
               <value>bob</value>
               <value>carl</value>
               <value>Dick</value>
           </array>
       </property>
       <property name="list">
           <list>
               <value>Emma</value>
               <value>Ford</value>
               <value>Harris</value>
           </list>
       </property>
       <property name="set">
           <set>
               <value>张三</value>
               <value>李四</value>
               <value>王五</value>
           </set>
       </property>
       <property name="map">
           <map>
               <entry key="alice" value="爱丽丝"></entry>
               <entry>
                   <key><value>bob</value></key>
                   <value>鲍勃</value>
               </entry>
           </map>
       </property>
       <property name="properties">
           <props>
               <prop key="1">张三</prop>
               <prop key="2">李四</prop>
               <prop key="3">王五</prop>
           </props>
       </property>
   </bean>
```

## 6 装配Bean 基于注解[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_34)

- 注解：就是一个类，使用@注解名称
- 开发中：使用注解 取代 xml配置文件。

1. @Component取代<bean class="">
   @Component("id") 取代
2. web开发，提供3个@Component注解衍生注解（功能一样）取代<bean class="">
   @Repository ：dao层
   @Service：service层
   @Controller：web层
3. 依赖注入，给私有字段设置，也可以给setter方法设置
   普通值：@Value("")
   引用值：
   方式1：按照【类型】注入
   @Autowired
   方式2：按照【名称】注入1
   @Autowired
   @Qualifier("名称")
   方式3：按照【名称】注入2
   @Resource("名称")
4. 生命周期
   初始化：@PostConstruct
   销毁：@PreDestroy
5. 作用域
   @Scope("prototype") 多例

- 注解使用前提，添加命名空间，让spring扫描含有注解类
  ![xml](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716094000235-1806240011.jpg)

```xml
Copy   <!--组件扫描，扫描含有注解的类-->
    <context:component-scan base-package="com.springlearning.annotation"></context:component-scan>
```

- 可能是版本的原因还是其他的原因，我看的教学老师使用比较老的spring的相关jar包，大概是3.多的版本，我是用是5.0的版本，如果使用注解的方式，需要导入spring-aop-5.0.4.RELEASE.jar这个jar包，否则，测试会报错java.lang.NoClassDefFoundError: org/springframework/aop/TargetSource。

> 笔记源自传智播客Spring教学

作者：[donkey-boy](https://www.cnblogs.com/donkey-boy/)

出处：https://www.cnblogs.com/donkey-boy/p/11192838.html

本站使用「[署名 4.0 国际](https://creativecommons.org/licenses/by/4.0)」创作共享协议，转载请在文章明显位置注明作者及出处。



## **1. AOP**[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_0)

### 1.1 AOP介绍[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_1)

#### 1.1.1 什么是AOP[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_2)

- 在软件业，AOP为Aspect Oriented Programming的缩写，意为：面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是OOP（面向对象编程）的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。
- AOP采取横向抽取机制，取代了传统纵向继承体系重复性代码
- 经典应用：事务管理、性能监视、安全检查、缓存 、日志等
- Spring AOP使用纯Java实现，不需要专门的编译过程和类加载器，在运行期通过代理方式向目标类织入增强代码
- AspectJ是一个基于Java语言的AOP框架，Spring2.0开始，Spring AOP引入对Aspect的支持，AspectJ扩展了Java语言，提供了一个专门的编译器，在编译时提供横向代码的织入

#### 1.1.2 AOP 实现原理[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_3)

- aop底层将采用代理机制进行实现。
- 接口 + 实现类：spring采用 jdk 的动态代理Proxy。
- 实现类：spring 采用 cglib字节码增强。

#### 1.1.2 AOP 术语[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_4)

1. target目标类：需要被代理的类。例如：UserService
2. Joinpoint连接点：所谓连接点是指那些可能被拦截到的方法。例如：所有的方法
3. PointCut切入点：已经被增强的连接点。例如：addUser()
4. advice通知/增强，增强代码。例如：after、before
5. Weaving织入：是指把增强advice应用到目标对象target来创建新的代理对象proxy的过程.
6. proxy代理类
7. Aspect切面：是切入点pointcut和通知advice的结合
   一个线是一个特殊的面。
   一个切入点和一个通知，组成成一个特殊的面。
   ![术语](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722093438740-66286457.jpg)

### 1.2 手动方式[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_5)

#### 1.2.1 JDK 动态代理[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_6)

- JDK动态代理 对“装饰者”设计模式 简化。使用前提：必须有接口

1. 目标类：接口 + 实现类
2. 切面类：用于存通知 MyAspect
3. 工厂类：编写工厂生成代理
4. 测试
   ![jdk动态代理](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722093539864-1560497458.png)

##### 1.2.1.1 目标类

```java
Copypublic interface UserService {

    void addUser();
    void updateUser();
    void deleteUser();
}
```

##### 1.2.1.2 切面类

```java
Copypublic class MyAspect {

    public void before(){
        System.out.println("this is before");
    }

    public void after(){
        System.out.println("this is after");
    }
}
```

##### 1.2.1.3 工厂

```java
Copypublic class MyBeanFactory {

    public static UserService createService(){
        //1 目标类
        final UserService userService = new UserServiceImpl();
        //2 切面类
        final MyAspect myAspect = new MyAspect();
        /**
         * 3 代理类：将目标类（切入点）和 切面类（通知） 结合 --> 切面
         *  Proxy.newProxyInstance
         *      参数1：loader ，类加载器，动态代理类 运行时创建，任何类都需要类加载器将其加载到内存。
         * 		 	一般情况：当前类.class.getClassLoader();
         * 		    目标类实例.getClass().get...
         * 		参数2：Class[] interfaces 代理类需要实现的所有接口
         * 	        方式1：目标类实例.getClass().getInterfaces()  ;注意：只能获得自己接口，不能获得父元素接口
         * 	        方式2：new Class[]{UserService.class}
         * 	        例如：jdbc 驱动  --> DriverManager  获得接口 Connection
         * 	    参数3：InvocationHandler  处理类，接口，必须进行实现类，一般采用匿名内部
         * 	        提供 invoke 方法，代理类的每一个方法执行时，都将调用一次invoke
         * 	            参数31：Object proxy ：代理对象
         * 	            参数32：Method method : 代理对象当前执行的方法的描述对象（反射）
         * 	                执行方法名：method.getName()
         * 	                执行方法：method.invoke(对象，实际参数)
         * 	            参数33：Object[] args :方法实际参数
         *
         */
        UserService proxService = (UserService) Proxy.newProxyInstance(
                MyBeanFactory.class.getClassLoader(),
                userService.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

                        //前执行
                        myAspect.before();

                        //执行目标类的方法
                        Object obj = method.invoke(userService,args);

                        //后方法
                        myAspect.after();

                        return obj;
                    }
                }
        );
        return proxService;
    }
    }
}
```

##### 1.2.1.4 测试类

```java
Copy    @Test
    public void demo01(){
        UserService userService = MyBeanFactory.createService();
        userService.addUser();
        userService.updateUser();
        userService.deleteUser();
    }
```

#### 1.2.2 CGLIB 字节码增强[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_7)

- 没有接口，只有实现类
- 采用字节码增强框架cglib，在运行时创建目标类的子类，从而对目标类进行增强。
- 导入jar包：
  自己导包（了解）：
  核心：hibernate-distribution-3.6.10.Final\lib\bytecode\cglib\cglib-2.2.jar
  依赖：struts-2.3.15.3\apps\struts2-blank\WEB-INF\lib\asm-3.3.jar
  spring-core..jar 已经整合以上两个内容
  ![CCGLIB代理](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722095527135-326521029.jpg)

##### 1.2.2.1 工厂类

```java
Copypublic class MyBeanFactory {

    public static UserService createService(){
        //1 目标类
        final UserService userService = new UserServiceImpl();
        //2 切面类
        final MyAspect myAspect = new MyAspect();
        // 3.代理类 ，采用cglib ,底层创建目标类的子类
        // 3.1 核心类
        Enhancer enhancer = new Enhancer();
        // 3.2 确定父类
        enhancer.setSuperclass(userService.getClass());

        /**
         * 3.3 设置回调函数 ， MethodInterceptor接口 等效 jdk InvocationHander接口
         *      intercept() 等效 jdk  invoke()
         *          参数1、参数2、参数3：以invoke一样
         *          参数4：methodProxy 方法的代理
         */

        enhancer.setCallback(new MethodInterceptor() {
            @Override
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                //前执行
                myAspect.before();

                //执行目标类方法
                Object obj = method.invoke(userService,objects);
                // * 执行代理类的父类，执行目标类（目标类和代理类 父子关系）
             //   methodProxy.invokeSuper(o,objects);

                //后执行
                myAspect.after();
                return obj;
            }
        });
        //3.4 创建代理
        UserServiceImpl proxService = (UserServiceImpl) enhancer.create();

        return proxService;
    }
}
```

### 1.3 AOP 联盟通知类型[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_8)

- AOP联盟为通知Advice定义了org.aopalliance.aop.Advice
- Spring按照通知Advice在目标类方法的连接点位置，可以分为5类
  - 前置通知 org.springframework.aop.MethodBeforeAdvice
    - 在目标方法执行前实施增强
  - 后置通知 org.springframework.aop.AfterReturningAdvice
    - 在目标方法执行后实施增强
  - 环绕通知 org.aopalliance.intercept.MethodInterceptor
    - 在目标方法执行前后实施增强
  - 异常抛出通知 org.springframework.aop.ThrowsAdvice
    - 在方法抛出异常后实施增强
  - 引介通知 org.springframework.aop.IntroductionInterceptor
    - 在目标类中添加一些新的方法和属性

```
Copy环绕通知，必须手动执行目标方法
try{
   //前置通知
   //执行目标方法
   //后置通知
} catch(){
   //抛出异常通知
}
```

### 1.4 spring 编写代理：半自动[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_9)

- 让spring 创建代理对象，从spring容器中手动的获取代理对象。
- 导入jar包：
  核心：4+1
  AOP：AOP联盟（规范）、spring-aop （实现）
  ![jar](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722093921692-475117514.jpg)

#### 1.4.1 目标类[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_10)

```java
Copypublic interface UserService {

    void addUser();
    void updateUser();
    void deleteUser();
}
```

#### 1.4.2 切面类[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_11)

```java
Copy/**
 *  切面类中确定通知，需要实现不同接口，接口就是规范，从而就确定方法名称。
 *  采用“环绕通知” MethodInterceptor
 */
public class MyAspect implements MethodInterceptor {

    @Override
    public Object invoke(MethodInvocation methodInvocation) throws Throwable {
        System.out.println("this is before");

        //手动执行目标方法
        Object obj = methodInvocation.proceed();

        System.out.println("this is after");
        return obj;
    }
}
```

#### 1.4.3 spring 配置[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_12)

```xml
Copy    <!-- 1 创建目标类-->
    <bean id="userService" class="com.springlearning.spring_proxy.UserServiceImpl"></bean>
    <!-- 2 创建切面类-->
    <bean id="myAspect" class="com.springlearning.spring_proxy.MyAspect"></bean>

    <!-- 3 创建代理类
         * 使用工厂bean FactoryBean ,底层调用getObject() 返回特殊bean
         * ProxyFactoryBean 用于创建代理工厂bean ,生成特殊代理对象
                interfaces : 确定接口们
                    通过<array> 可以设置多个值
                    只有一个值时， value=""
                target : 确定目标类
                interceptorNames : 通知 切面类的名称，类型String[], 如果设置一个值 value=""
                optimize :强制使用cglib
                    <property name="optimize" value="true"></property>
          底层机制
                如果目标类有接口，采用jdk动态代理
                如果没有接口，采用cglib 字节码增强
                如果声明 optimize = true ,无论是否有接口 ，都采用cglib
    -->
    <bean id="proxyService" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="interfaces" value="com.springlearning.spring_proxy.UserService"></property>
        <property name="target" ref="userService"></property>
        <property name="interceptorNames" value="myAspect"></property>
    </bean>
```

#### 1.4.4 测试[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_13)

```java
Copy    @Test
    public void demo01(){
        String xmlPath = "com/springlearning/spring_proxy/beans.xml";
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);
        //获得代理类
        UserService userService = (UserService) applicationContext.getBean("proxyService");
        userService.addUser();
        userService.updateUser();
        userService.deleteUser();
    }
```

### 1.5 spring aop 编程：全自动[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_14)

- 从spring容器获得目标类，如果配置aop，spring将自动生成代理。
- 要确定目标类，aspectj 切入点表达式，导入jar包
- 在spring-aop-5.0.4版本中包含了aopalliance和aspectj
  ![jar包](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722093953940-120442558.jpg)
- 这里我在调试的时候出了问题，其中在spring-aop-5.0.4版本中包含了aopalliance,而我看的教学是3.x的版本,需要导入另外的aopalliance包，还有错把aspectjrt 当成 aspectjweaver,然而两个包有着不同的功能。

#### 1.5.1 spring 配置[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_15)

```xml
Copy<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/aop
                           http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 1 创建目标类 -->
    <bean id="userService" class="com.springlearning.spring_proxy.UserServiceImpl"></bean>
    <!-- 2 创建切面类（通知）-->
    <bean id="myAspect" class="com.springlearning.spring_proxy.MyAspect"></bean>
    <!-- 3 aop编程
        3.1 导入命名空间
        3.2 使用 <aop:config> 进行配置
                proxy-target-class="true" 声明时使用cglib代理
            <aop:pointcut> 切入点 ， 从目标对象获得具体方法
            <aop:advisor> 特殊的切面, 只用一个通知 和一个切入点
                advice-ref 通知引用
                pointcut-ref 切入点引用
        3.3 切入点表达式
            execution(* com.springlearning.spring_proxy.*.(..))
            选择方法   返回值任意  包         类名称任意  方法名任意   参数任意
    -->
    <aop:config proxy-target-class="true">
        <aop:pointcut id="myPointCut" expression="execution(* com.springlearning.spring_proxy.*.*(..))"/>
        <aop:advisor advice-ref="myAspect" pointcut-ref="myPointCut"></aop:advisor>
    </aop:config>
</beans>
```

#### 1.5.2 测试[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_16)

```java
Copy    @Test
    public void demo01(){
        String xmlPath = "com/springlearning/spring_proxy/beans.xml";
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);

        //获得代理类
        UserService userService = (UserService) applicationContext.getBean("userService");
        userService.addUser();
        userService.updateUser();
        userService.deleteUser();
    }
```

## **2. AspectJ**[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_17)

### 2.1 介绍[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_18)

- AspectJ是一个基于Java语言的AOP框架
- Spring2.0以后新增了对AspectJ切点表达式支持
- @AspectJ 是AspectJ1.5新增功能，通过JDK5注解技术，允许直接在Bean类中定义切面
  新版本Spring框架，建议使用AspectJ方式来开发AOP
- 主要用途：自定义开发

### 2.2 切入点表达式[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_19)

1. execution() 用于描述方法
   语法：execution(修饰符 返回值 包.类.方法名(参数) throws异常)
   修饰符，一般省略
   public 公共方法
   \* 任意
   返回值，不能省略
   void 返回没有值
   String 返回值字符串
   \* 任意
   包，[省略]
   com.springlearning.proxy 固定包
   com.springlearning.proxy.*.service proxy包下面子包任意 （例如：com.springlearning.proxy.staff.service）
   com.springlearning.proxy.. proxy包下面的所有子包（含自己）
   com.springlearning.proxy.*.service.. proxy包下面任意子包，固定目录service，service目录任意包
   类，[省略]
   UserServiceImpl 指定类
   *Impl 以Impl结尾
   User* 以User开头
   \* 任意
   方法名，不能省略
   addUser 固定方法
   add* 以add开头
   *Do 以Do结尾
   \* 任意
   (参数)
   () 无参
   (int) 一个整型
   (int ,int) 两个
   (..) 参数任意
   throws ,可省略，一般不写。

综合 1
execution(* com.springlearning.proxy.*.service..*.*(..))

综合 2
<aop:pointcut expression="execution(* com.springlearning.*WithCommit.*(..)) ||
execution(* com.springlearning.*Service.*(..))" id="myPointCut"/>

1. within:匹配包或子包中的方法
   within(com.springlearning.aop..*)
2. this:匹配实现接口的代理对象中的方法
   this(com.springlearning.aop.user.UserDAO)
3. target:匹配实现接口的目标对象中的方法
   target(com.springlearning.aop.user.UserDAO)
4. args:匹配参数格式符合标准的方法
   args(int,int)
5. bean(id) 对指定的bean所有的方法(了解)
   bean('userService')

### 2.3 AspectJ 通知类型[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_20)

- aop联盟定义通知类型，具有特性接口，必须实现，从而确定方法名称。
- aspectj 通知类型，只定义类型名称。已经方法格式。
- 个数：6种，知道5种，掌握1中
  before:前置通知(应用：各种校验)
  在方法执行前执行，如果通知抛出异常，阻止方法运行
  afterReturning:后置通知(应用：常规数据处理)
  方法正常返回后执行，如果方法中抛出异常，通知无法执行
  必须在方法执行后才执行，所以可以获得方法的返回值。
  around:环绕通知(应用：十分强大，可以做任何事情)
  方法执行前后分别执行，可以阻止方法的执行
  必须手动执行目标方法
  afterThrowing:抛出异常通知(应用：包装异常信息)
  方法抛出异常后执行，如果方法没有抛出异常，无法执行
  after:最终通知(应用：清理现场)
  方法执行完毕后执行，无论方法中是否出现异常

```
Copy环绕

try{
     //前置：before
    //手动执行目标方法
    //后置：afterRetruning
} catch(){
    //抛出异常 afterThrowing
} finally{
    //最终 after
}
```

![aspectj通知类型](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094021264-1412097916.jpg)
![相关类](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094104338-1559339396.jpg)
![AfterThrowing](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094130619-1912048020.jpg)
![After](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094156467-1784324931.jpg)

### 2.4 导入jar包[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_21)

- 4个：
  aop联盟规范
  spring aop 实现
  aspect 规范
  spring aspect 实现

### 2.5 基于xml[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_22)

1. 目标类：接口 + 实现
2. 切面类：编写多个通知，采用aspectj 通知名称任意（方法名任意）
3. aop编程，将通知应用到目标类
4. 测试

#### 2.5.1 切面类[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_23)

```java
Copy/**
 * 切面类，含有多个通知
 */
public class MyAspect {

    public void myBefore(JoinPoint joinPoint){
        System.out.println("前置通知：" + joinPoint.getSignature().getName());
    }
    public void myAfterReturning(JoinPoint joinPoint,Object ret){
        System.out.println("后置通知 ： " + joinPoint.getSignature().getName() + " , -->" + ret);
    }
    public Object myAround(ProceedingJoinPoint joinPoint) throws Throwable{
        System.out.println("前");
        //手动执行目标方法
        Object obj = joinPoint.proceed();
        System.out.println("后");
        return obj;
    }
    public void myAfterThrowing(JoinPoint joinPoint,Throwable throwable){
        System.out.println("抛出异常通知：" + throwable.getMessage());
    }
    public void myAfter(JoinPoint joinPoint){
        System.out.println("最终通知");
    }
}
```

#### 2.5.2 spring配置[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_24)

```xml
Copy<!-- 1 创建目标类 -->
    <bean id="userService" class="com.springlearning.aspectj.UserServiceImpl"></bean>
    <!-- 2 创建切面类（通知）-->
    <bean id="myAspect" class="com.springlearning.aspectj.MyAspect"></bean>
    <!-- 3 aop编程
          <aop:aspect> 将切面类 声明“切面”，从而获得通知（方法）
                ref 切面类引用
          <aop:pointcut> 声明一个切入点，所有通知都可以使用。
                expression 切入点表达式
                id 名称，用于其他通知引用
    -->
    <aop:config>
        <aop:aspect ref="myAspect">
            <aop:pointcut id="myPointcut" expression="execution(* com.springlearning.aspectj.UserServiceImpl.*(..))"/>
            <!-- 3.1 前置通知
				<aop:before method="" pointcut="" pointcut-ref=""/>
					method : 通知，及方法名
					pointcut :切入点表达式，此表达式只能当前通知使用。
					pointcut-ref ： 切入点引用，可以与其他通知共享切入点。
				通知方法格式：public void myBefore(JoinPoint joinPoint){
					参数1：org.aspectj.lang.JoinPoint  用于描述连接点（目标方法），获得目标方法名等
				例如：
			<aop:before method="myBefore" pointcut-ref="myPointCut"/>
			-->

            <!-- 3.2后置通知  ,目标方法后执行，获得返回值
                <aop:after-returning method="" pointcut-ref="" returning=""/>
                    returning 通知方法第二个参数的名称
                通知方法格式：public void myAfterReturning(JoinPoint joinPoint,Object ret){
                    参数1：连接点描述
                    参数2：类型Object，参数名 returning="ret" 配置的
                例如：
            <aop:after-returning method="myAfterReturning" pointcut-ref="myPointCut" returning="ret" />
            -->

            <!-- 3.3 环绕通知
                <aop:around method="" pointcut-ref=""/>
                通知方法格式：public Object myAround(ProceedingJoinPoint joinPoint) throws Throwable{
                    返回值类型：Object
                    方法名：任意
                    参数：org.aspectj.lang.ProceedingJoinPoint
                    抛出异常
                执行目标方法：Object obj = joinPoint.proceed();
                例如：
            <aop:around method="myAround" pointcut-ref="myPointCut"/>
            -->
            <!-- 3.4 抛出异常
                <aop:after-throwing method="" pointcut-ref="" throwing=""/>
                    throwing ：通知方法的第二个参数名称
                通知方法格式：public void myAfterThrowing(JoinPoint joinPoint,Throwable e){
                    参数1：连接点描述对象
                    参数2：获得异常信息，类型Throwable ，参数名由throwing="e" 配置
                例如：
            <aop:after-throwing method="myAfterThrowing" pointcut-ref="myPointCut" throwing="e"/>
            -->
            <!-- 3.5 最终通知-->
            <aop:after method="myAfter" pointcut-ref="myPointcut"></aop:after>

        </aop:aspect>

    </aop:config>
```

### 2.6 基于注解[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_25)

#### 2.6.1 替换bean[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_26)

```xml
Copy <!-- 1 创建目标类 -->
    <bean id="userService" class="com.springlearning.aspectj.UserServiceImpl"></bean>
    <!-- 2 创建切面类（通知）-->
    <bean id="myAspect" class="com.springlearning.aspectj.MyAspect"></bean>
Copy@Component
public class MyAspect {
@Service("userService")
public class UserServiceImpl implements UserService {
```

- 注意：扫描

```xml
Copy<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/aop
                           http://www.springframework.org/schema/aop/spring-aop.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 1. 扫描 注解类 -->
    <context:component-scan base-package="com.springlearning.aspectj"></context:component-scan>
```

#### 2.6.2 替换 aop[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_27)

- 必须进行aspectj 自动代理

```xml
Copy<!-- 2.确定 aop注解生效 -->
	<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

- 声明切面

```xml
Copy<aop:aspect ref="myAspect"> <!--被下面注解替换 -->
Copy@Component
@Aspect //添加注解
public class MyAspect {
```

- 替换前置通知

```xml
Copy<aop:before method="myBefore" pointcut="execution(* com.ithspringlearning.aspectj.UserServiceImpl.*(..))"/> <!--被下面注解替换 -->
Copy    //切入点当前有效
    @Before("execution(* com.springlearning.aspectj.UserServiceImpl.*(..))")
    public void myBefore(JoinPoint joinPoint){
        System.out.println("前置通知：" + joinPoint.getSignature().getName());
    }
```

- 替换 公共切入点

```xml
Copy <aop:pointcut id="myPointcut" expression="execution(* com.springlearning.aspectj.UserServiceImpl.*(..))"/>
 <!--被下面注解替换 -->
Copy    //声明公共切入点
    @Pointcut("execution(* com.springlearning.aspectj.UserServiceImpl.*(..))")
    private void myPointCut(){}
```

- 替换后置

```xml
Copy  <aop:after-returning method="myAfterReturning" pointcut-ref="myPointCut" returning="ret" />
Copy    @AfterReturning(value = "myPointCut()",returning = "ret")
    public void myAfterReturning(JoinPoint joinPoint,Object ret){
        System.out.println("后置通知 ： " + joinPoint.getSignature().getName() + " , -->" + ret);
    }
```

![引用](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094220387-2118651005.jpg)

- 环绕替换

```xml
Copy  <aop:around method="myAround" pointcut-ref="myPointCut"/>
Copy    @Around(value = "myPointCut()")
    public Object myAround(ProceedingJoinPoint joinPoint) throws Throwable{
        System.out.println("前");
        //手动执行目标方法
        Object obj = joinPoint.proceed();
        System.out.println("后");
        return obj;
    }
```

- 替换抛出异常

```xml
Copy<aop:after-throwing method="myAfterThrowing" pointcut-ref="myPointCut" throwing="throwable"/>
Copy    @AfterThrowing(value = "myPointCut()" ,throwing = "throwable")
    public void myAfterThrowing(JoinPoint joinPoint,Throwable throwable){
        System.out.println("抛出异常通知：" + throwable.getMessage());
    }
```

#### 2.6.3 切面类[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_28)

```java
Copy/**
 * 切面类，含有多个通知
 */
@Component
@Aspect
public class MyAspect {

    //声明公共切入点
    @Pointcut("execution(* com.springlearning.aspectj.UserServiceImpl.*(..))")
    private void myPointCut(){}
//    @AfterReturning(value = "myPointCut()",returning = "ret")
    public void myAfterReturning(JoinPoint joinPoint,Object ret){
        System.out.println("后置通知 ： " + joinPoint.getSignature().getName() + " , -->" + ret);
    }
    //切入点当前有效
 //   @Before("execution(* com.springlearning.aspectj.UserServiceImpl.*(..))")
    public void myBefore(JoinPoint joinPoint){
        System.out.println("前置通知：" + joinPoint.getSignature().getName());
    }

 //   @Around(value = "myPointCut()")
    public Object myAround(ProceedingJoinPoint joinPoint) throws Throwable{
        System.out.println("前");
        //手动执行目标方法
        Object obj = joinPoint.proceed();
        System.out.println("后");
        return obj;
    }
 //   @AfterThrowing(value = "myPointCut()" ,throwing = "throwable")
    public void myAfterThrowing(JoinPoint joinPoint,Throwable throwable){
        System.out.println("抛出异常通知：" + throwable.getMessage());
    }
    @After(value = "myPointCut()")
    public void myAfter(JoinPoint joinPoint){
        System.out.println("最终通知");
    }
}
```

#### 2.6.4 spring 配置[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_29)

```xml
Copy    <!-- 1. 扫描 注解类 -->
    <context:component-scan base-package="com.springlearning.aspectj"></context:component-scan>
    
    <!-- 2.确定 aop注解生效 -->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

#### 2.6.5 aop 注解总结[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_30)

- @Aspect 声明切面，修饰切面类，从而获得 通知。
- 通知
  - @Before 前置
  - @AfterReturning 后置
  - @Around 环绕
  - @AfterThrowing 抛出异常
  - @After 最终
- 切入点
  - @PointCut ，修饰方法 private void xxx(){} 之后通过“方法名”获得切入点引用

## **3. JdbcTemplate**[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_31)

- spring 提供用于操作JDBC工具类，类似：DBUtils。
- 依赖 连接池DataSource （数据源）

### 3.1 环境搭建[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_32)

### 3.1.1 创建表[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_33)

```sql
Copycreate table t_user(
  id int primary key ,
  username varchar(50),
  password varchar(32)
);
```

### 3.1.2 导入 jar 包[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_34)

![jar包](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094241079-1906981969.jpg)

### 3.1.3 javaBean[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_35)

```java
Copypublic class User {

    private Integer id;
    private String username;
    private String password;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

### 3.2 使用api[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_36)

```java
Copy    @Test
    public void demo(){
        //1 创建数据源（连接池） dbcp
        BasicDataSource dataSource = new BasicDataSource();
        // * 基本4项
        dataSource.setDriverClassName("oracle.jdbc.driver.OracleDriver");
        dataSource.setUrl("jdbc:oracle:thin:@localhost:1530/orcl");
        dataSource.setUsername("sys as sysdba");
        dataSource.setPassword("sys");
        //2  创建模板
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);
        //3 通过api操作
        jdbcTemplate.update("insert into t_user(id,username,password) values(?,?,?)","6", "tom","998");
    }
```

### 3.3 配置DBCP[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_37)

```xml
Copy    <!-- 创建数据源 -->
    <bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource">
        <property name="driverClassName" value="oracle.jdbc.driver.OracleDriver"></property>
        <property name="url" value="jdbc:oracle:thin:@localhost:1530/orcl"></property>
        <property name="username" value="sys as sysdba"></property>
        <property name="password" value="sys"></property>
    </bean>
    <!-- 创建模板 ,需要注入数据源-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!-- 配置dao -->
    <bean id="userDao" class="com.springlearning.jdbctemplate.UserDao">
        <property name="jdbcTemplate" ref="jdbcTemplate"></property>
    </bean>
```

### 3.4 配置C3P0[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_38)

```xml
Copy    <!-- 创建C3P0数据源 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="oracle.jdbc.driver.OracleDriver"></property>
        <property name="jdbcUrl" value="jdbc:oracle:thin:@localhost:1530/orcl"></property>
        <property name="user" value="sys as sysdba"></property>
        <property name="password" value="sys"></property>
    </bean>
```

- 这儿里注意c3p0的jar包版本，需要mchange-commons-java-0.2.3.包，这是c3p0数据库连接池的辅助包，没有这个包系统启动的时候会报classnotfoundexception，这是c3p0-0.9.2版本后分离出来的包，0.9.1的时候还是一个包就搞定的

### 3.5 使用JdbcDaoSupport[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_39)

#### 3.5.1 dao层[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_40)

```java
Copypackage com.springlearning.jdbctemplate;

import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.support.JdbcDaoSupport;

import java.util.List;

/**
 * @ClassName：UserDao
 * @author: donkey-boy
 * @date:2019/7/19 10:52
 */
public class UserDao extends JdbcDaoSupport {

    /**
      * @Author: donkey-boy
    　* @Description: 更新操作
    　* @Param: user
    　* @Return:  void
    　* @Date: 2019/7/19 15:04
    　*/
    public void update(User user){
        String sql = "UPDATE t_user SET username=?,password=? WHERE id=?";
        Object[] args = {user.getUsername(),user.getPassword(),user.getId()};
        this.getJdbcTemplate().update(sql,args);
    }
    /**
      * @Author: donkey-boy
    　* @Description: 查询全部
    　* @Param: []
    　* @Return: java.util.List<com.springlearning.jdbctemplate.User>
    　* @Date: 2019/7/19 15:06
    　*/
    public List<User> findAll(){
        List<User> userList = this.getJdbcTemplate().query("select * from t_user", BeanPropertyRowMapper.newInstance(User.class));
        return userList;
    }

    /**
      * @Author: donkey-boy
    　* @Description: 获取单个用户
    　* @Param: [id]
    　* @Return: com.springlearning.jdbctemplate.User
    　* @Date: 2019/7/19 15:06
    　*/
    public User getUser(int id){
        User user = this.getJdbcTemplate().queryForObject("select * from t_user where id=?", BeanPropertyRowMapper.newInstance(User.class), id);
        return user;
    }
}
```

#### 3.5.2 spring 配置文件[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_41)

```xml
Copy    <!--  配置dao
        * dao 继承 JdbcDaoSupport，之后只需要注入数据源，底层将自动创建模板
    -->
    <!-- 配置dao -->
    <bean id="userDao" class="com.springlearning.jdbctemplate.UserDao">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
```

#### 3.5.3 源码分析[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_42)

![源码](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094339055-469857623.jpg)

### 3.6 配置 properties[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_43)

#### 3.6.1 properties 文件[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_44)

```properties
Copyjdbc.driverClass=oracle.jdbc.driver.OracleDriver
jdbc.jdbcUrl=jdbc:oracle:thin:@localhost:1530/orcl
jdbc.user=sys as sysdba
jdbc.password=sys
```

#### 3.6.2 spring 配置[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_45)

```xml
Copy    <!-- 加载配置文件 
		"classpath:"前缀表示 src下
		在配置文件之后通过  ${key} 获得内容
	-->
    <context:property-placeholder location="classpath:com/springlearning/jdbctemplate/jdbcInfo.properties"></context:property-placeholder>
    <!-- 创建C3P0数据源 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driverClass}"></property>
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
        <property name="user" value="${jdbc.user}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>
```

> 笔记源自传智播客Spring教学

作者：[donkey-boy](https://www.cnblogs.com/donkey-boy/)

出处：https://www.cnblogs.com/donkey-boy/p/11223983.html

本站使用「[署名 4.0 国际](https://creativecommons.org/licenses/by/4.0)」创作共享协议，转载请在文章明显位置注明作者及出处。



=======
## **1. spring框架概述**[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_0)

### 1.1 什么是spring[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_1)

- Spring是一个开源框架，Spring是于2003 年兴起的一个轻量级的Java 开发框架，由Rod Johnson 在其著作Expert One-On-One J2EE Development and Design中阐述的部分理念和原型衍生而来。它是为了解决企业应用开发的复杂性而创建的。框架的主要优势之一就是其分层架构，分层架构允许使用者选择使用哪一个组件，同时为 J2EE 应用程序开发提供集成的框架。Spring使用基本的JavaBean来完成以前只可能由EJB完成的事情。然而，Spring的用途不仅限于服务器端的开发。从简单性、可测试性和松耦合的角度而言，任何Java应用都可以从Spring中受益。Spring的核心是控制反转（IoC）和面向切面（AOP）。简单来说，Spring是一个分层的JavaSE/EE full-stack(一站式) 轻量级开源框架。 [^摘自]: (百度百科)
- 轻量级：与EJB对比，依赖资源少，销毁的资源少。
- 分层： 一站式，每一个层都提供的解决方案

### 1.2 spring由来[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_2)

- Expert One-to-One J2EE Design and Development
- Expert One-to-One J2EE Development without EJB

### 1.3 spring核心[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_3)

- Spring的核心是 **控制反转（IoC）** 和 **面向切面（AOP）**

### 1.4 spring优点[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_4)

- 方便解耦，简化开发 （高内聚低耦合）
  - Spring就是一个大工厂（容器），可以将所有对象创建和依赖关系维护，交给Spring管理
  - spring工厂是用于生成bean
- AOP编程的支持
  - Spring提供面向切面编程，可以方便的实现对程序进行权限拦截、运行监控等功能
- 声明式事务的支持
  - 只需要通过配置就可以完成对事务的管理，而无需手动编程
- 方便程序的测试
  - Spring对Junit4支持，可以通过注解方便的测试Spring程序
- 方便集成各种优秀框架
  - Spring不排斥各种优秀的开源框架，其内部提供了对各种优秀框架（如：Struts、Hibernate、MyBatis、Quartz等）的直接支持
- 降低JavaEE API的使用难度
  - Spring 对JavaEE开发中非常难用的一些API（JDBC、JavaMail、远程调用等），都提供了封装，使这些API应用难度大大降低

### 1.5 spring体系结构[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_5)

- Spring框架是一个分层架构，它包含一系列的功能要素并被分为大约20个模块。这些模块分为Core Container、Data Access/Integration、Web、AOP（Aspect Oriented Programming）、Instrumentation的测试部分，如图所示：![Spring体系结构图](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716093445114-344400221.png)
- 核心容器：beans、core、context、expression

## **2. 入门案例：IoC**[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_6)

### 2.1 导入jar包[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_7)

- 在写案例之前，需要导入5个jar包，4+1：4个核心（beans、core、context、expression） + 1个依赖（commons-loggins...jar)![5个jar包](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716093714897-656039556.png)

### 2.2 目标类[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_8)

- 提供UserService接口和实现类
- 提供UserService接口和实现类
  之前开发中，直接new一个对象即可。
  学习spring之后，将由Spring创建对象实例--> IoC 控制反转（Inverse of Control）之后需要实例对象时，从spring工厂（容器）中获得，需要将实现类的全限定名称配置到xml文件中。

```java
Copy    public interface UserService {

        public void addUser();
    }
    public class UserServiceImpl implements UserService{
        @Override
        public void addUser() {
            System.out.println("add a User");
        }
    }
```

### 2.3 配置文件[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_9)

- 位置：任意，开发中一般在classpath下（src）
- 名称：任意，开发中常用applicationContext.xml
- 内容：添加schema约束
  约束文件位置：spring-framework-xxx.RELEASE\docs\spring-framework-reference\html\xsd-config.html

```xml
Copy    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
                               http://www.springframework.org/schema/beans/spring-beans.xsd">
        <!-- 配置service 
		    <bean> 配置需要创建的对象
			    id ：用于之后从spring容器获得实例时使用的
			    class ：需要创建实例的全限定类名
	    -->
        <bean id="userService" class="com.springlearning.ioc.UserServiceImpl"></bean>
</beans>
```

### 2.4 测试[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_10)

```java
Copy        @Test
	public void demo02(){
		//从spring容器获得
		//1 获得容器
		String xmlPath = "com/springlearning/ioc/beans.xml";
		ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);
		//2获得内容 --不需要自己new，都是从spring容器获得
		UserService userService = (UserService) applicationContext.getBean("userServiceId");
		userService.addUser();
	}
```

## **3. 入门案例：DI**[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_11)

- DI Dependency Injection,依赖注入
  is a: 是一个，继承。
  has a：有一个，成员变量，依赖。

```java
Copy    class B{  
        private A a; //B类依赖A类  
    }  
```

依赖：一个对象需要使用另外一个对象
注入：通过setter方法进行另一个对象实例设置

- 例如:

```java
Copy  class BookServiceImpl{
        //之前开发：接口 = 实现类  （service和dao耦合）
	//private BookDao bookDao = new BookDaoImpl();
 	//spring之后 （解耦：service实现类使用dao接口，不知道具体的实现类）
	private BookDao bookDao;
	setter方法
   }
```

模拟spring执行过程
创建service实例：BookService bookService = new BookServiceImpl() -->IoC <bean>
创建dao实例：BookDao bookDao = new BookDaoImple() -->IoC
将dao设置给service：bookService.setBookDao(bookDao); -->DI <property>

### 3.1 目标类[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_12)

- 创建BookService接口和实现类
- 创建BookDao接口和实现类
- 将dao和service配置 xml文件
- 使用api测试

#### 3.1.1 dao[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_13)

```java
Copypublic interface BookDao {

    public void addBook();
}
public class BookDaoImpl implements BookDao {
    @Override
    public void addBook() {
        System.out.println("add a book");
    }
}
```

#### 3.1.2 service[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_14)

```java
Copypublic interface BookService {
    void addBook();
}
public class BookServiceImpl implements BookService {
    //方式1: 之前 ， 接口=实现类
    //private BookDao bookDao = new BookDaoImpl();
    //方式2: 接口+ setter
    private BookDao bookDao;
    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
    @Override
    public void addBook() {
        bookDao.addBook();
    }
}
```

### 3.2 配置文件[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_15)

```xml
Copy<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--
	模拟spring执行过程
		创建service实例：BookService bookService = new BookServiceImpl()	IoC  <bean>
		创建dao实例：BookDao bookDao = new BookDaoImpl()			IoC
		将dao设置给service：bookService.setBookDao(bookDao);		DI   <property>

		<property> 用于进行属性注入
			name： bean的属性名，通过setter方法获得
				setBookDao ##> BookDao  ##> bookDao
			ref ：另一个bean的id值的引用
	 -->
    <!-- 创建service -->
    <bean id="bookService" class="com.springlearning.di.BookServiceImpl">
        <property name="bookDao" ref="bookDao"></property>
    </bean>
    <!-- 创建dao实例 -->
    <bean id="bookDao" class="com.springlearning.di.BookDaoImpl"></bean>
</beans>
```

### 3.3 测试[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_16)

```java
Copy@Test
public void demo01(){
    String xmlPath = "com/springlearning/di/beans.xml";
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);
    BookService bookService = (BookService) applicationContext.getBean("bookService");
    bookService.addBook();
}
```

## **4. 核心API**[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_17)

- api整体了解，之后不使用，在学习过程需要。
  ![api](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716093808563-1471929885.png)
- BeanFactory ：这是一个工厂，用于生成任意bean。
  采取延迟加载，第一次getBean时才会初始化Bean
- ApplicationContext：是BeanFactory的子接口，功能更强大。（国际化处理、事件传递、Bean自动装配、各种不同应用层的Context实现）。当配置文件被加载，就进行对象实例化。
  ClassPathXmlApplicationContext 用于加载classpath（类路径、src）下的xml
  加载xml运行时位置 --> /WEB-INF/classes/...xml
  FileSystemXmlApplicationContext 用于加载指定盘符下的xml
  加载xml运行时位置 --> /WEB-INF/...xml
  通过java web ServletContext.getRealPath() 获得具体盘符

```java
Copy@Test
    public void demo02(){
        String xmlPath = "com/springlearning/di/beans.xml";
        BeanFactory beanFactory = new XmlBeanFactory(new ClassPathResource(xmlPath));
        BookService bookService = (BookService) beanFactory.getBean("bookService");
        bookService.addBook();
    }
```

## **5. 装配Bean 基于XML**[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_18)

### 5.1 实例化方式[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_19)

- 3种bean实例化方式：默认构造、静态工厂、实例工厂

#### 5.1.1 默认构造[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_20)

```xml
Copy<bean id="" class="">  必须提供默认构造
```

#### 5.1.2 静态工厂[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_21)

- 常用与spring整合其他框架（工具）
- 静态工厂：用于生成实例对象，所有的方法必须是static

```xml
Copy<bean id=""  class="工厂全限定类名"  factory-method="静态方法">
```

##### 5.1.2.1 工厂

```java
Copypublic class MyBeanFactory {
    /**
     * 创建实例
     * @return
     */
    public static UserService createService(){
        return new UserServiceImpl();
    }
}
```

##### 5.1.2.2 spring配置

```xml
Copy<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!-- 将静态工厂创建的实例交予spring
		class 确定静态工厂全限定类名
		factory-method 确定静态方法名
	-->
    <bean id="userService" class="com.springlearning.bean_xml.MyBeanFactory" factory-method="createService"></bean>
</beans>
```

#### 5.1.3 实例工厂[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_22)

- 实例工厂：必须先有工厂实例对象，通过实例对象创建对象。提供所有的方法都是“非静态”的。

##### 5.1.3.1 工厂

```java
Copy/**
 * @ClassName：MyBeanFactory
 * @author: donkey-boy
 * @desc 实例工厂，所有方法非静态
 * @date:2019/7/12 14:51
 */
public class MyBeanFactory {
    /**
     * 创建实例
     * @return
     */
    public UserService createService(){
        return new UserServiceImpl();
    }
}
```

##### 5.1.3.2 spring 配置

```xml
Copy<bean id="myBeanFactory" class="com.springlearning.bean_xml.MyBeanFactory"></bean>
    <!-- 获得userservice
        * factory-bean 确定工厂实例
        * factory-bean 确定普通方法
     -->
<bean id="userService" factory-bean="myBeanFactory" factory-method="createService"></bean>
```

### 5.2 Bean 种类[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_23)

- 普通bean：之前操作的都是普通bean。<bean id="" class="A"> ，spring直接创建A实例，并返回
- FactoryBean：是一个特殊的bean，具有工厂生成对象能力，只能生成特定的对象。
  bean必须使用 FactoryBean接口，此接口提供方法 getObject() 用于获得特定bean。
  <bean id="" class="FB"> 先创建FB实例，使用调用getObject()方法，并返回方法的返回值
  FB fb = new FB();
  return fb.getObject();
- BeanFactory 和 FactoryBean 对比？
  BeanFactory：工厂，用于生成任意bean。
  FactoryBean：特殊bean，用于生成另一个特定的bean。例如：ProxyFactoryBean ，此工厂bean用于生产代理。<bean id="" class="....ProxyFactoryBean"> 获得代理对象实例。AOP使用

### 5.3 作用域[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_24)

- 作用域：用于确定spring创建bean实例个数
  ![作用域](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716093833688-679391486.png)
- 取值：
  singleton 单例，默认
  prototype 多例，每执行一次getBean将获得一个实例。例如：struts整合spring，配置action多例。
- 配置信息

```xml
Copy<bean id="" class=""  scope="">
<bean id="userService" class="*" 
		scope="prototype" ></bean>
```

### 5.4 生命周期[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_25)

#### 5.4.1 初始化和销毁[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_26)

- 目标方法执行前后执行后，将进行初始化或销毁。

```xml
Copy<bean id="" class="" init-method="初始化方法名称"  destroy-method="销毁的方法名称">
```

##### 5.4.1.1 目标类

```java
Copypublic class UserServiceImpl implements UserService {
    @Override
    public void addUser() {
        System.out.println("add a User");
    }

    public void myInit(){
        System.out.println("初始化");
    }
    public void myDestroy(){
        System.out.println("销毁");
    }
}
```

##### 5.4.1.2 spring配置

```xml
Copy<!--
	init-method 用于配置初始化方法,准备数据等
	destroy-method 用于配置销毁方法,清理资源等
-->
<bean id="userService" class="com.springlearning.lifecycle.UserServiceImpl" init-method="myInit" destroy-method="myDestroy"></bean>
```

##### 5.4.1.3 测试

```java
Copy@Test
    public void demo02()  {
        String xmlPath = "com/springlearning/lifecycle/beans.xml";
        ClassPathXmlApplicationContext  applicationContext = new ClassPathXmlApplicationContext(xmlPath);
        UserService userService = (UserService) applicationContext.getBean("userService");
        userService.addUser();

        //要求：1.容器必须close，销毁方法执行; 2.必须是单例的
//		applicationContext.getClass().getMethod("close").invoke(applicationContext);
        // * 此方法接口中没有定义，实现类提供
        applicationContext.close();
    }
```

#### 5.4.2 BeanPostProcessor 后处理Bean[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_27)

- spring 提供一种机制，只要实现此接口BeanPostProcessor，并将实现类提供给spring容器，spring容器将自动执行，在初始化方法前执行before()，在初始化方法后执行after() 。 配置<bean class="">
  ![后处理Bean](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716093901328-1700487806.jpg)
- Factory hook(勾子) that allows for custom modification of new bean instances, e.g. checking for marker interfaces or wrapping them with proxies.
- spring提供工厂勾子，用于修改实例对象，可以生成代理对象，是AOP底层。
  模拟
  A a =new A();
  a = B.before(a) --> 将a的实例对象传递给后处理bean，可以生成代理对象并返回。
  a.init();
  a = B.after(a);
  a.addUser(); //生成代理对象，目的在目标方法前后执行（例如：开启事务、提交事务）
  a.destroy()

##### 5.4.2.1 编写实现类

```java
Copypublic class MyBeanPostProcessor implements BeanPostProcessor {

    public Object postProcessBeforeInitialization(Object bean,String beanName) throws BeansException{
        System.out.println("前方法： "+ beanName);
        return  bean;
    }

    public Object postProcessAfterInitialization(final Object bean ,String beanName) throws  BeansException{
        System.out.println("后方法： "+ beanName);
        return Proxy.newProxyInstance(
                MyBeanPostProcessor.class.getClassLoader(),
                bean.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
                        System.out.println("-----开启事物");
                        Object obj = method.invoke(bean,objects);
                        System.out.println("-----提交事物");
                        return obj;
                    }
                }
        );
    }
}
```

##### 5.4.2.2 配置

```xml
Copy<!-- 将后处理的实现类注册给spring -->
    <bean class="com.springlearning.lifecycle.MyBeanPostProcessor"></bean>
```

- 问题1：后处理bean作用某一个目标类，还是所有目标类？
  所有
- 问题2：如何只作用一个？
  通过“参数2”beanName进行控制

### 5.5 属性依赖注入[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_28)

- 依赖注入方式：手动装配 和 自动装配
- 手动装配：一般进行配置信息都采用手动
  基于xml装配：构造方法、setter方法
  基于注解装配：
- 自动装配：struts和spring 整合可以自动装配
  byType：按类型装配
  byName：按名称装配
  constructor构造装配，
  auto： 不确定装配。

#### 5.5.1 构造方法[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_29)

##### 5.5.1.1 目标类

```java
Copypublic class User {

    private Integer id;
    private String username;
    private Integer age;

    public User(Integer id, String username) {
        super();
        this.id = id;
        this.username = username;
    }
    public User(String username,Integer age) {
        super();
        this.username = username;
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", age=" + age +
                '}';
    }
}
```

##### 5.5.1.2 spring配置

```xml
Copy<!-- 构造方法注入
		* <constructor-arg> 用于配置构造方法一个参数argument
			name ：参数的名称
			value：设置普通数据
			ref：引用数据，一般是另一个bean id值

			index ：参数的索引号，从0开始 。如果只有索引，匹配到了多个构造方法时，默认使用第一个。
			type ：确定参数类型
		例如：使用名称name
			<constructor-arg name="username" value="jack"></constructor-arg>
			<constructor-arg name="age" value="18"></constructor-arg>
		例如2：【类型type 和  索引 index】
			<constructor-arg index="0" type="java.lang.String" value="1"></constructor-arg>
			<constructor-arg index="1" type="java.lang.Integer" value="2"></constructor-arg>
	-->
    <bean id="user" class="com.springlearning.constructor.User" >
        <constructor-arg index="0" type="java.lang.String" value="1"></constructor-arg>
        <constructor-arg index="1" type="java.lang.Integer" value="2"></constructor-arg>
    </bean>
```

#### 5.5.2 setter 方法[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_30)

```xml
Copy     <!-- setter方法注入 
		* 普通数据 
			<property name="" value="值">
			等效
			<property name="">
				<value>值
		* 引用数据
			<property name="" ref="另一个bean">
			等效
			<property name="">
				<ref bean="另一个bean"/>
	
	-->
    <bean id="user" class="com.springlearning.setter.User" >
        <property name="id" value="1"></property>
        <property name="username">
            <value>gzy</value>
        </property>
        <property name="age" value="22" ></property>
        <property name="address" ref="address"></property>
    </bean>
    <bean id="address" class="com.springlearning.setter.Address">
        <property name="addr" value="huhhot"></property>
        <property name="tel" value="1234"></property>
    </bean>
```

#### 5.5.3 P命名空间[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_31)

- 对“setter方法注入”进行简化，替换<property name="属性名">，而是在
  <bean p:属性名="普通值" p:属性名-ref="引用值">
- p命名空间使用前提，必须添加命名空间
  ![xml](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716093932905-1230731366.jpg)

```xml
Copy    <bean id="user" class="com.springlearning.setter.User"
        p:username="gzy" p:age="22"
          p:address-ref="address"
    >
    </bean>
    <bean id="address" class="com.springlearning.setter.Address"
          p:addr="baotou" p:tel="123">
    </bean>
```

#### 5.5.4 SpEL[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_32)

- <property>进行统一编程，所有的内容都使用value

  <property name="" value="#{表达式}">

  # {123}、#{'jack'} ： 数字、字符串

  # {beanId} ：另一个bean引用

  # {beanId.propName} ：操作数据

  # {beanId.toString()} ：执行方法

  # {T(类).字段|方法} ：静态方法或字段

```xml
Copy <bean id="user" class="com.springlearning.setter.User"
        p:username="gzy" p:age="22"
          p:address-ref="address"
    >
        <property name="id" value="#{123}"></property>
    </bean>
    <bean id="address" class="com.springlearning.setter.Address"
          p:addr="baotou" p:tel="123">
    </bean>
```

#### 5.5.4 集合注入[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_33)

```xml
Copy<!-- 
		集合的注入都是给<property>添加子标签
			数组：<array>
			List：<list>
			Set：<set>
			Map：<map> ，map存放k/v 键值对，使用<entry>描述
			Properties：<props>  <prop key=""></prop>  【】
			
		普通数据：<value>
		引用数据：<ref>
	-->
	 <bean id="collData" class="com.springlearning.coll.CollData">
       <property name="array">
           <array>
               <value>alice</value>
               <value>bob</value>
               <value>carl</value>
               <value>Dick</value>
           </array>
       </property>
       <property name="list">
           <list>
               <value>Emma</value>
               <value>Ford</value>
               <value>Harris</value>
           </list>
       </property>
       <property name="set">
           <set>
               <value>张三</value>
               <value>李四</value>
               <value>王五</value>
           </set>
       </property>
       <property name="map">
           <map>
               <entry key="alice" value="爱丽丝"></entry>
               <entry>
                   <key><value>bob</value></key>
                   <value>鲍勃</value>
               </entry>
           </map>
       </property>
       <property name="properties">
           <props>
               <prop key="1">张三</prop>
               <prop key="2">李四</prop>
               <prop key="3">王五</prop>
           </props>
       </property>
   </bean>
```

## 6 装配Bean 基于注解[#](https://www.cnblogs.com/donkey-boy/p/11192838.html#idx_34)

- 注解：就是一个类，使用@注解名称
- 开发中：使用注解 取代 xml配置文件。

1. @Component取代<bean class="">
   @Component("id") 取代
2. web开发，提供3个@Component注解衍生注解（功能一样）取代<bean class="">
   @Repository ：dao层
   @Service：service层
   @Controller：web层
3. 依赖注入，给私有字段设置，也可以给setter方法设置
   普通值：@Value("")
   引用值：
   方式1：按照【类型】注入
   @Autowired
   方式2：按照【名称】注入1
   @Autowired
   @Qualifier("名称")
   方式3：按照【名称】注入2
   @Resource("名称")
4. 生命周期
   初始化：@PostConstruct
   销毁：@PreDestroy
5. 作用域
   @Scope("prototype") 多例

- 注解使用前提，添加命名空间，让spring扫描含有注解类
  ![xml](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190716094000235-1806240011.jpg)

```xml
Copy   <!--组件扫描，扫描含有注解的类-->
    <context:component-scan base-package="com.springlearning.annotation"></context:component-scan>
```

- 可能是版本的原因还是其他的原因，我看的教学老师使用比较老的spring的相关jar包，大概是3.多的版本，我是用是5.0的版本，如果使用注解的方式，需要导入spring-aop-5.0.4.RELEASE.jar这个jar包，否则，测试会报错java.lang.NoClassDefFoundError: org/springframework/aop/TargetSource。

> 笔记源自传智播客Spring教学

作者：[donkey-boy](https://www.cnblogs.com/donkey-boy/)

出处：https://www.cnblogs.com/donkey-boy/p/11192838.html

本站使用「[署名 4.0 国际](https://creativecommons.org/licenses/by/4.0)」创作共享协议，转载请在文章明显位置注明作者及出处。



## **1. AOP**[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_0)

### 1.1 AOP介绍[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_1)

#### 1.1.1 什么是AOP[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_2)

- 在软件业，AOP为Aspect Oriented Programming的缩写，意为：面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是OOP（面向对象编程）的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。
- AOP采取横向抽取机制，取代了传统纵向继承体系重复性代码
- 经典应用：事务管理、性能监视、安全检查、缓存 、日志等
- Spring AOP使用纯Java实现，不需要专门的编译过程和类加载器，在运行期通过代理方式向目标类织入增强代码
- AspectJ是一个基于Java语言的AOP框架，Spring2.0开始，Spring AOP引入对Aspect的支持，AspectJ扩展了Java语言，提供了一个专门的编译器，在编译时提供横向代码的织入

#### 1.1.2 AOP 实现原理[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_3)

- aop底层将采用代理机制进行实现。
- 接口 + 实现类：spring采用 jdk 的动态代理Proxy。
- 实现类：spring 采用 cglib字节码增强。

#### 1.1.2 AOP 术语[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_4)

1. target目标类：需要被代理的类。例如：UserService
2. Joinpoint连接点：所谓连接点是指那些可能被拦截到的方法。例如：所有的方法
3. PointCut切入点：已经被增强的连接点。例如：addUser()
4. advice通知/增强，增强代码。例如：after、before
5. Weaving织入：是指把增强advice应用到目标对象target来创建新的代理对象proxy的过程.
6. proxy代理类
7. Aspect切面：是切入点pointcut和通知advice的结合
   一个线是一个特殊的面。
   一个切入点和一个通知，组成成一个特殊的面。
   ![术语](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722093438740-66286457.jpg)

### 1.2 手动方式[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_5)

#### 1.2.1 JDK 动态代理[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_6)

- JDK动态代理 对“装饰者”设计模式 简化。使用前提：必须有接口

1. 目标类：接口 + 实现类
2. 切面类：用于存通知 MyAspect
3. 工厂类：编写工厂生成代理
4. 测试
   ![jdk动态代理](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722093539864-1560497458.png)

##### 1.2.1.1 目标类

```java
Copypublic interface UserService {

    void addUser();
    void updateUser();
    void deleteUser();
}
```

##### 1.2.1.2 切面类

```java
Copypublic class MyAspect {

    public void before(){
        System.out.println("this is before");
    }

    public void after(){
        System.out.println("this is after");
    }
}
```

##### 1.2.1.3 工厂

```java
Copypublic class MyBeanFactory {

    public static UserService createService(){
        //1 目标类
        final UserService userService = new UserServiceImpl();
        //2 切面类
        final MyAspect myAspect = new MyAspect();
        /**
         * 3 代理类：将目标类（切入点）和 切面类（通知） 结合 --> 切面
         *  Proxy.newProxyInstance
         *      参数1：loader ，类加载器，动态代理类 运行时创建，任何类都需要类加载器将其加载到内存。
         * 		 	一般情况：当前类.class.getClassLoader();
         * 		    目标类实例.getClass().get...
         * 		参数2：Class[] interfaces 代理类需要实现的所有接口
         * 	        方式1：目标类实例.getClass().getInterfaces()  ;注意：只能获得自己接口，不能获得父元素接口
         * 	        方式2：new Class[]{UserService.class}
         * 	        例如：jdbc 驱动  --> DriverManager  获得接口 Connection
         * 	    参数3：InvocationHandler  处理类，接口，必须进行实现类，一般采用匿名内部
         * 	        提供 invoke 方法，代理类的每一个方法执行时，都将调用一次invoke
         * 	            参数31：Object proxy ：代理对象
         * 	            参数32：Method method : 代理对象当前执行的方法的描述对象（反射）
         * 	                执行方法名：method.getName()
         * 	                执行方法：method.invoke(对象，实际参数)
         * 	            参数33：Object[] args :方法实际参数
         *
         */
        UserService proxService = (UserService) Proxy.newProxyInstance(
                MyBeanFactory.class.getClassLoader(),
                userService.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

                        //前执行
                        myAspect.before();

                        //执行目标类的方法
                        Object obj = method.invoke(userService,args);

                        //后方法
                        myAspect.after();

                        return obj;
                    }
                }
        );
        return proxService;
    }
    }
}
```

##### 1.2.1.4 测试类

```java
Copy    @Test
    public void demo01(){
        UserService userService = MyBeanFactory.createService();
        userService.addUser();
        userService.updateUser();
        userService.deleteUser();
    }
```

#### 1.2.2 CGLIB 字节码增强[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_7)

- 没有接口，只有实现类
- 采用字节码增强框架cglib，在运行时创建目标类的子类，从而对目标类进行增强。
- 导入jar包：
  自己导包（了解）：
  核心：hibernate-distribution-3.6.10.Final\lib\bytecode\cglib\cglib-2.2.jar
  依赖：struts-2.3.15.3\apps\struts2-blank\WEB-INF\lib\asm-3.3.jar
  spring-core..jar 已经整合以上两个内容
  ![CCGLIB代理](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722095527135-326521029.jpg)

##### 1.2.2.1 工厂类

```java
Copypublic class MyBeanFactory {

    public static UserService createService(){
        //1 目标类
        final UserService userService = new UserServiceImpl();
        //2 切面类
        final MyAspect myAspect = new MyAspect();
        // 3.代理类 ，采用cglib ,底层创建目标类的子类
        // 3.1 核心类
        Enhancer enhancer = new Enhancer();
        // 3.2 确定父类
        enhancer.setSuperclass(userService.getClass());

        /**
         * 3.3 设置回调函数 ， MethodInterceptor接口 等效 jdk InvocationHander接口
         *      intercept() 等效 jdk  invoke()
         *          参数1、参数2、参数3：以invoke一样
         *          参数4：methodProxy 方法的代理
         */

        enhancer.setCallback(new MethodInterceptor() {
            @Override
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                //前执行
                myAspect.before();

                //执行目标类方法
                Object obj = method.invoke(userService,objects);
                // * 执行代理类的父类，执行目标类（目标类和代理类 父子关系）
             //   methodProxy.invokeSuper(o,objects);

                //后执行
                myAspect.after();
                return obj;
            }
        });
        //3.4 创建代理
        UserServiceImpl proxService = (UserServiceImpl) enhancer.create();

        return proxService;
    }
}
```

### 1.3 AOP 联盟通知类型[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_8)

- AOP联盟为通知Advice定义了org.aopalliance.aop.Advice
- Spring按照通知Advice在目标类方法的连接点位置，可以分为5类
  - 前置通知 org.springframework.aop.MethodBeforeAdvice
    - 在目标方法执行前实施增强
  - 后置通知 org.springframework.aop.AfterReturningAdvice
    - 在目标方法执行后实施增强
  - 环绕通知 org.aopalliance.intercept.MethodInterceptor
    - 在目标方法执行前后实施增强
  - 异常抛出通知 org.springframework.aop.ThrowsAdvice
    - 在方法抛出异常后实施增强
  - 引介通知 org.springframework.aop.IntroductionInterceptor
    - 在目标类中添加一些新的方法和属性

```
Copy环绕通知，必须手动执行目标方法
try{
   //前置通知
   //执行目标方法
   //后置通知
} catch(){
   //抛出异常通知
}
```

### 1.4 spring 编写代理：半自动[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_9)

- 让spring 创建代理对象，从spring容器中手动的获取代理对象。
- 导入jar包：
  核心：4+1
  AOP：AOP联盟（规范）、spring-aop （实现）
  ![jar](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722093921692-475117514.jpg)

#### 1.4.1 目标类[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_10)

```java
Copypublic interface UserService {

    void addUser();
    void updateUser();
    void deleteUser();
}
```

#### 1.4.2 切面类[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_11)

```java
Copy/**
 *  切面类中确定通知，需要实现不同接口，接口就是规范，从而就确定方法名称。
 *  采用“环绕通知” MethodInterceptor
 */
public class MyAspect implements MethodInterceptor {

    @Override
    public Object invoke(MethodInvocation methodInvocation) throws Throwable {
        System.out.println("this is before");

        //手动执行目标方法
        Object obj = methodInvocation.proceed();

        System.out.println("this is after");
        return obj;
    }
}
```

#### 1.4.3 spring 配置[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_12)

```xml
Copy    <!-- 1 创建目标类-->
    <bean id="userService" class="com.springlearning.spring_proxy.UserServiceImpl"></bean>
    <!-- 2 创建切面类-->
    <bean id="myAspect" class="com.springlearning.spring_proxy.MyAspect"></bean>

    <!-- 3 创建代理类
         * 使用工厂bean FactoryBean ,底层调用getObject() 返回特殊bean
         * ProxyFactoryBean 用于创建代理工厂bean ,生成特殊代理对象
                interfaces : 确定接口们
                    通过<array> 可以设置多个值
                    只有一个值时， value=""
                target : 确定目标类
                interceptorNames : 通知 切面类的名称，类型String[], 如果设置一个值 value=""
                optimize :强制使用cglib
                    <property name="optimize" value="true"></property>
          底层机制
                如果目标类有接口，采用jdk动态代理
                如果没有接口，采用cglib 字节码增强
                如果声明 optimize = true ,无论是否有接口 ，都采用cglib
    -->
    <bean id="proxyService" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="interfaces" value="com.springlearning.spring_proxy.UserService"></property>
        <property name="target" ref="userService"></property>
        <property name="interceptorNames" value="myAspect"></property>
    </bean>
```

#### 1.4.4 测试[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_13)

```java
Copy    @Test
    public void demo01(){
        String xmlPath = "com/springlearning/spring_proxy/beans.xml";
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);
        //获得代理类
        UserService userService = (UserService) applicationContext.getBean("proxyService");
        userService.addUser();
        userService.updateUser();
        userService.deleteUser();
    }
```

### 1.5 spring aop 编程：全自动[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_14)

- 从spring容器获得目标类，如果配置aop，spring将自动生成代理。
- 要确定目标类，aspectj 切入点表达式，导入jar包
- 在spring-aop-5.0.4版本中包含了aopalliance和aspectj
  ![jar包](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722093953940-120442558.jpg)
- 这里我在调试的时候出了问题，其中在spring-aop-5.0.4版本中包含了aopalliance,而我看的教学是3.x的版本,需要导入另外的aopalliance包，还有错把aspectjrt 当成 aspectjweaver,然而两个包有着不同的功能。

#### 1.5.1 spring 配置[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_15)

```xml
Copy<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/aop
                           http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 1 创建目标类 -->
    <bean id="userService" class="com.springlearning.spring_proxy.UserServiceImpl"></bean>
    <!-- 2 创建切面类（通知）-->
    <bean id="myAspect" class="com.springlearning.spring_proxy.MyAspect"></bean>
    <!-- 3 aop编程
        3.1 导入命名空间
        3.2 使用 <aop:config> 进行配置
                proxy-target-class="true" 声明时使用cglib代理
            <aop:pointcut> 切入点 ， 从目标对象获得具体方法
            <aop:advisor> 特殊的切面, 只用一个通知 和一个切入点
                advice-ref 通知引用
                pointcut-ref 切入点引用
        3.3 切入点表达式
            execution(* com.springlearning.spring_proxy.*.(..))
            选择方法   返回值任意  包         类名称任意  方法名任意   参数任意
    -->
    <aop:config proxy-target-class="true">
        <aop:pointcut id="myPointCut" expression="execution(* com.springlearning.spring_proxy.*.*(..))"/>
        <aop:advisor advice-ref="myAspect" pointcut-ref="myPointCut"></aop:advisor>
    </aop:config>
</beans>
```

#### 1.5.2 测试[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_16)

```java
Copy    @Test
    public void demo01(){
        String xmlPath = "com/springlearning/spring_proxy/beans.xml";
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);

        //获得代理类
        UserService userService = (UserService) applicationContext.getBean("userService");
        userService.addUser();
        userService.updateUser();
        userService.deleteUser();
    }
```

## **2. AspectJ**[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_17)

### 2.1 介绍[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_18)

- AspectJ是一个基于Java语言的AOP框架
- Spring2.0以后新增了对AspectJ切点表达式支持
- @AspectJ 是AspectJ1.5新增功能，通过JDK5注解技术，允许直接在Bean类中定义切面
  新版本Spring框架，建议使用AspectJ方式来开发AOP
- 主要用途：自定义开发

### 2.2 切入点表达式[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_19)

1. execution() 用于描述方法
   语法：execution(修饰符 返回值 包.类.方法名(参数) throws异常)
   修饰符，一般省略
   public 公共方法
   \* 任意
   返回值，不能省略
   void 返回没有值
   String 返回值字符串
   \* 任意
   包，[省略]
   com.springlearning.proxy 固定包
   com.springlearning.proxy.*.service proxy包下面子包任意 （例如：com.springlearning.proxy.staff.service）
   com.springlearning.proxy.. proxy包下面的所有子包（含自己）
   com.springlearning.proxy.*.service.. proxy包下面任意子包，固定目录service，service目录任意包
   类，[省略]
   UserServiceImpl 指定类
   *Impl 以Impl结尾
   User* 以User开头
   \* 任意
   方法名，不能省略
   addUser 固定方法
   add* 以add开头
   *Do 以Do结尾
   \* 任意
   (参数)
   () 无参
   (int) 一个整型
   (int ,int) 两个
   (..) 参数任意
   throws ,可省略，一般不写。

综合 1
execution(* com.springlearning.proxy.*.service..*.*(..))

综合 2
<aop:pointcut expression="execution(* com.springlearning.*WithCommit.*(..)) ||
execution(* com.springlearning.*Service.*(..))" id="myPointCut"/>

1. within:匹配包或子包中的方法
   within(com.springlearning.aop..*)
2. this:匹配实现接口的代理对象中的方法
   this(com.springlearning.aop.user.UserDAO)
3. target:匹配实现接口的目标对象中的方法
   target(com.springlearning.aop.user.UserDAO)
4. args:匹配参数格式符合标准的方法
   args(int,int)
5. bean(id) 对指定的bean所有的方法(了解)
   bean('userService')

### 2.3 AspectJ 通知类型[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_20)

- aop联盟定义通知类型，具有特性接口，必须实现，从而确定方法名称。
- aspectj 通知类型，只定义类型名称。已经方法格式。
- 个数：6种，知道5种，掌握1中
  before:前置通知(应用：各种校验)
  在方法执行前执行，如果通知抛出异常，阻止方法运行
  afterReturning:后置通知(应用：常规数据处理)
  方法正常返回后执行，如果方法中抛出异常，通知无法执行
  必须在方法执行后才执行，所以可以获得方法的返回值。
  around:环绕通知(应用：十分强大，可以做任何事情)
  方法执行前后分别执行，可以阻止方法的执行
  必须手动执行目标方法
  afterThrowing:抛出异常通知(应用：包装异常信息)
  方法抛出异常后执行，如果方法没有抛出异常，无法执行
  after:最终通知(应用：清理现场)
  方法执行完毕后执行，无论方法中是否出现异常

```
Copy环绕

try{
     //前置：before
    //手动执行目标方法
    //后置：afterRetruning
} catch(){
    //抛出异常 afterThrowing
} finally{
    //最终 after
}
```

![aspectj通知类型](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094021264-1412097916.jpg)
![相关类](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094104338-1559339396.jpg)
![AfterThrowing](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094130619-1912048020.jpg)
![After](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094156467-1784324931.jpg)

### 2.4 导入jar包[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_21)

- 4个：
  aop联盟规范
  spring aop 实现
  aspect 规范
  spring aspect 实现

### 2.5 基于xml[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_22)

1. 目标类：接口 + 实现
2. 切面类：编写多个通知，采用aspectj 通知名称任意（方法名任意）
3. aop编程，将通知应用到目标类
4. 测试

#### 2.5.1 切面类[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_23)

```java
Copy/**
 * 切面类，含有多个通知
 */
public class MyAspect {

    public void myBefore(JoinPoint joinPoint){
        System.out.println("前置通知：" + joinPoint.getSignature().getName());
    }
    public void myAfterReturning(JoinPoint joinPoint,Object ret){
        System.out.println("后置通知 ： " + joinPoint.getSignature().getName() + " , -->" + ret);
    }
    public Object myAround(ProceedingJoinPoint joinPoint) throws Throwable{
        System.out.println("前");
        //手动执行目标方法
        Object obj = joinPoint.proceed();
        System.out.println("后");
        return obj;
    }
    public void myAfterThrowing(JoinPoint joinPoint,Throwable throwable){
        System.out.println("抛出异常通知：" + throwable.getMessage());
    }
    public void myAfter(JoinPoint joinPoint){
        System.out.println("最终通知");
    }
}
```

#### 2.5.2 spring配置[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_24)

```xml
Copy<!-- 1 创建目标类 -->
    <bean id="userService" class="com.springlearning.aspectj.UserServiceImpl"></bean>
    <!-- 2 创建切面类（通知）-->
    <bean id="myAspect" class="com.springlearning.aspectj.MyAspect"></bean>
    <!-- 3 aop编程
          <aop:aspect> 将切面类 声明“切面”，从而获得通知（方法）
                ref 切面类引用
          <aop:pointcut> 声明一个切入点，所有通知都可以使用。
                expression 切入点表达式
                id 名称，用于其他通知引用
    -->
    <aop:config>
        <aop:aspect ref="myAspect">
            <aop:pointcut id="myPointcut" expression="execution(* com.springlearning.aspectj.UserServiceImpl.*(..))"/>
            <!-- 3.1 前置通知
				<aop:before method="" pointcut="" pointcut-ref=""/>
					method : 通知，及方法名
					pointcut :切入点表达式，此表达式只能当前通知使用。
					pointcut-ref ： 切入点引用，可以与其他通知共享切入点。
				通知方法格式：public void myBefore(JoinPoint joinPoint){
					参数1：org.aspectj.lang.JoinPoint  用于描述连接点（目标方法），获得目标方法名等
				例如：
			<aop:before method="myBefore" pointcut-ref="myPointCut"/>
			-->

            <!-- 3.2后置通知  ,目标方法后执行，获得返回值
                <aop:after-returning method="" pointcut-ref="" returning=""/>
                    returning 通知方法第二个参数的名称
                通知方法格式：public void myAfterReturning(JoinPoint joinPoint,Object ret){
                    参数1：连接点描述
                    参数2：类型Object，参数名 returning="ret" 配置的
                例如：
            <aop:after-returning method="myAfterReturning" pointcut-ref="myPointCut" returning="ret" />
            -->

            <!-- 3.3 环绕通知
                <aop:around method="" pointcut-ref=""/>
                通知方法格式：public Object myAround(ProceedingJoinPoint joinPoint) throws Throwable{
                    返回值类型：Object
                    方法名：任意
                    参数：org.aspectj.lang.ProceedingJoinPoint
                    抛出异常
                执行目标方法：Object obj = joinPoint.proceed();
                例如：
            <aop:around method="myAround" pointcut-ref="myPointCut"/>
            -->
            <!-- 3.4 抛出异常
                <aop:after-throwing method="" pointcut-ref="" throwing=""/>
                    throwing ：通知方法的第二个参数名称
                通知方法格式：public void myAfterThrowing(JoinPoint joinPoint,Throwable e){
                    参数1：连接点描述对象
                    参数2：获得异常信息，类型Throwable ，参数名由throwing="e" 配置
                例如：
            <aop:after-throwing method="myAfterThrowing" pointcut-ref="myPointCut" throwing="e"/>
            -->
            <!-- 3.5 最终通知-->
            <aop:after method="myAfter" pointcut-ref="myPointcut"></aop:after>

        </aop:aspect>

    </aop:config>
```

### 2.6 基于注解[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_25)

#### 2.6.1 替换bean[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_26)

```xml
Copy <!-- 1 创建目标类 -->
    <bean id="userService" class="com.springlearning.aspectj.UserServiceImpl"></bean>
    <!-- 2 创建切面类（通知）-->
    <bean id="myAspect" class="com.springlearning.aspectj.MyAspect"></bean>
Copy@Component
public class MyAspect {
@Service("userService")
public class UserServiceImpl implements UserService {
```

- 注意：扫描

```xml
Copy<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/aop
                           http://www.springframework.org/schema/aop/spring-aop.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 1. 扫描 注解类 -->
    <context:component-scan base-package="com.springlearning.aspectj"></context:component-scan>
```

#### 2.6.2 替换 aop[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_27)

- 必须进行aspectj 自动代理

```xml
Copy<!-- 2.确定 aop注解生效 -->
	<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

- 声明切面

```xml
Copy<aop:aspect ref="myAspect"> <!--被下面注解替换 -->
Copy@Component
@Aspect //添加注解
public class MyAspect {
```

- 替换前置通知

```xml
Copy<aop:before method="myBefore" pointcut="execution(* com.ithspringlearning.aspectj.UserServiceImpl.*(..))"/> <!--被下面注解替换 -->
Copy    //切入点当前有效
    @Before("execution(* com.springlearning.aspectj.UserServiceImpl.*(..))")
    public void myBefore(JoinPoint joinPoint){
        System.out.println("前置通知：" + joinPoint.getSignature().getName());
    }
```

- 替换 公共切入点

```xml
Copy <aop:pointcut id="myPointcut" expression="execution(* com.springlearning.aspectj.UserServiceImpl.*(..))"/>
 <!--被下面注解替换 -->
Copy    //声明公共切入点
    @Pointcut("execution(* com.springlearning.aspectj.UserServiceImpl.*(..))")
    private void myPointCut(){}
```

- 替换后置

```xml
Copy  <aop:after-returning method="myAfterReturning" pointcut-ref="myPointCut" returning="ret" />
Copy    @AfterReturning(value = "myPointCut()",returning = "ret")
    public void myAfterReturning(JoinPoint joinPoint,Object ret){
        System.out.println("后置通知 ： " + joinPoint.getSignature().getName() + " , -->" + ret);
    }
```

![引用](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094220387-2118651005.jpg)

- 环绕替换

```xml
Copy  <aop:around method="myAround" pointcut-ref="myPointCut"/>
Copy    @Around(value = "myPointCut()")
    public Object myAround(ProceedingJoinPoint joinPoint) throws Throwable{
        System.out.println("前");
        //手动执行目标方法
        Object obj = joinPoint.proceed();
        System.out.println("后");
        return obj;
    }
```

- 替换抛出异常

```xml
Copy<aop:after-throwing method="myAfterThrowing" pointcut-ref="myPointCut" throwing="throwable"/>
Copy    @AfterThrowing(value = "myPointCut()" ,throwing = "throwable")
    public void myAfterThrowing(JoinPoint joinPoint,Throwable throwable){
        System.out.println("抛出异常通知：" + throwable.getMessage());
    }
```

#### 2.6.3 切面类[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_28)

```java
Copy/**
 * 切面类，含有多个通知
 */
@Component
@Aspect
public class MyAspect {

    //声明公共切入点
    @Pointcut("execution(* com.springlearning.aspectj.UserServiceImpl.*(..))")
    private void myPointCut(){}
//    @AfterReturning(value = "myPointCut()",returning = "ret")
    public void myAfterReturning(JoinPoint joinPoint,Object ret){
        System.out.println("后置通知 ： " + joinPoint.getSignature().getName() + " , -->" + ret);
    }
    //切入点当前有效
 //   @Before("execution(* com.springlearning.aspectj.UserServiceImpl.*(..))")
    public void myBefore(JoinPoint joinPoint){
        System.out.println("前置通知：" + joinPoint.getSignature().getName());
    }

 //   @Around(value = "myPointCut()")
    public Object myAround(ProceedingJoinPoint joinPoint) throws Throwable{
        System.out.println("前");
        //手动执行目标方法
        Object obj = joinPoint.proceed();
        System.out.println("后");
        return obj;
    }
 //   @AfterThrowing(value = "myPointCut()" ,throwing = "throwable")
    public void myAfterThrowing(JoinPoint joinPoint,Throwable throwable){
        System.out.println("抛出异常通知：" + throwable.getMessage());
    }
    @After(value = "myPointCut()")
    public void myAfter(JoinPoint joinPoint){
        System.out.println("最终通知");
    }
}
```

#### 2.6.4 spring 配置[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_29)

```xml
Copy    <!-- 1. 扫描 注解类 -->
    <context:component-scan base-package="com.springlearning.aspectj"></context:component-scan>
    
    <!-- 2.确定 aop注解生效 -->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

#### 2.6.5 aop 注解总结[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_30)

- @Aspect 声明切面，修饰切面类，从而获得 通知。
- 通知
  - @Before 前置
  - @AfterReturning 后置
  - @Around 环绕
  - @AfterThrowing 抛出异常
  - @After 最终
- 切入点
  - @PointCut ，修饰方法 private void xxx(){} 之后通过“方法名”获得切入点引用

## **3. JdbcTemplate**[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_31)

- spring 提供用于操作JDBC工具类，类似：DBUtils。
- 依赖 连接池DataSource （数据源）

### 3.1 环境搭建[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_32)

### 3.1.1 创建表[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_33)

```sql
Copycreate table t_user(
  id int primary key ,
  username varchar(50),
  password varchar(32)
);
```

### 3.1.2 导入 jar 包[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_34)

![jar包](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094241079-1906981969.jpg)

### 3.1.3 javaBean[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_35)

```java
Copypublic class User {

    private Integer id;
    private String username;
    private String password;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

### 3.2 使用api[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_36)

```java
Copy    @Test
    public void demo(){
        //1 创建数据源（连接池） dbcp
        BasicDataSource dataSource = new BasicDataSource();
        // * 基本4项
        dataSource.setDriverClassName("oracle.jdbc.driver.OracleDriver");
        dataSource.setUrl("jdbc:oracle:thin:@localhost:1530/orcl");
        dataSource.setUsername("sys as sysdba");
        dataSource.setPassword("sys");
        //2  创建模板
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);
        //3 通过api操作
        jdbcTemplate.update("insert into t_user(id,username,password) values(?,?,?)","6", "tom","998");
    }
```

### 3.3 配置DBCP[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_37)

```xml
Copy    <!-- 创建数据源 -->
    <bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource">
        <property name="driverClassName" value="oracle.jdbc.driver.OracleDriver"></property>
        <property name="url" value="jdbc:oracle:thin:@localhost:1530/orcl"></property>
        <property name="username" value="sys as sysdba"></property>
        <property name="password" value="sys"></property>
    </bean>
    <!-- 创建模板 ,需要注入数据源-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!-- 配置dao -->
    <bean id="userDao" class="com.springlearning.jdbctemplate.UserDao">
        <property name="jdbcTemplate" ref="jdbcTemplate"></property>
    </bean>
```

### 3.4 配置C3P0[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_38)

```xml
Copy    <!-- 创建C3P0数据源 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="oracle.jdbc.driver.OracleDriver"></property>
        <property name="jdbcUrl" value="jdbc:oracle:thin:@localhost:1530/orcl"></property>
        <property name="user" value="sys as sysdba"></property>
        <property name="password" value="sys"></property>
    </bean>
```

- 这儿里注意c3p0的jar包版本，需要mchange-commons-java-0.2.3.包，这是c3p0数据库连接池的辅助包，没有这个包系统启动的时候会报classnotfoundexception，这是c3p0-0.9.2版本后分离出来的包，0.9.1的时候还是一个包就搞定的

### 3.5 使用JdbcDaoSupport[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_39)

#### 3.5.1 dao层[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_40)

```java
Copypackage com.springlearning.jdbctemplate;

import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.support.JdbcDaoSupport;

import java.util.List;

/**
 * @ClassName：UserDao
 * @author: donkey-boy
 * @date:2019/7/19 10:52
 */
public class UserDao extends JdbcDaoSupport {

    /**
      * @Author: donkey-boy
    　* @Description: 更新操作
    　* @Param: user
    　* @Return:  void
    　* @Date: 2019/7/19 15:04
    　*/
    public void update(User user){
        String sql = "UPDATE t_user SET username=?,password=? WHERE id=?";
        Object[] args = {user.getUsername(),user.getPassword(),user.getId()};
        this.getJdbcTemplate().update(sql,args);
    }
    /**
      * @Author: donkey-boy
    　* @Description: 查询全部
    　* @Param: []
    　* @Return: java.util.List<com.springlearning.jdbctemplate.User>
    　* @Date: 2019/7/19 15:06
    　*/
    public List<User> findAll(){
        List<User> userList = this.getJdbcTemplate().query("select * from t_user", BeanPropertyRowMapper.newInstance(User.class));
        return userList;
    }

    /**
      * @Author: donkey-boy
    　* @Description: 获取单个用户
    　* @Param: [id]
    　* @Return: com.springlearning.jdbctemplate.User
    　* @Date: 2019/7/19 15:06
    　*/
    public User getUser(int id){
        User user = this.getJdbcTemplate().queryForObject("select * from t_user where id=?", BeanPropertyRowMapper.newInstance(User.class), id);
        return user;
    }
}
```

#### 3.5.2 spring 配置文件[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_41)

```xml
Copy    <!--  配置dao
        * dao 继承 JdbcDaoSupport，之后只需要注入数据源，底层将自动创建模板
    -->
    <!-- 配置dao -->
    <bean id="userDao" class="com.springlearning.jdbctemplate.UserDao">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
```

#### 3.5.3 源码分析[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_42)

![源码](https://img2018.cnblogs.com/blog/1526662/201907/1526662-20190722094339055-469857623.jpg)

### 3.6 配置 properties[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_43)

#### 3.6.1 properties 文件[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_44)

```properties
Copyjdbc.driverClass=oracle.jdbc.driver.OracleDriver
jdbc.jdbcUrl=jdbc:oracle:thin:@localhost:1530/orcl
jdbc.user=sys as sysdba
jdbc.password=sys
```

#### 3.6.2 spring 配置[#](https://www.cnblogs.com/donkey-boy/p/11223983.html#idx_45)

```xml
Copy    <!-- 加载配置文件 
		"classpath:"前缀表示 src下
		在配置文件之后通过  ${key} 获得内容
	-->
    <context:property-placeholder location="classpath:com/springlearning/jdbctemplate/jdbcInfo.properties"></context:property-placeholder>
    <!-- 创建C3P0数据源 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driverClass}"></property>
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
        <property name="user" value="${jdbc.user}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>
```

> 笔记源自传智播客Spring教学

作者：[donkey-boy](https://www.cnblogs.com/donkey-boy/)

出处：https://www.cnblogs.com/donkey-boy/p/11223983.html

本站使用「[署名 4.0 国际](https://creativecommons.org/licenses/by/4.0)」创作共享协议，转载请在文章明显位置注明作者及出处。



>>>>>>> 1bceba3b0817dd12b5184b5608d4687460c549ab
