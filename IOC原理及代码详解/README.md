<<<<<<< HEAD
转载:https://www.cnblogs.com/jing99/p/11809770.html

## 一、什么是IOC

　　引用 Spring 官方原文:This chapter covers the Spring Framework implementation of the Inversion of Control (IoC) [1] principle. IoC is also known as dependency injection (DI). It is a process whereby objects define their dependencies, that is, the other objects they work with, only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then injects those dependencies when it creates the bean. This process is fundamentally the inverse, hence the name Inversion of Control (IoC), of the bean itself controlling the instantiation or location of its dependencies by using direct construction of classes, or a mechanism such as the Service Locator pattern.

　　“控制反转(IoC)”也称为“依赖注入(DI)”，是一个定义对象依赖的过程，对象只和 构造参数，工厂方法参数，对象实例属性或工厂方法返回相关。容器在创建这些 bean 的时 候注入这些依赖。这个过程是一个反向的过程，所以命名为依赖反转，对象实例的创建由其 提供的构造方法或服务定位机制来实现。

　　IOC 最大的好处就是“解耦”。

## 二、IOC底层原理使用技术

　　1、xml配置文件

　　2、dom4j解析配置的xml文件

　　3、工厂、策略、委托等设计模式

　　4、反射

　　5、动态代理（JDK自带动态代理(实现了接口的类)、CGLib(未实现接口的类)）

## 三、Spring容器基本概念

　　Spring 通过一个配置文件描述 Bean 及 Bean 之间的依赖关系，利用 Java 语言的反射功能实例化 Bean 并建立 Bean 之间的依赖关系。 Spring 的 IoC 容器在完成这些底层工作的基础上，还提供了 Bean 实例缓存、生命周期管理、 Bean 实例代理、事件发布、资源装载等高级服务。

　　BeanFactory 是 Spring 框架的基础设施，面向 Spring 本身；

　　ApplicationContext 面向使用 Spring 框架的开发者，几乎所有的应用场合我们都直接使用 ApplicationContext 而非底层的 BeanFactory。

### 　　1、BeanFactory

　  　Spring Bean的创建是典型的工厂模式，这一系列的Bean工厂，也即IOC容器为开发者管理对象间的依赖关系提供了很多便利和基础服务，在Spring中有许多的IOC容器的实现供用户选择和使用。如图：

　　　　　　![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109020841986-812672699.png)

　　其中BeanFactory作为最顶层的一个接口类，它定义了IOC容器的基本功能规范，BeanFactory 有三个子类：ListableBeanFactory、HierarchicalBeanFactory 和AutowireCapableBeanFactory。但是最终的默认实现类是 DefaultListableBeanFactory，他实现了所有的接口。那为何要定义这么多层次的接口呢？其实每个接口都有他使用的场合，它主要是为了区分在 Spring 内部在操作过程中对象的传递和转化过程中，对对象的数据访问所做的限制。例如：

- ListableBeanFactory 接口：表示这些 Bean 是可列表的。
- HierarchicalBeanFactory 接口：表示的是这些 Bean 是有继承关系的，也就是每个Bean 有可能有父 Bean。
- AutowireCapableBeanFactory 接口：定义 Bean 的自动装配规则。

　　这四个接口共同定义了 Bean 的集合、Bean 之间的关系、以及 Bean 行为。BeanFactory接口定义如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public interface BeanFactory {    
     
     //对FactoryBean的转义定义，因为如果使用bean的名字检索FactoryBean得到的对象是工厂生成的对象，    
     //如果需要得到工厂本身，需要转义           
     String FACTORY_BEAN_PREFIX = "&"; 
        
     //根据bean的名字，获取在IOC容器中得到bean实例    
     Object getBean(String name) throws BeansException;    
   
    //根据bean的名字和Class类型来得到bean实例，增加了类型安全验证机制。    
     Object getBean(String name, Class requiredType) throws BeansException;    
    
    //提供对bean的检索，看看是否在IOC容器有这个名字的bean    
     boolean containsBean(String name);    
    
    //根据bean名字得到bean实例，并同时判断这个bean是不是单例    
    boolean isSingleton(String name) throws NoSuchBeanDefinitionException;    
    
    //得到bean实例的Class类型    
    Class getType(String name) throws NoSuchBeanDefinitionException;    
    
    //得到bean的别名，如果根据别名检索，那么其原名也会被检索出来    
    String[] getAliases(String name);    
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 　　2、BeanDefinition

　　SpringIOC容器管理了我们定义的各种Bean对象及其相互的关系。在`BeanFactory`容器中，每一个注入对象都对应一个`BeanDefinition`实例对象，该实例对象负责保存注入对象的所有必要信息，包括其对应的对象的class类型、是否是抽象类、构造方法参数以及其他属性等。当客户端向`BeanFactory`请求相应对象的时候，`BeanFactory`会通过这些信息为客户端返回一个完备可用的对象实例。其继承体系如下：

　　　　　　　　　　　　　　![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109021249026-1634095720.png)

　　那么`BeanDefinition`实例对象的信息是从哪而来呢？这里就要引出一个专门加载解析配置文件的类了，他就是`BeanDefinitionReader`，对应到`xml`配置文件，就是他的子类`XmlBeanDefinitionReader`，`XmlBeanDefinitionReader`负责读取`Spring`指定格式的`XML`配置文件并解析，之后将解析后的文件内容映射到相应的`BeanDefinition`。这个解析过程主要通过下图中的类完成：

　　　　　　　　　　![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109021325017-1587844524.png)

### 　　3、XmlBeanFactory     

　　在BeanFactory里只对IOC容器的基本行为作了定义，根本不关心bean是如何定义、加载、生产的。工厂是如何产生对象的，我们需要看具体的IOC容器实现，spring提供了许多IOC容器的实现。比如XmlBeanFactory，ClasspathXmlApplicationContext等。其中XmlBeanFactory就是针对最基本的ioc容器的实现，这个IOC容器可以读取XML文件定义的BeanDefinition(XML文件中对bean的描述)，因此XmlBeanFactory是一个低级容器。

　　**XmlBeanFactory 初级容器是通过 Resource 装载 Spring 配置信息并启动 IOC 容器**，然后就可以通过 BeanFactory#getBean(beanName)方法从 IOC 容器中获取 Bean 了。通过 BeanFactory 启动IOC 容器时，并不会初始化配置文件中定义的 Bean，初始化动作发生在第一次调用时。

　　对于单实例 (singleton) 的 Bean 来说，BeanFactory 会缓存 Bean 实例，所以第二次使用 getBean() 获取 Bean 时将直接从 IOC 容器的缓存中获取 Bean 实例。**Spring 在 DefaultSingletonBeanRegistry 类中提供了一个用于缓存单实例 Bean 的缓存器，它是一个用ConcurrentHashMap 实现的缓存器，单实例的 Bean 以 beanName 为键保存在这个\**ConcurrentHashMap\** 中。**

　　注意：在初始化 BeanFactory 时，必须为其提供一种日志框架，比如使用Log4J， 即在类路径下提供 Log4J 配置文件，这样启动 Spring 容器才不会报错。

　　XMLBeanFactory定义如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public class XmlBeanFactory extends DefaultListableBeanFactory{
     private final XmlBeanDefinitionReader reader; 
     public XmlBeanFactory(Resource resource)throws BeansException{
         this(resource, null);
     }
     public XmlBeanFactory(Resource resource, BeanFactory parentBeanFactory)
          throws BeansException{
         super(parentBeanFactory);
         this.reader = new XmlBeanDefinitionReader(this);
         this.reader.loadBeanDefinitions(resource);
    }
 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　XMLBeanFactory可以如下使用，实际上初级容器大致使用就是如下方法。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//根据Xml配置文件创建Resource资源对象，该对象中包含了BeanDefinition的信息
 ClassPathResource resource =new ClassPathResource("application-context.xml");
//创建DefaultListableBeanFactory
 DefaultListableBeanFactory factory =new DefaultListableBeanFactory();
//创建XmlBeanDefinitionReader读取器，用于载入BeanDefinition。之所以需要BeanFactory作为参数，是因为会将读取的信息回调配置给factory
 XmlBeanDefinitionReader reader =new XmlBeanDefinitionReader(factory);
//XmlBeanDefinitionReader执行载入BeanDefinition的方法，最后会完成Bean的载入和注册。完成后Bean就成功的放置到IOC容器当中，以后我们就可以从中取得Bean来使用
 reader.loadBeanDefinitions(resource);
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 　　4、ApplicationContext

　　ApplicationContext是Spring提供的一个高级IOC容器，它除了能够提供IOC容器的基本功能外，还为用户提供了以下的附加服务。从ApplicationContext接口的实现，我们看出其特点：

- 支持信息源，可以实现国际化。（实现MessageSource接口）
- 访问资源。(实现ResourcePatternResolver接口，这个后面要讲)
- 支持应用事件。(实现ApplicationEventPublisher接口)

　　其与BeanFactory关系如下：

![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109013948856-338064772.png)

　　Spring为ApplicationContext提供了3种实现，分别是ClassPathXmlApplicationContext、FileSystemXmlApplicationContext、XmlWebApplicationContext，其中XmlWebApplicationContext是专为Web工程定制的。如下图：

　　　　　　　　　　　　![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109023530488-2065388793.png)

　　ApplicationContext允许上下文嵌套，通过保持父上下文可以维持一个上下文体系。对于bean的查找可以在这个上下文体系中发生，首先检查当前上下文，其次是父上下文，逐级向上，这样为不同的Spring应用提供了一个共享的bean定义环境。

### 　　5、ClassPathXmlApplicationContext和FileSystemXmlApplicationContext

　　FileSystemXmlApplicationContext和ClassPathXmlApplicationContext是Spring定义的一种高级容器，不仅仅是IOC。它支持不同信息源头，支持 BeanFactory 工具类，支持层级容器，支持访问文件资源，支持事件发布通知，支持接口回调等。

　　ClassPathXmlApplicationContext的类外部结构关系为：![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191107034407323-1426055756.png)

　　FileSystemXmlApplicationContext的类外部结构关系为（与ClassPathXmlApplicationContext一致）：

![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109025044410-1370859547.jpg)

　　与BeanFactory的xml文件定位方式一样是基于路径的。如：　

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
AplicationContext ctx = new FileSystemXmlApplicationContext("bean.xml"); //加载单个配置文件 

String[] locations = {"bean1.xml", "bean2.xml", "bean3.xml"};  
ApplicationContext ctx = new FileSystemXmlApplicationContext(locations ); //加载多个配置文件

ApplicationContext ctx =new FileSystemXmlApplicationContext("D:roject/bean.xml");//根据具体路径加载文件

// ClassPathXmlApplicationContext同FileSystemXmlApplicationContext一样的初始化方式

// FileSystemXmlApplicationContext这个方法是从文件绝对路径加载配置文件，如果在参数中写的不是绝对路径，那么方法调用的时候也会默认用绝对路径来找，
// 默认文件路径是项目名下一级，与src同级。如果前边加了file:则说明后边的路径就要写全路径了,如：file:D:/workspace/applicationContext.xml。

// ClassPathXmlApplicationContext这个方法是从classpath下加载配置文件(适合于相对路径方式加载)，默认文件路径是src下面那一级。
// 该方法参数中classpath: 前缀是不需要的，默认就是指项目的classpath路径下面；如果要写classpath，请注意classpath:和classpath*:的区别:
// classpath: 只能加载一个配置文件,如果配置了多个,则只加载第一个
// classpath*: 可以加载多个配置文件,如果有多个配置文件,就用这个
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 　　6、XMLWebApplicationContext

　　XMLWebApplicationContext是专门为web应用准备的，他允许从相对于web根目录的路劲中装载配置文件完成初始化工作，从XMLWebApplicationContext中可以获得ServletContext的引用，整个Web应用上下文对象将作为属性放置在ServletContext中，以便web应用可以访问spring上下文，spring中提供WebApplicationContextUtils的getWebApplicationContext(ServletContext serviceContext)方法来获得XMLWebApplicationContext对象。

```
ServletContext servletContext = request.getSession().getServletContext();      
ApplicationContext ctx = WebApplicationContextUtils.getWebApplicationContext(servletContext);  
```

　　ContextLoaderListener和DispatcherServlet 会创建XMLWebApplicationContext容器，但是ContextLoaderListener监听器初始化完毕后，才会初始化Servlet，：

　　XMLWebApplicationContext的类外部结构关系为：

![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191107034526454-1450884761.png)

## 四、Spring IOC容器初始化

　　ApplicationContext 容器的初始化流程主要由 AbstractApplicationContext 类中的 refresh 方法实现。大致过程为:为 BeanFactory 对象执行后续处理(如:context:propertyPlaceholder等)->在上下文(Context)中注册 bean->为 bean 注册拦截处理器(AOP 相关)-非 lazy-init 的 singleton 实例->发布相应事件(Lifecycle 接口相关实现类的生命周期事件发布)。

　　在 spring 中，构建容器的过程都是同步的。同步操作是为了保证容器构建的过程中，不出现多线程资源冲突问题（因为对象的构建、资源的扫描、文件的扫描如果存在多线程对文件的扫描问题，会出现锁的问题）。

### 　　1、构造函数触发IOC初始化

　　从 FileSystemXmlApplicationContext 开始剖析，创建 FileSystemXmlApplicationContext 的构造方法：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public class Test {
    public static void main(String[] args) throws ClassNotFoundException {

        ApplicationContext ctx = new FileSystemXmlApplicationContext
                ("spring-beans/src/test/resources/beans.xml");
        System.out.println("number : " + ctx.getBeanDefinitionCount());
        ((Person) ctx.getBean("person")).work();
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　进入FileSystemXmlApplicationContext类查看构造方法：

　　　　　　![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109033236309-796849512.png)

　　可以看到该构造方法被重载了，可以传递 configLocation 字符串或者字符串数组，也就是说，可以传递过个配置文件的地址。默认刷新为true，parent 容器为null。并进入另一个构造器：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public FileSystemXmlApplicationContext(String[] configLocations, boolean refresh, @Nullable ApplicationContext parent) throws BeansException {
    super(parent);　　// parent为null
    this.setConfigLocations(configLocations);
    if (refresh) {
        this.refresh();
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 　　2、设置资源加载器和资源定位

　　由ApplicationContext高级容器的构造方法可知它做了3件事情，一是设置设置资源加载器，二是设置资源定位，二是刷新容器（开始IOC容器初始化）。

　　首先，调用父类容器的构造方法(super(parent)方法)为容器设置好Bean资源加载器。

　　然后，再调用父类AbstractRefreshableConfigApplicationContext的setConfigLocations(configLocations)方法设置Bean定义资源文件的定位路径。

　　通过追踪FileSystemXmlApplicationContext的继承体系，发现其父类的父类AbstractApplicationContext中初始化IoC容器所做的主要源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public abstract class AbstractApplicationContext extends DefaultResourceLoader  
        implements ConfigurableApplicationContext, DisposableBean {  
    //静态初始化块，在整个容器创建过程中只执行一次  
    static {  
        //为了避免应用程序在Weblogic8.1关闭时出现类加载异常加载问题，加载IoC容  
        //器关闭事件(ContextClosedEvent)类  
        ContextClosedEvent.class.getName();  
    }  
    //FileSystemXmlApplicationContext调用父类构造方法调用的就是该方法  
    public AbstractApplicationContext(ApplicationContext parent) {  
        this.parent = parent;  
        this.resourcePatternResolver = getResourcePatternResolver();  
    }  
    //获取一个Spring Source的加载器用于读入Spring Bean定义资源文件  
    protected ResourcePatternResolver getResourcePatternResolver() {  
        // AbstractApplicationContext继承DefaultResourceLoader，也是一个S  
        //Spring资源加载器，其getResource(String location)方法用于载入资源  
        return new PathMatchingResourcePatternResolver(this);  
    }   
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　AbstractApplicationContext构造方法中调用PathMatchingResourcePatternResolver的构造方法创建Spring资源加载器：

```
public PathMatchingResourcePatternResolver(ResourceLoader resourceLoader) {  
        Assert.notNull(resourceLoader, "ResourceLoader must not be null");  
        //设置Spring的资源加载器  
        this.resourceLoader = resourceLoader;  
} 
```

　　在设置容器的资源加载器之后，接下来FileSystemXmlApplicationContet执行setConfigLocations方法通过调用其父类AbstractRefreshableConfigApplicationContext的方法进行对Bean定义资源文件的定位，该方法的源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//处理单个资源文件路径为一个字符串的情况  
public void setConfigLocation(String location) {  
   //String CONFIG_LOCATION_DELIMITERS = ",; /t/n";  
   //即多个资源文件路径之间用” ,; /t/n”分隔，解析成数组形式  
    setConfigLocations(StringUtils.tokenizeToStringArray(location, CONFIG_LOCATION_DELIMITERS));  
}  

//解析Bean定义资源文件的路径，处理多个资源文件字符串数组  
 public void setConfigLocations(String[] locations) {  
    if (locations != null) {  
        Assert.noNullElements(locations, "Config locations must not be null");  
        this.configLocations = new String[locations.length];  
        for (int i = 0; i < locations.length; i++) {  
            // resolvePath为同一个类中将字符串解析为路径的方法  
            this.configLocations[i] = resolvePath(locations[i]).trim();  
        }  
    }  
    else {  
        this.configLocations = null;  
    }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 　　3、高级容器初始化入口

　　Spring IoC容器对Bean定义资源的载入是从refresh()函数开始的，refresh()是一个模板方法，refresh()方法的作用是：在创建IoC容器前，如果已经有容器存在，则需要把已有的容器销毁和关闭，以保证在refresh之后使用的是新建立起来的IoC容器。refresh的作用类似于对IoC容器的重启，在新建立好的容器中对容器进行初始化，对Bean定义资源进行载入

　　FileSystemXmlApplicationContext通过调用其父类AbstractApplicationContext的refresh()函数启动整个IoC容器对Bean定义的载入过程：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
/**
 *
 * 1. 构建BeanFactory，以便产生所需要的bean定义实例
 * 2. 注册可能感兴趣的事件
 * 3. 创建bean 实例对象
 * 4. 触发被监听的事件
 *
 */
@Override
public void refresh() throws BeansException, IllegalStateException {
    synchronized (this.startupShutdownMonitor) {
        // 为刷新准备应用上下文
        prepareRefresh();
        // 告诉子类刷新内部bean工厂，即在子类中启动refreshBeanFactory()的地方----创建bean工厂，根据配置文件生成bean定义
        ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();
        // 在这个上下文中使用bean工厂
        prepareBeanFactory(beanFactory);

        try {
            // 设置BeanFactory的后置处理器
            postProcessBeanFactory(beanFactory);
            // 调用BeanFactory的后处理器，这些后处理器是在Bean定义中向容器注册的
            invokeBeanFactoryPostProcessors(beanFactory);
            // 注册Bean的后处理器，在Bean创建过程中调用
            registerBeanPostProcessors(beanFactory);
            //对上下文的消息源进行初始化
            initMessageSource();
            // 初始化上下文中的事件机制
            initApplicationEventMulticaster();
            // 初始化其他的特殊Bean
            onRefresh();
            // 检查监听Bean并且将这些Bean向容器注册
            registerListeners();
            // 实例化所有的（non-lazy-init）单件
            finishBeanFactoryInitialization(beanFactory);
            //  发布容器事件，结束refresh过程
            finishRefresh();
        }catch (BeansException ex) {
            if (logger.isWarnEnabled()) {
                logger.warn("Exception encountered during context initialization - " +
                        "cancelling refresh attempt: " + ex);
            }
            // 为防止bean资源占用，在异常处理中，销毁已经在前面过程中生成的单件bean
            destroyBeans();
            // 重置“active”标志
            cancelRefresh(ex);
            throw ex;
        }

        finally {
            // Reset common introspection caches in Spring's core, since we might not ever need metadata for singleton beans anymore...
            resetCommonCaches();
        }
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

- **prepareRefresh** - 准备上下文环境信息，为接下来ApplicationContext 容器的初始化流程做准备。
- **BeanFactory** 的构建。 BeanFactory 是 ApplicationContext 的父接口。是 spring 框架中的顶级容器工厂对象。BeanFactory 只能管理 bean 对象。没有其他功能。如：aop 切面管理、propertyplaceholder 的加载等就不属于BeanFactory的管理范围。 构建 BeanFactory 的功能就是管理 bean 对象。创建 BeanFactory 中管理的 bean 对象。
- **postProcessBeanFactory** - 加载配置中 BeanFactory 无法处理的内容 。如:propertyplacehodler 的加载。
- **invokeBeanFactoryPostProcessors**- 将上一步加载的内容，作为一个容器可以管理的bean对象注册到 ApplicationContext 中。底层实质是在将 postProcessBeanFactory 中加载的内容包装成一个容器 ApplicationContext 可以管理的 bean 对象。
- **registerBeanPostProcessors** - 继续完成上一步的注册操作。配置文件中配置的 bean 对象都创建并注册完成。
- **initMessageSource** - i18n,国际化。初始化国际化消息源。
- **initApplicationEventMulticaster** - 注册事件多播监听。如 ApplicationEvent 事件。是 spring 框架中的观察者模式实现机制。
- **onRefresh** - 初始化主题资源(ThemeSource)。spring 框架提供的视图主题信息。 registerListeners- 创建监听器，并注册。
- **finishBeanFactoryInitialization** - 初始化配置中出现的所有的 lazy-init=false 的 bean 对象，且 bean 对象必须是 singleton 的。
- **finishRefresh** - 最后一步。 发布最终事件。生命周期监听事件。 spring 容器定义了生命周期接口。可以实现容器启动调用初始化，容器销毁之前调用回收资源。Lifecycle 接口。

　　refresh()方法主要为IoC容器Bean的生命周期管理提供条件，Spring IOC容器载入Bean定义资源文件从其子类容器的refreshBeanFactory()方法启动，所以整个refresh()中“ConfigurableListableBeanFactory beanFactory =obtainFreshBeanFactory();” 这句以后代码的都是注册容器的信息源和生命周期事件，载入过程就是从这句代码启动。

　　refresh()方法的作用是：在创建IOC容器前，如果已经有容器存在，则需要把已有的容器销毁和关闭，以保证在refresh之后使用的是新建立起来的IOC容器。refresh的作用类似于对IOC容器的重启，在新建立好的容器中对容器进行初始化，对Bean定义资源进行载入。

### 　　4、XMLBeanFactory低级容器初始化入口

　　AbstractApplicationContext的obtainFreshBeanFactory()方法调用子类容器的refreshBeanFactory()方法，启动容器载入Bean定义资源文件的过程，代码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {  
    //这里使用了委派设计模式，父类定义了抽象的refreshBeanFactory()方法，具体实现调用子类容器的refreshBeanFactory()方法
    refreshBeanFactory();  
    ConfigurableListableBeanFactory beanFactory = getBeanFactory();  
    if (logger.isDebugEnabled()) {  
        logger.debug("Bean factory for " + getDisplayName() + ": " + beanFactory);  
    }  
    return beanFactory;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　AbstractApplicationContext子类的refreshBeanFactory()方法只抽象定义了refreshBeanFactory()方法，容器真正调用的是其子类AbstractRefreshableApplicationContext实现的  refreshBeanFactory()方法，方法的源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
protected final void refreshBeanFactory() throws BeansException {  
   if (hasBeanFactory()) {//如果已经有容器，销毁容器中的bean，关闭容器  
       destroyBeans();  
       closeBeanFactory();  
   }  
   try {  
        // 创建IoC容器 
        // new DefaultListableBeanFactory(getInternalParentBeanFactory()) 
        DefaultListableBeanFactory beanFactory = createBeanFactory();  
        // 设置序列化
        beanFactory.setSerializationId(getId());  
        // 对IoC容器进行定制化，如设置启动参数，开启注解的自动装配等  
        customizeBeanFactory(beanFactory);  
        // 调用载入Bean定义的方法，主要这里又使用了一个委派模式，在当前类中只定义了抽象的loadBeanDefinitions方法，具体的实现调用子类容器  
        loadBeanDefinitions(beanFactory);  
        synchronized (this.beanFactoryMonitor) {  
            this.beanFactory = beanFactory;  
        }  
   }  
   catch (IOException ex) {  
       throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　首先判断是否存在了 BeanFactory，如果有则销毁重新创建，调用 createBeanFactory 方法，该方法中就是像注释写的：创建了 DefaultListableBeanFactory ，也既是说，DefaultListableBeanFactory 就是 BeanFactory的默认实现。

　　接着调用loadBeanDefinitions(beanFactory)装载bean的Definitions， Definition 是核心之一，代表着 IOC 中的基本数据结构。

### 　　5、调用loadBeanDefinitions载入Bean定义

　　该方法也是个抽象方法，默认实现是AbstractRefreshableApplicationContext的子类AbstractXmlApplicationContext ，继续看该方法实现：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public abstract class AbstractXmlApplicationContext extends AbstractRefreshableConfigApplicationContext {  
    //实现父类抽象的载入Bean定义方法  
    @Override  
    protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) throws BeansException, IOException {  
        // 创建XmlBeanDefinitionReader，即创建Bean读取器，并通过回调设置到容器中去，容器使用该读取器读取Bean定义资源  
        XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);  
        // 为Bean读取器设置Spring资源加载器，AbstractXmlApplicationContext的祖先父类AbstractApplicationContext继承DefaultResourceLoader　　　　 // 因此，容器本身也是一个资源加载器  
        beanDefinitionReader.setResourceLoader(this);  
        // 为Bean读取器设置SAX xml解析器  
        beanDefinitionReader.setEntityResolver(new ResourceEntityResolver(this));  
        // 当Bean读取器读取Bean定义的Xml资源文件时，启用Xml的校验机制  
        initBeanDefinitionReader(beanDefinitionReader);  
        // Bean读取器真正实现加载的方法  
        loadBeanDefinitions(beanDefinitionReader);  
   }  
   // Xml Bean读取器加载Bean定义资源  
   protected void loadBeanDefinitions(XmlBeanDefinitionReader reader) throws BeansException, IOException {  
       // 获取Bean定义资源的定位  
       Resource[] configResources = getConfigResources();  
       if (configResources != null) {  
           // Xml Bean读取器调用其父类AbstractBeanDefinitionReader读取定位的Bean定义资源  
           reader.loadBeanDefinitions(configResources);  
       }  
       // 如果子类中获取的Bean定义资源定位为空，则获取FileSystemXmlApplicationContext构造方法中setConfigLocations方法设置的资源  
       String[] configLocations = getConfigLocations();  
       if (configLocations != null) {  
           // Xml Bean读取器调用其父类AbstractBeanDefinitionReader读取定位的Bean定义资源  
           reader.loadBeanDefinitions(configLocations);  
       }  
   }  
   // 这里又使用了一个委托模式，调用子类的获取Bean定义资源定位的方法，该方法在ClassPathXmlApplicationContext中进行实现
   // 对于我们举例分析源码的FileSystemXmlApplicationContext没有使用该方法  
   protected Resource[] getConfigResources() {  
       return null;  
   }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　Xml Bean读取器(XmlBeanDefinitionReader)调用其父类AbstractBeanDefinitionReader的 reader.loadBeanDefinitions方法读取Bean定义资源。由于我们使用FileSystemXmlApplicationContext作为例子分析，因此getConfigResources的返回值为null，因此程序跳过第一个if，进入第二个if执行reader.loadBeanDefinitions(configLocations)分支。

### 　　6、AbstractBeanDefinitionReader读取Bean定义资源

　　XmlBeanDefinitionReader在其抽象父类AbstractBeanDefinitionReader中定义了载入过程，loadBeanDefinitions方法源码如下(注意方法入参是location)：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//重载方法，调用下面的loadBeanDefinitions(String, Set<Resource>);方法  
public int loadBeanDefinitions(String location) throws BeanDefinitionStoreException {  
   return loadBeanDefinitions(location, null);  
}  
//重载方法，调用loadBeanDefinitions(String);  
public int loadBeanDefinitions(String... locations) throws BeanDefinitionStoreException {  
   Assert.notNull(locations, "Location array must not be null");  
   int counter = 0;  
   for (String location : locations) {  
       counter += loadBeanDefinitions(location);  
   }  
   return counter;  
}

public int loadBeanDefinitions(String location, Set<Resource> actualResources) throws BeanDefinitionStoreException {  
   //获取在IoC容器初始化过程中设置的资源加载器  
   ResourceLoader resourceLoader = getResourceLoader();  
   if (resourceLoader == null) {  
       throw new BeanDefinitionStoreException(  
               "Cannot import bean definitions from location [" + location + "]: no ResourceLoader available");  
   }  
   if (resourceLoader instanceof ResourcePatternResolver) {  
       try {  
           //将指定位置的Bean定义资源文件解析为Spring IoC容器封装的资源  
           //加载多个指定位置的Bean定义资源文件  
           Resource[] resources = ((ResourcePatternResolver) resourceLoader).getResources(location);  
           //委派调用其子类XmlBeanDefinitionReader的方法，实现加载功能  
           int loadCount = loadBeanDefinitions(resources);  
           if (actualResources != null) {  
               for (Resource resource : resources) {  
                   actualResources.add(resource);  
               }  
           }  
           if (logger.isDebugEnabled()) {  
               logger.debug("Loaded " + loadCount + " bean definitions from location pattern [" + location + "]");  
           }  
           return loadCount;  
       }  
       catch (IOException ex) {  
           throw new BeanDefinitionStoreException(  
                   "Could not resolve bean definition resource pattern [" + location + "]", ex);  
       }  
   }  
   else {  
       //将指定位置的Bean定义资源文件解析为Spring IoC容器封装的资源  
       //加载单个指定位置的Bean定义资源文件  
       Resource resource = resourceLoader.getResource(location);  
       //委派调用其子类XmlBeanDefinitionReader的方法，实现加载功能  
       int loadCount = loadBeanDefinitions(resource);  
       if (actualResources != null) {  
           actualResources.add(resource);  
       }  
       if (logger.isDebugEnabled()) {  
           logger.debug("Loaded " + loadCount + " bean definitions from location [" + location + "]");  
       }  
       return loadCount;  
   }  
} 
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　loadBeanDefinitions(Resource...resources)方法和上面分析的3个方法类似，同样也是调用XmlBeanDefinitionReader的loadBeanDefinitions方法。如代码：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public int loadBeanDefinitions(Resource... resources) throws BeanDefinitionStoreException {
    Assert.notNull(resources, "Resource array must not be null");
    int counter = 0;
    Resource[] var3 = resources;
    int var4 = resources.length;

    for(int var5 = 0; var5 < var4; ++var5) {
        Resource resource = var3[var5];
        counter += this.loadBeanDefinitions((Resource)resource);
    }

    return counter;
} 
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　从对AbstractBeanDefinitionReader的loadBeanDefinitions方法源码分析可以看出该方法做了以下两件事：

　　首先，调用资源加载器的获取资源方法resourceLoader.getResource(location)，获取到要加载的资源。

　　其次，真正执行加载功能是其子类XmlBeanDefinitionReader的loadBeanDefinitions方法。

　　结合ResourceLoader与ApplicationContext的继承关系图，可以知道此时调用的是DefaultResourceLoader中的getSource()方法定位Resource，因为FileSystemXmlApplicationContext本身就是DefaultResourceLoader的实现类，所以此时又回到了FileSystemXmlApplicationContext中来。

### 　　7、资源加载器获取要读入的资源

　　XmlBeanDefinitionReader通过调用其父类DefaultResourceLoader的getResource方法获取要加载的资源，其源码如下:

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//获取Resource的具体实现方法  
public Resource getResource(String location) {  
   Assert.notNull(location, "Location must not be null");  
   //如果是类路径的方式，那需要使用ClassPathResource 来得到bean 文件的资源对象  
   if (location.startsWith(CLASSPATH_URL_PREFIX)) {  
       return new ClassPathResource(location.substring(CLASSPATH_URL_PREFIX.length()), getClassLoader());  
   }  
    try {  
         // 如果是URL 方式，使用UrlResource 作为bean 文件的资源对象  
        URL url = new URL(location);  
        return new UrlResource(url);  
       }  
       catch (MalformedURLException ex) { 
       } 
       //如果既不是classpath标识，又不是URL标识的Resource定位，则调用  
       //容器本身的getResourceByPath方法获取Resource  
       return getResourceByPath(location);  
} 
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　FileSystemXmlApplicationContext容器提供了getResourceByPath方法的实现，就是为了处理既不是classpath标识，又不是URL标识的Resource定位这种情况。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
protected Resource getResourceByPath(String path) {    
   if (path != null && path.startsWith("/")) {    
        path = path.substring(1);    
    }  
    //这里使用文件系统资源对象来定义bean 文件
    return new FileSystemResource(path);  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　这样代码就回到了 FileSystemXmlApplicationContext 中来，他提供了FileSystemResource 来完成从文件系统得到配置文件的资源定义。

　　这样，就可以从文件系统路径上对IOC 配置文件进行加载 - 当然我们可以按照这个逻辑从任何地方加载，在Spring 中我们看到它提供 的各种资源抽象，比如ClassPathResource, URLResource,FileSystemResource 等来供我们使用。上面我们看到的是定位Resource 的一个过程，而这只是加载过程的一部分。

### 　　8、XmlBeanDefinitionReader加载Bean定义资源

   Bean定义的Resource得到了后，继续回到XmlBeanDefinitionReader的loadBeanDefinitions(Resource …)方法看到代表bean文件的资源定义以后的载入过程。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
// XmlBeanDefinitionReader加载资源的入口方法  
public int loadBeanDefinitions(Resource resource) throws BeanDefinitionStoreException {  
   // 将读入的XML资源进行特殊编码处理  
   return loadBeanDefinitions(new EncodedResource(resource));  
} 
// 这里是载入XML形式Bean定义资源文件方法
public int loadBeanDefinitions(EncodedResource encodedResource) throws BeanDefinitionStoreException {  　　... ...     
   try {    
        // 将资源文件转为InputStream的IO流 
       InputStream inputStream = encodedResource.getResource().getInputStream();    
       try {    
          // 从InputStream中得到XML的解析源    
           InputSource inputSource = new InputSource(inputStream);    
           if (encodedResource.getEncoding() != null) {    
               inputSource.setEncoding(encodedResource.getEncoding());    
           }    
           // 这里是具体的读取过程    
           return doLoadBeanDefinitions(inputSource, encodedResource.getResource());    
       }    
       finally {    
           // 关闭从Resource中得到的IO流    
           inputStream.close();    
       }    　　... ...
   }    
}    
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　进入 loadBeanDefinitions(resource) 方法中，最后进入到 XmlBeanDefinitionReader.loadBeanDefinitions(EncodedResource encodedResource) 方法中，该方法主要调用 doLoadBeanDefinitions(inputSource, encodedResource.getResource()) 方法。其实现为：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
// 从特定XML文件中实际载入Bean定义资源的方法 
protected int doLoadBeanDefinitions(InputSource inputSource, Resource resource) throws BeanDefinitionStoreException {
    try {
        // 将XML文件转换为DOM对象，解析过程由documentLoader实现 
        Document doc = this.doLoadDocument(inputSource, resource);
        // 这里是启动对Bean定义解析的详细过程，该解析过程会用到Spring的Bean配置规则
        return this.registerBeanDefinitions(doc, resource);
    } catch (BeanDefinitionStoreException var4) {
        throw var4;
    } catch (SAXParseException var5) {
        throw new XmlBeanDefinitionStoreException(resource.getDescription(), "Line " + var5.getLineNumber() + " in XML document from " + resource + " is invalid", var5);
    } catch (SAXException var6) {
        throw new XmlBeanDefinitionStoreException(resource.getDescription(), "XML document from " + resource + " is invalid", var6);
    } catch (ParserConfigurationException var7) {
        throw new BeanDefinitionStoreException(resource.getDescription(), "Parser configuration exception parsing XML from " + resource, var7);
    } catch (IOException var8) {
        throw new BeanDefinitionStoreException(resource.getDescription(), "IOException parsing XML document from " + resource, var8);
    } catch (Throwable var9) {
        throw new BeanDefinitionStoreException(resource.getDescription(), "Unexpected exception parsing XML document from " + resource, var9);
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过源码分析，载入Bean定义资源文件的最后一步是将Bean定义资源转换为Document对象，该过程由documentLoader实现。

### 　　9、DocumentLoader将Bean定义资源转换为Document对象

　　DocumentLoader通过其实现类DefaultDocumentLoader将Bean定义资源转换成Document对象，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//使用标准的JAXP将载入的Bean定义资源转换成document对象  
public Document loadDocument(InputSource inputSource, EntityResolver entityResolver,  
       ErrorHandler errorHandler, int validationMode, boolean namespaceAware) throws Exception {  
   //创建文件解析器工厂  
   DocumentBuilderFactory factory = createDocumentBuilderFactory(validationMode, namespaceAware);  
   if (logger.isDebugEnabled()) {  
       logger.debug("Using JAXP provider [" + factory.getClass().getName() + "]");  
   }  
   //创建文档解析器  
   DocumentBuilder builder = createDocumentBuilder(factory, entityResolver, errorHandler);  
   //解析Spring的Bean定义资源  
   return builder.parse(inputSource);  
}  
protected DocumentBuilderFactory createDocumentBuilderFactory(int validationMode, boolean namespaceAware)  
       throws ParserConfigurationException {  
   //创建文档解析工厂  
   DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();  
   factory.setNamespaceAware(namespaceAware);  
   //设置解析XML的校验  
   if (validationMode != XmlValidationModeDetector.VALIDATION_NONE) {  
       factory.setValidating(true);  
       if (validationMode == XmlValidationModeDetector.VALIDATION_XSD) {  
           factory.setNamespaceAware(true);  
           try {  
               factory.setAttribute(SCHEMA_LANGUAGE_ATTRIBUTE, XSD_SCHEMA_LANGUAGE);  
           }  
           catch (IllegalArgumentException ex) {  
               ParserConfigurationException pcex = new ParserConfigurationException(  
                       "Unable to validate using XSD: Your JAXP provider [" + factory +  
                       "] does not support XML Schema. Are you running on Java 1.4 with Apache Crimson? " +  
                       "Upgrade to Apache Xerces (or Java 1.5) for full XSD support.");  
               pcex.initCause(ex);  
               throw pcex;  
           }  
       }  
   }  
   return factory;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　该解析过程调用JavaEE标准的JAXP标准进行处理。至此Spring IOC容器根据定位的Bean定义资源文件，将其加载读入并转换成为Document对象过程完成。接下来我们要继续分析Spring IOC容器将载入的Bean定义资源文件转换为Document对象之后，是如何将其解析为Spring IOC管理的Bean对象并将其注册到容器中的。

### 　　10、XmlBeanDefinitionReader解析载入的Bean定义资源文件

　　XmlBeanDefinitionReader类中的doLoadBeanDefinitions方法是从特定XML文件中实际载入Bean定义资源的方法，该方法在载入Bean定义资源之后将其转换为Document对象，接下来调用registerBeanDefinitions启动Spring IOC容器对Bean定义的解析过程，registerBeanDefinitions方法源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//按照Spring的Bean语义要求将Bean定义资源解析并转换为容器内部数据结构  
public int registerBeanDefinitions(Document doc, Resource resource) throws BeanDefinitionStoreException {  
   //得到BeanDefinitionDocumentReader来对xml格式的BeanDefinition解析  
   BeanDefinitionDocumentReader documentReader = createBeanDefinitionDocumentReader();  
   //获得容器中注册的Bean数量  
   int countBefore = getRegistry().getBeanDefinitionCount();  
   //解析过程入口，这里使用了委派模式，BeanDefinitionDocumentReader只是个接口，//具体的解析实现过程有实现类DefaultBeanDefinitionDocumentReader完成  
   documentReader.registerBeanDefinitions(doc, createReaderContext(resource));  
   //统计解析的Bean数量  
   return getRegistry().getBeanDefinitionCount() - countBefore;  
}  
//创建BeanDefinitionDocumentReader对象，解析Document对象  
protected BeanDefinitionDocumentReader createBeanDefinitionDocumentReader() {  
   return BeanDefinitionDocumentReader.class.cast(BeanUtils.instantiateClass(this.documentReaderClass));  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　Bean定义资源的载入解析分为以下两个过程：

　　首先，通过调用XML解析器将Bean定义资源文件转换得到Document对象，但是这些Document对象并没有按照Spring的Bean规则进行解析。这一步是载入的过程

　　其次，在完成通用的XML解析之后，按照Spring的Bean规则对Document对象进行解析。

　　按照Spring的Bean规则对Document对象解析的过程是在接口BeanDefinitionDocumentReader的实现类DefaultBeanDefinitionDocumentReader中实现的。

### 　　11、DefaultBeanDefinitionDocumentReader对Bean定义的Document对象解析

　　BeanDefinitionDocumentReader接口通过registerBeanDefinitions方法调用其实现类DefaultBeanDefinitionDocumentReader对Document对象进行解析，解析的代码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//根据Spring DTD对Bean的定义规则解析Bean定义Document对象  
public void registerBeanDefinitions(Document doc, XmlReaderContext readerContext) {  
    //获得XML描述符  
    this.readerContext = readerContext;  
    logger.debug("Loading bean definitions");  
    //获得Document的根元素  
    Element root = doc.getDocumentElement();  
    //具体的解析过程由BeanDefinitionParserDelegate实现，  
    //BeanDefinitionParserDelegate中定义了Spring Bean定义XML文件的各种元素  
   BeanDefinitionParserDelegate delegate = createHelper(readerContext, root);  
   //在解析Bean定义之前，进行自定义的解析，增强解析过程的可扩展性  
   preProcessXml(root);  
   //从Document的根元素开始进行Bean定义的Document对象  
   parseBeanDefinitions(root, delegate);  
   //在解析Bean定义之后，进行自定义的解析，增加解析过程的可扩展性  
   postProcessXml(root);  
}  
//创建BeanDefinitionParserDelegate，用于完成真正的解析过程  
protected BeanDefinitionParserDelegate createHelper(XmlReaderContext readerContext, Element root) {  
   BeanDefinitionParserDelegate delegate = new BeanDefinitionParserDelegate(readerContext);  
   //BeanDefinitionParserDelegate初始化Document根元素  
   delegate.initDefaults(root);  
   return delegate;  
}  
//使用Spring的Bean规则从Document的根元素开始进行Bean定义的Document对象  
protected void parseBeanDefinitions(Element root, BeanDefinitionParserDelegate delegate) {  
   //Bean定义的Document对象使用了Spring默认的XML命名空间  
   if (delegate.isDefaultNamespace(root)) {  
       //获取Bean定义的Document对象根元素的所有子节点  
       NodeList nl = root.getChildNodes();  
       for (int i = 0; i < nl.getLength(); i++) {  
           Node node = nl.item(i);  
           //获得Document节点是XML元素节点  
           if (node instanceof Element) {  
               Element ele = (Element) node;  
           //Bean定义的Document的元素节点使用的是Spring默认的XML命名空间  
               if (delegate.isDefaultNamespace(ele)) {  
                   //使用Spring的Bean规则解析元素节点  
                   parseDefaultElement(ele, delegate);  
               }  
               else {  
                   //没有使用Spring默认的XML命名空间，则使用用户自定义的解//析规则解析元素节点  
                   delegate.parseCustomElement(ele);  
               }  
           }  
       }  
   }  
   else {  
       //Document的根节点没有使用Spring默认的命名空间，则使用用户自定义的  
       //解析规则解析Document根节点  
       delegate.parseCustomElement(root);  
   }  
}  
//使用Spring的Bean规则解析Document元素节点  
private void parseDefaultElement(Element ele, BeanDefinitionParserDelegate delegate) {  
   //如果元素节点是<Import>导入元素，进行导入解析  
   if (delegate.nodeNameEquals(ele, IMPORT_ELEMENT)) {  
       importBeanDefinitionResource(ele);  
   }  
   //如果元素节点是<Alias>别名元素，进行别名解析  
   else if (delegate.nodeNameEquals(ele, ALIAS_ELEMENT)) {  
       processAliasRegistration(ele);  
   }  
   //元素节点既不是导入元素，也不是别名元素，即普通的<Bean>元素，  
   //按照Spring的Bean规则解析元素  
   else if (delegate.nodeNameEquals(ele, BEAN_ELEMENT)) {  
       processBeanDefinition(ele, delegate);  
   }  
}  
//解析<Import>导入元素，从给定的导入路径加载Bean定义资源到Spring IoC容器中  
protected void importBeanDefinitionResource(Element ele) {  
   //获取给定的导入元素的location属性  
   String location = ele.getAttribute(RESOURCE_ATTRIBUTE);  
   //如果导入元素的location属性值为空，则没有导入任何资源，直接返回  
   if (!StringUtils.hasText(location)) {  
       getReaderContext().error("Resource location must not be empty", ele);  
       return;  
   }  
   //使用系统变量值解析location属性值  
   location = SystemPropertyUtils.resolvePlaceholders(location);  
   Set<Resource> actualResources = new LinkedHashSet<Resource>(4);  
   //标识给定的导入元素的location是否是绝对路径  
   boolean absoluteLocation = false;  
   try {  
       absoluteLocation = ResourcePatternUtils.isUrl(location) || ResourceUtils.toURI(location).isAbsolute();  
   }  
   catch (URISyntaxException ex) {  
       //给定的导入元素的location不是绝对路径  
   }  
   //给定的导入元素的location是绝对路径  
   if (absoluteLocation) {  
       try {  
           //使用资源读入器加载给定路径的Bean定义资源  
           int importCount = getReaderContext().getReader().loadBeanDefinitions(location, actualResources);  
           if (logger.isDebugEnabled()) {  
               logger.debug("Imported " + importCount + " bean definitions from URL location [" + location + "]");  
           }  
       }  
       catch (BeanDefinitionStoreException ex) {  
           getReaderContext().error(  
                   "Failed to import bean definitions from URL location [" + location + "]", ele, ex);  
       }  
   }  
   else {  
       //给定的导入元素的location是相对路径  
       try {  
           int importCount;  
           //将给定导入元素的location封装为相对路径资源  
           Resource relativeResource = getReaderContext().getResource().createRelative(location);  
           //封装的相对路径资源存在  
           if (relativeResource.exists()) {  
               //使用资源读入器加载Bean定义资源  
               importCount = getReaderContext().getReader().loadBeanDefinitions(relativeResource);  
               actualResources.add(relativeResource);  
           }  
           //封装的相对路径资源不存在  
           else {  
               //获取Spring IoC容器资源读入器的基本路径  
               String baseLocation = getReaderContext().getResource().getURL().toString();  
               //根据Spring IoC容器资源读入器的基本路径加载给定导入  
               //路径的资源  
               importCount = getReaderContext().getReader().loadBeanDefinitions(  
                       StringUtils.applyRelativePath(baseLocation, location), actualResources);  
           }  
           if (logger.isDebugEnabled()) {  
               logger.debug("Imported " + importCount + " bean definitions from relative location [" + location + "]");  
           }  
       }  
       catch (IOException ex) {  
           getReaderContext().error("Failed to resolve current resource location", ele, ex);  
       }  
       catch (BeanDefinitionStoreException ex) {  
           getReaderContext().error("Failed to import bean definitions from relative location [" + location + "]",  
                   ele, ex);  
       }  
   }  
   Resource[] actResArray = actualResources.toArray(new Resource[actualResources.size()]);  
   //在解析完<Import>元素之后，发送容器导入其他资源处理完成事件  
   getReaderContext().fireImportProcessed(location, actResArray, extractSource(ele));  
}  
//解析<Alias>别名元素，为Bean向Spring IoC容器注册别名  
protected void processAliasRegistration(Element ele) {  
   //获取<Alias>别名元素中name的属性值  
   String name = ele.getAttribute(NAME_ATTRIBUTE);  
   //获取<Alias>别名元素中alias的属性值  
   String alias = ele.getAttribute(ALIAS_ATTRIBUTE);  
   boolean valid = true;  
   //<alias>别名元素的name属性值为空  
   if (!StringUtils.hasText(name)) {  
       getReaderContext().error("Name must not be empty", ele);  
       valid = false;  
   }  
   //<alias>别名元素的alias属性值为空  
   if (!StringUtils.hasText(alias)) {  
       getReaderContext().error("Alias must not be empty", ele);  
       valid = false;  
   }  
   if (valid) {  
       try {  
           //向容器的资源读入器注册别名  
           getReaderContext().getRegistry().registerAlias(name, alias);  
       }  
       catch (Exception ex) {  
           getReaderContext().error("Failed to register alias '" + alias +  
                   "' for bean with name '" + name + "'", ele, ex);  
       }  
       //在解析完<Alias>元素之后，发送容器别名处理完成事件  
       getReaderContext().fireAliasRegistered(name, alias, extractSource(ele));  
   }  
}  
//解析Bean定义资源Document对象的普通元素  
protected void processBeanDefinition(Element ele, BeanDefinitionParserDelegate delegate) {  
   // BeanDefinitionHolder是对BeanDefinition的封装，即Bean定义的封装类  
   //对Document对象中<Bean>元素的解析由BeanDefinitionParserDelegate实现  BeanDefinitionHolder bdHolder = delegate.parseBeanDefinitionElement(ele);  
   if (bdHolder != null) {  
       bdHolder = delegate.decorateBeanDefinitionIfRequired(ele, bdHolder);  
       try {  
          //向Spring IoC容器注册解析得到的Bean定义，这是Bean定义向IoC容器注册的入口            
              BeanDefinitionReaderUtils.registerBeanDefinition(bdHolder, getReaderContext().getRegistry());  
       }  
       catch (BeanDefinitionStoreException ex) {  
           getReaderContext().error("Failed to register bean definition with name '" +  
                   bdHolder.getBeanName() + "'", ele, ex);  
       }  
       //在完成向Spring IoC容器注册解析得到的Bean定义之后，发送注册事件  
       getReaderContext().fireComponentRegistered(new BeanComponentDefinition(bdHolder));  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过上述Spring IOC容器对载入的Bean定义Document解析可以看出，我们使用Spring时，在Spring配置文件中可以使用<Import>元素来导入IOC容器所需要的其他资源，Spring IOC容器在解析时会首先将指定导入的资源加载进容器中。使用<Ailas>别名时，Spring IoC容器首先将别名元素所定义的别名注册到容器中。

　　对于既不是<Import>元素，又不是<Alias>元素的元素，即Spring配置文件中普通的<Bean>元素的解析由BeanDefinitionParserDelegate类的parseBeanDefinitionElement方法来实现。

### 　　12、BeanDefinitionParserDelegate解析Bean定义资源文件中的<Bean>元素

　　Bean定义资源文件中的<Import>和<Alias>元素解析在DefaultBeanDefinitionDocumentReader中已经完成，对Bean定义资源文件中使用最多的<Bean>元素交由BeanDefinitionParserDelegate来解析，其解析实现的源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//解析<Bean>元素的入口  
   public BeanDefinitionHolder parseBeanDefinitionElement(Element ele) {  
       return parseBeanDefinitionElement(ele, null);  
   }  
   //解析Bean定义资源文件中的<Bean>元素，这个方法中主要处理<Bean>元素的id，name  
   //和别名属性  
   public BeanDefinitionHolder parseBeanDefinitionElement(Element ele, BeanDefinition containingBean) {  
       //获取<Bean>元素中的id属性值  
       String id = ele.getAttribute(ID_ATTRIBUTE);  
       //获取<Bean>元素中的name属性值  
       String nameAttr = ele.getAttribute(NAME_ATTRIBUTE);  
       ////获取<Bean>元素中的alias属性值  
       List<String> aliases = new ArrayList<String>();  
       //将<Bean>元素中的所有name属性值存放到别名中  
       if (StringUtils.hasLength(nameAttr)) {  
           String[] nameArr = StringUtils.tokenizeToStringArray(nameAttr, BEAN_NAME_DELIMITERS);  
           aliases.addAll(Arrays.asList(nameArr));  
       }  
       String beanName = id;  
       //如果<Bean>元素中没有配置id属性时，将别名中的第一个值赋值给beanName  
       if (!StringUtils.hasText(beanName) && !aliases.isEmpty()) {  
           beanName = aliases.remove(0);  
           if (logger.isDebugEnabled()) {  
               logger.debug("No XML 'id' specified - using '" + beanName +  
                       "' as bean name and " + aliases + " as aliases");  
           }  
       }  
       //检查<Bean>元素所配置的id或者name的唯一性，containingBean标识<Bean>  
       //元素中是否包含子<Bean>元素  
       if (containingBean == null) {  
           //检查<Bean>元素所配置的id、name或者别名是否重复  
           checkNameUniqueness(beanName, aliases, ele);  
       }  
       //详细对<Bean>元素中配置的Bean定义进行解析的地方  
       AbstractBeanDefinition beanDefinition = parseBeanDefinitionElement(ele, beanName, containingBean);  
       if (beanDefinition != null) {  
           if (!StringUtils.hasText(beanName)) {  
               try {  
                   if (containingBean != null) {  
                       //如果<Bean>元素中没有配置id、别名或者name，且没有包含子//<Bean>元素，为解析的Bean生成一个唯一beanName并注册  
                       beanName = BeanDefinitionReaderUtils.generateBeanName(  
                               beanDefinition, this.readerContext.getRegistry(), true);  
                   }  
                   else {  
                       //如果<Bean>元素中没有配置id、别名或者name，且包含了子//<Bean>元素，为解析的Bean使用别名向IoC容器注册  
                       beanName = this.readerContext.generateBeanName(beanDefinition);  
                       //为解析的Bean使用别名注册时，为了向后兼容                                    //Spring1.2/2.0，给别名添加类名后缀  
                       String beanClassName = beanDefinition.getBeanClassName();  
                       if (beanClassName != null &&  
                               beanName.startsWith(beanClassName) && beanName.length() > beanClassName.length() &&  
                               !this.readerContext.getRegistry().isBeanNameInUse(beanClassName)) {  
                           aliases.add(beanClassName);  
                       }  
                   }  
                   if (logger.isDebugEnabled()) {  
                       logger.debug("Neither XML 'id' nor 'name' specified - " +  
                               "using generated bean name [" + beanName + "]");  
                   }  
               }  
               catch (Exception ex) {  
                   error(ex.getMessage(), ele);  
                   return null;  
               }  
           }  
           String[] aliasesArray = StringUtils.toStringArray(aliases);  
           return new BeanDefinitionHolder(beanDefinition, beanName, aliasesArray);  
       }  
       //当解析出错时，返回null  
       return null;  
   }  
   //详细对<Bean>元素中配置的Bean定义其他属性进行解析，由于上面的方法中已经对//Bean的id、name和别名等属性进行了处理，该方法中主要处理除这三个以外的其他属性数据  
   public AbstractBeanDefinition parseBeanDefinitionElement(  
           Element ele, String beanName, BeanDefinition containingBean) {  
       //记录解析的<Bean>  
       this.parseState.push(new BeanEntry(beanName));  
       //这里只读取<Bean>元素中配置的class名字，然后载入到BeanDefinition中去  
       //只是记录配置的class名字，不做实例化，对象的实例化在依赖注入时完成  
       String className = null;  
       if (ele.hasAttribute(CLASS_ATTRIBUTE)) {  
           className = ele.getAttribute(CLASS_ATTRIBUTE).trim();  
       }  
       try {  
           String parent = null;  
           //如果<Bean>元素中配置了parent属性，则获取parent属性的值  
           if (ele.hasAttribute(PARENT_ATTRIBUTE)) {  
               parent = ele.getAttribute(PARENT_ATTRIBUTE);  
           }  
           //根据<Bean>元素配置的class名称和parent属性值创建BeanDefinition  
           //为载入Bean定义信息做准备  
           AbstractBeanDefinition bd = createBeanDefinition(className, parent);  
           //对当前的<Bean>元素中配置的一些属性进行解析和设置，如配置的单态(singleton)属性等  
           parseBeanDefinitionAttributes(ele, beanName, containingBean, bd);  
           //为<Bean>元素解析的Bean设置description信息 bd.setDescription(DomUtils.getChildElementValueByTagName(ele, DESCRIPTION_ELEMENT));  
           //对<Bean>元素的meta(元信息)属性解析  
           parseMetaElements(ele, bd);  
           //对<Bean>元素的lookup-method属性解析  
           parseLookupOverrideSubElements(ele, bd.getMethodOverrides());  
           //对<Bean>元素的replaced-method属性解析  
           parseReplacedMethodSubElements(ele, bd.getMethodOverrides());  
           //解析<Bean>元素的构造方法设置  
           parseConstructorArgElements(ele, bd);  
           //解析<Bean>元素的<property>设置  
           parsePropertyElements(ele, bd);  
           //解析<Bean>元素的qualifier属性  
           parseQualifierElements(ele, bd);  
           //为当前解析的Bean设置所需的资源和依赖对象  
           bd.setResource(this.readerContext.getResource());  
           bd.setSource(extractSource(ele));  
           return bd;  
       }  
       catch (ClassNotFoundException ex) {  
           error("Bean class [" + className + "] not found", ele, ex);  
       }  
       catch (NoClassDefFoundError err) {  
           error("Class that bean class [" + className + "] depends on not found", ele, err);  
       }  
       catch (Throwable ex) {  
           error("Unexpected failure during bean definition parsing", ele, ex);  
       }  
       finally {  
           this.parseState.pop();  
       }  
       //解析<Bean>元素出错时，返回null  
       return null;  
   }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　只要使用过Spring，对Spring配置文件比较熟悉的人，通过对上述源码的分析，就会明白我们在Spring配置文件中<Bean>元素的中配置的属性就是通过该方法解析和设置到Bean中去的。

　　注意：在解析<Bean>元素过程中没有创建和实例化Bean对象，只是创建了Bean对象的定义类BeanDefinition，将<Bean>元素中的配置信息设置到BeanDefinition中作为记录，当依赖注入时才使用这些记录信息创建和实例化具体的Bean对象。

　　上面方法中一些对一些配置如元信息(meta)、qualifier等的解析，我们在Spring中配置时使用的也不多，我们在使用Spring的<Bean>元素时，配置最多的是<property>属性，因此我们下面继续分析源码，了解Bean的属性在解析时是如何设置的。

### 　　13、BeanDefinitionParserDelegate解析<property>元素

　　BeanDefinitionParserDelegate在解析<Bean>调用parsePropertyElements方法解析<Bean>元素中的<property>属性子元素，解析源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//解析<Bean>元素中的<property>子元素  
public void parsePropertyElements(Element beanEle, BeanDefinition bd) {  
   //获取<Bean>元素中所有的子元素  
   NodeList nl = beanEle.getChildNodes();  
   for (int i = 0; i < nl.getLength(); i++) {  
       Node node = nl.item(i);  
       //如果子元素是<property>子元素，则调用解析<property>子元素方法解析  
       if (isCandidateElement(node) && nodeNameEquals(node, PROPERTY_ELEMENT)) {  
           parsePropertyElement((Element) node, bd);  
       }  
   }  
}  
//解析<property>元素  
public void parsePropertyElement(Element ele, BeanDefinition bd) {  
   //获取<property>元素的名字   
   String propertyName = ele.getAttribute(NAME_ATTRIBUTE);  
   if (!StringUtils.hasLength(propertyName)) {  
       error("Tag 'property' must have a 'name' attribute", ele);  
       return;  
   }  
   this.parseState.push(new PropertyEntry(propertyName));  
   try {  
       //如果一个Bean中已经有同名的property存在，则不进行解析，直接返回。  
       //即如果在同一个Bean中配置同名的property，则只有第一个起作用  
       if (bd.getPropertyValues().contains(propertyName)) {  
           error("Multiple 'property' definitions for property '" + propertyName + "'", ele);  
           return;  
       }  
       //解析获取property的值  
       Object val = parsePropertyValue(ele, bd, propertyName);  
       //根据property的名字和值创建property实例  
       PropertyValue pv = new PropertyValue(propertyName, val);  
       //解析<property>元素中的属性  
       parseMetaElements(ele, pv);  
       pv.setSource(extractSource(ele));  
       bd.getPropertyValues().addPropertyValue(pv);  
   }  
   finally {  
       this.parseState.pop();  
   }  
}  
//解析获取property值  
public Object parsePropertyValue(Element ele, BeanDefinition bd, String propertyName) {  
   String elementName = (propertyName != null) ?  
                   "<property> element for property '" + propertyName + "'" :  
                   "<constructor-arg> element";  
   //获取<property>的所有子元素，只能是其中一种类型:ref,value,list等  
   NodeList nl = ele.getChildNodes();  
   Element subElement = null;  
   for (int i = 0; i < nl.getLength(); i++) {  
       Node node = nl.item(i);  
       //子元素不是description和meta属性  
       if (node instanceof Element && !nodeNameEquals(node, DESCRIPTION_ELEMENT) &&  
               !nodeNameEquals(node, META_ELEMENT)) {  
           if (subElement != null) {  
               error(elementName + " must not contain more than one sub-element", ele);  
           }  
           else {//当前<property>元素包含有子元素  
               subElement = (Element) node;  
           }  
       }  
   }  
   //判断property的属性值是ref还是value，不允许既是ref又是value  
   boolean hasRefAttribute = ele.hasAttribute(REF_ATTRIBUTE);  
   boolean hasValueAttribute = ele.hasAttribute(VALUE_ATTRIBUTE);  
   if ((hasRefAttribute && hasValueAttribute) ||  
           ((hasRefAttribute || hasValueAttribute) && subElement != null)) {  
       error(elementName +  
               " is only allowed to contain either 'ref' attribute OR 'value' attribute OR sub-element", ele);  
   }  
   //如果属性是ref，创建一个ref的数据对象RuntimeBeanReference，这个对象  
   //封装了ref信息  
   if (hasRefAttribute) {  
       String refName = ele.getAttribute(REF_ATTRIBUTE);  
       if (!StringUtils.hasText(refName)) {  
           error(elementName + " contains empty 'ref' attribute", ele);  
       }  
       //一个指向运行时所依赖对象的引用  
       RuntimeBeanReference ref = new RuntimeBeanReference(refName);  
       //设置这个ref的数据对象是被当前的property对象所引用  
       ref.setSource(extractSource(ele));  
       return ref;  
   }  
    //如果属性是value，创建一个value的数据对象TypedStringValue，这个对象  
   //封装了value信息  
   else if (hasValueAttribute) {  
       //一个持有String类型值的对象  
       TypedStringValue valueHolder = new TypedStringValue(ele.getAttribute(VALUE_ATTRIBUTE));  
       //设置这个value数据对象是被当前的property对象所引用  
       valueHolder.setSource(extractSource(ele));  
       return valueHolder;  
   }  
   //如果当前<property>元素还有子元素  
   else if (subElement != null) {  
       //解析<property>的子元素  
       return parsePropertySubElement(subElement, bd);  
   }  
   else {  
       //propery属性中既不是ref，也不是value属性，解析出错返回null        error(elementName + " must specify a ref or value", ele);  
       return null;  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过对上述源码的分析，我们可以了解在Spring配置文件中，<Bean>元素中<property>元素的相关配置是如何处理的：

- ref：被封装为指向依赖对象一个引用。
- value：配置都会封装成一个字符串类型的对象。
- ref和value都通过“解析的数据类型属性值.setSource(extractSource(ele));”方法将属性值/引用与所引用的属性关联起来。

　　在方法的最后对于<property>元素的子元素通过parsePropertySubElement 方法解析。

### 　　14、解析<property>元素的子元素

　　在BeanDefinitionParserDelegate类中的parsePropertySubElement方法对<property>中的子元素解析，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//解析<property>元素中ref,value或者集合等子元素  
public Object parsePropertySubElement(Element ele, BeanDefinition bd, String defaultValueType) {  
   //如果<property>没有使用Spring默认的命名空间，则使用用户自定义的规则解析//内嵌元素  
   if (!isDefaultNamespace(ele)) {  
       return parseNestedCustomElement(ele, bd);  
   }  
   //如果子元素是bean，则使用解析<Bean>元素的方法解析  
   else if (nodeNameEquals(ele, BEAN_ELEMENT)) {  
       BeanDefinitionHolder nestedBd = parseBeanDefinitionElement(ele, bd);  
       if (nestedBd != null) {  
           nestedBd = decorateBeanDefinitionIfRequired(ele, nestedBd, bd);  
       }  
       return nestedBd;  
   }  
   //如果子元素是ref，ref中只能有以下3个属性：bean、local、parent  
   else if (nodeNameEquals(ele, REF_ELEMENT)) {  
       //获取<property>元素中的bean属性值，引用其他解析的Bean的名称  
       //可以不再同一个Spring配置文件中，具体请参考Spring对ref的配置规则  
       String refName = ele.getAttribute(BEAN_REF_ATTRIBUTE);  
       boolean toParent = false;  
       if (!StringUtils.hasLength(refName)) {  
            //获取<property>元素中的local属性值，引用同一个Xml文件中配置  
            //的Bean的id，local和ref不同，local只能引用同一个配置文件中的Bean  
           refName = ele.getAttribute(LOCAL_REF_ATTRIBUTE);  
           if (!StringUtils.hasLength(refName)) {  
               //获取<property>元素中parent属性值，引用父级容器中的Bean  
               refName = ele.getAttribute(PARENT_REF_ATTRIBUTE);  
               toParent = true;  
               if (!StringUtils.hasLength(refName)) {  
                   error("'bean', 'local' or 'parent' is required for <ref> element", ele);  
                   return null;  
               }  
           }  
       }  
       //没有配置ref的目标属性值  
       if (!StringUtils.hasText(refName)) {  
           error("<ref> element contains empty target attribute", ele);  
           return null;  
       }  
       //创建ref类型数据，指向被引用的对象  
       RuntimeBeanReference ref = new RuntimeBeanReference(refName, toParent);  
       //设置引用类型值是被当前子元素所引用  
       ref.setSource(extractSource(ele));  
       return ref;  
   }  
   //如果子元素是<idref>，使用解析ref元素的方法解析  
   else if (nodeNameEquals(ele, IDREF_ELEMENT)) {  
       return parseIdRefElement(ele);  
   }  
   //如果子元素是<value>，使用解析value元素的方法解析  
   else if (nodeNameEquals(ele, VALUE_ELEMENT)) {  
       return parseValueElement(ele, defaultValueType);  
   }  
   //如果子元素是null，为<property>设置一个封装null值的字符串数据  
   else if (nodeNameEquals(ele, NULL_ELEMENT)) {  
       TypedStringValue nullHolder = new TypedStringValue(null);  
       nullHolder.setSource(extractSource(ele));  
       return nullHolder;  
   }  
   //如果子元素是<array>，使用解析array集合子元素的方法解析  
   else if (nodeNameEquals(ele, ARRAY_ELEMENT)) {  
       return parseArrayElement(ele, bd);  
   }  
   //如果子元素是<list>，使用解析list集合子元素的方法解析  
   else if (nodeNameEquals(ele, LIST_ELEMENT)) {  
       return parseListElement(ele, bd);  
   }  
   //如果子元素是<set>，使用解析set集合子元素的方法解析  
   else if (nodeNameEquals(ele, SET_ELEMENT)) {  
       return parseSetElement(ele, bd);  
   }  
   //如果子元素是<map>，使用解析map集合子元素的方法解析  
   else if (nodeNameEquals(ele, MAP_ELEMENT)) {  
       return parseMapElement(ele, bd);  
   }  
   //如果子元素是<props>，使用解析props集合子元素的方法解析  
   else if (nodeNameEquals(ele, PROPS_ELEMENT)) {  
       return parsePropsElement(ele);  
   }  
   //既不是ref，又不是value，也不是集合，则子元素配置错误，返回null  
   else {  
       error("Unknown property sub-element: [" + ele.getNodeName() + "]", ele);  
       return null;  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过上述源码分析，我们明白了在Spring配置文件中，对<property>元素中配置的Array、List、Set、Map、Prop等各种集合子元素的都通过上述方法解析，生成对应的数据对象，比如ManagedList、ManagedArray、ManagedSet等，这些Managed类是Spring对象BeanDefiniton的数据封装，对集合数据类型的具体解析有各自的解析方法实现，解析方法的命名非常规范，一目了然，我们对<list>集合元素的解析方法进行源码分析，了解其实现过程。

### 　　15、解析<list>子元素

　　在BeanDefinitionParserDelegate类中的parseListElement方法就是具体实现解析<property>元素中的<list>集合子元素，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//解析<list>集合子元素  
public List parseListElement(Element collectionEle, BeanDefinition bd) {  
   //获取<list>元素中的value-type属性，即获取集合元素的数据类型  
   String defaultElementType = collectionEle.getAttribute(VALUE_TYPE_ATTRIBUTE);  
   //获取<list>集合元素中的所有子节点  
   NodeList nl = collectionEle.getChildNodes();  
   //Spring中将List封装为ManagedList  
   ManagedList<Object> target = new ManagedList<Object>(nl.getLength());  
   target.setSource(extractSource(collectionEle));  
   //设置集合目标数据类型  
   target.setElementTypeName(defaultElementType);  
   target.setMergeEnabled(parseMergeAttribute(collectionEle));  
   //具体的<list>元素解析  
   parseCollectionElements(nl, target, bd, defaultElementType);  
   return target;  
}   
//具体解析<list>集合元素，<array>、<list>和<set>都使用该方法解析  
protected void parseCollectionElements(  
       NodeList elementNodes, Collection<Object> target, BeanDefinition bd, String defaultElementType) {  
   //遍历集合所有节点  
   for (int i = 0; i < elementNodes.getLength(); i++) {  
       Node node = elementNodes.item(i);  
       //节点不是description节点  
       if (node instanceof Element && !nodeNameEquals(node, DESCRIPTION_ELEMENT)) {  
           //将解析的元素加入集合中，递归调用下一个子元素  
           target.add(parsePropertySubElement((Element) node, bd, defaultElementType));  
       }  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　经过对Spring Bean定义资源文件转换的Document对象中的元素层层解析，Spring IOC现在已经将XML形式定义的Bean定义资源文件转换为Spring IOC所识别的数据结构——BeanDefinition，它是Bean定义资源文件中配置的POJO对象在Spring IoC容器中的映射，我们可以通过AbstractBeanDefinition为入口，供IOC容器进行索引、查询和操作。

　　通过Spring IOC容器对Bean定义资源的解析后，IOC容器大致完成了管理Bean对象的准备工作，即初始化过程，但是最为重要的依赖注入还没有发生，现在在IOC容器中BeanDefinition存储的只是一些静态信息，接下来需要向容器注册Bean定义信息才能全部完成IOC容器的初始化过程。

### 　　16、解析过后的BeanDefinition在IOC容器中的注册

　　接下来会到我们第3步中分析DefaultBeanDefinitionDocumentReader对Bean定义转换的Document对象解析的流程中，在其parseDefaultElement方法中完成对Document对象的解析后得到封装BeanDefinition的BeanDefinitionHold对象，然后调用BeanDefinitionReaderUtils的registerBeanDefinition方法向IOC容器注册解析的Bean，BeanDefinitionReaderUtils的注册的源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//将解析的BeanDefinitionHold注册到容器中 
public static void registerBeanDefinition(BeanDefinitionHolder definitionHolder, BeanDefinitionRegistry registry)  
    throws BeanDefinitionStoreException {  
        //获取解析的BeanDefinition的名称
         String beanName = definitionHolder.getBeanName();  
        //向IoC容器注册BeanDefinition 
        registry.registerBeanDefinition(beanName, definitionHolder.getBeanDefinition());  
        //如果解析的BeanDefinition有别名，向容器为其注册别名  
         String[] aliases = definitionHolder.getAliases();  
        if (aliases != null) {  
            for (String aliase : aliases) {  
                registry.registerAlias(beanName, aliase);  
            }  
        }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　当调用BeanDefinitionReaderUtils向IOC容器注册解析的BeanDefinition时，真正完成注册功能的是DefaultListableBeanFactory。

### 　　17、DefaultListableBeanFactory向IOC容器注册解析后的BeanDefinition

　　DefaultListableBeanFactory中使用一个ConcurrentHashMap<String, BeanDefinition>的集合对象存放IoC容器中注册解析的BeanDefinition，向IOC容器注册的主要源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//存储注册的俄BeanDefinition  
private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<String, BeanDefinition>();  
//向IoC容器注册解析的BeanDefiniton  
public void registerBeanDefinition(String beanName, BeanDefinition beanDefinition)  
       throws BeanDefinitionStoreException {  
   Assert.hasText(beanName, "Bean name must not be empty");  
   Assert.notNull(beanDefinition, "BeanDefinition must not be null");  
   //校验解析的BeanDefiniton  
   if (beanDefinition instanceof AbstractBeanDefinition) {  
       try {  
           ((AbstractBeanDefinition) beanDefinition).validate();  
       }  
       catch (BeanDefinitionValidationException ex) {  
           throw new BeanDefinitionStoreException(beanDefinition.getResourceDescription(), beanName,  
                   "Validation of bean definition failed", ex);  
       }  
   }  
   //注册的过程中需要线程同步，以保证数据的一致性  
   synchronized (this.beanDefinitionMap) {  
       Object oldBeanDefinition = this.beanDefinitionMap.get(beanName);  
       //检查是否有同名的BeanDefinition已经在IoC容器中注册，如果已经注册，  
       //并且不允许覆盖已注册的Bean，则抛出注册失败异常  
       if (oldBeanDefinition != null) {  
           if (!this.allowBeanDefinitionOverriding) {  
               throw new BeanDefinitionStoreException(beanDefinition.getResourceDescription(), beanName,  
                       "Cannot register bean definition [" + beanDefinition + "] for bean '" + beanName +  
                       "': There is already [" + oldBeanDefinition + "] bound.");  
           }  
           else {//如果允许覆盖，则同名的Bean，后注册的覆盖先注册的  
               if (this.logger.isInfoEnabled()) {  
                   this.logger.info("Overriding bean definition for bean '" + beanName +  
                           "': replacing [" + oldBeanDefinition + "] with [" + beanDefinition + "]");  
               }  
           }  
       }  
       //IoC容器中没有已经注册同名的Bean，按正常注册流程注册  
       else {  
           this.beanDefinitionNames.add(beanName);  
           this.frozenBeanDefinitionNames = null;  
       }  
       this.beanDefinitionMap.put(beanName, beanDefinition);  
       //重置所有已经注册过的BeanDefinition的缓存  
       resetBeanDefinition(beanName);  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　至此，Bean定义资源文件中配置的Bean被解析过后，已经注册到IOC容器中，被容器管理起来，真正完成了IOC容器初始化所做的全部工作。现 在IOC容器中已经建立了整个Bean的配置信息，这些BeanDefinition信息已经可以使用，并且可以被检索，IOC容器的作用就是对这些注册的Bean定义信息进行处理和维护。这些的注册的Bean定义信息是IOC容器控制反转的基础，正是有了这些注册的数据，容器才可以进行依赖注入。

　　总结

- 初始化的入口在容器实现中的 **refresh**()调用来完成
- 对 bean 定义载入 IOC 容器使用的方法是 **loadBeanDefinition**,其中的大致过程如下：通过 ResourceLoader 来完成资源文件位置的定位，**DefaultResourceLoader** 是默认的实现，同时上下文本身就给出了 ResourceLoader 的实现，可以从**类路径，文件系统, URL** 等方式来定为资源位置。如果是 XmlBeanFactory作为 IOC 容器，那么需要为它指定 bean 定义的资源，也就是说 bean 定义文件时通过抽象成 Resource 来被 IOC 容器处理的，容器通过 **BeanDefinitionReader**来完成定义信息的解析和 Bean 信息的注册,往往使用的是**XmlBeanDefinitionReader** 来解析 bean 的 xml 定义文件 - 实际的处理过程是委托给 **BeanDefinitionParserDelegate** 来完成的，从而得到 bean 的定义信息，这些信息在 Spring 中使用 BeanDefinition 对象来表示 - 这个名字可以让我们想到loadBeanDefinition,RegisterBeanDefinition 这些相关的方法 - 他们都是为处理 BeanDefinitin 服务的， 容器解析得到 BeanDefinition 以后，需要把它在 IOC 容器中注册，这由 IOC 实现 **BeanDefinitionRegistry** 接口来实现。注册过程就是在 IOC 容器内部维护的一个ConcurrentHashMap 来保存得到的 BeanDefinition 的过程。这个 ConcurrentHashMap 是 IOC 容器持有 bean 信息的场所，以后对 bean 的操作都是围绕这个ConcurrentHashMap 来实现的.
- 然后我们就可以通过 BeanFactory 和 ApplicationContext 来享受到 Spring IOC 的服务了,在使用 IOC 容器的时候，我们注意到除了少量粘合代码，绝大多数以正确 IoC 风格编写的应用程序代码完全不用关心如何到达工厂，因为容器将把这些对象与容器管理的其他对象钩在一起。基本的策略是把工厂放到已知的地方，最好是放在对预期使用的上下文有意义的地方，以及代码将实际需要访问工厂的地方。 Spring 本身提供了对声明式载入 web 应用程序用法的应用程序上下文,并将其存储在ServletContext 中的框架实现。具体可以参见以后的文章 

　　在使用 Spring IOC 容器的时候我们还需要区别两个概念：

​    Beanfactory 和 Factory bean，其中 BeanFactory 指的是 IOC 容器的编程抽象，比如 ApplicationContext， XmlBeanFactory 等，这些都是 IOC 容器的具体表现，需要使用什么样的容器由客户决定,但 Spring 为我们提供了丰富的选择。 FactoryBean 只是一个可以在 IOC容器中被管理的一个 bean，是对各种处理过程和资源使用的抽象，Factory bean 在需要时产生另一个对象，而不返回 FactoryBean本身,我们可以把它看成是一个抽象工厂，对它的调用返回的是工厂生产的产品。所有的 Factory bean 都实现特殊的org.springframework.beans.factory.FactoryBean 接口，当使用容器中 factory bean 的时候，该容器不会返回 factory bean 本身,而是返回其生成的对象。Spring 包括了大部分的通用资源和服务访问抽象的 Factory bean 的实现，其中包括：对 JNDI 查询的处理、对代理对象的处理、对事务性代理的处理、对 RMI 代理的处理等，这些我们都可以看成是具体的工厂，看成是Spring 为我们建立好的工厂。也就是说 Spring 通过使用抽象工厂模式为我们准备了一系列工厂来生产一些特定的对象,免除我们手工重复的工作，我们要使用时只需要在 IOC 容器里配置好就能很方便的使用了。

## 五、IOC容器的依赖注入

### 　　1、依赖注入发生的时间

　　当Spring IoC容器完成了Bean定义资源的定位、载入、解析和注册以后，IOC容器中已经管理类Bean定义的相关数据，但是此时IoC容器还没有对所管理的Bean进行依赖注入，依赖注入在以下两种情况发生：

- 用户第一次通过getBean方法向IOC容索要Bean时，IOC容器触发依赖注入。
- 当用户在Bean定义资源中为<Bean>元素配置了lazy-init属性，即让容器在解析注册Bean定义时进行预实例化，触发依赖注入。

　　BeanFactory接口定义了Spring IOC容器的基本功能规范，是Spring IOC容器所应遵守的最底层和最基本的编程规范。BeanFactory接口中定义了几个getBean方法，就是用户向IOC容器索取管理的Bean的方法，我们通过分析其子类的具体实现，理解Spring IOC容器在用户索取Bean时如何完成依赖注入。

　　在BeanFactory中我们看到getBean（String…）函数，它的具体实现在AbstractBeanFactory中

### 　　2、AbstractBeanFactory通过getBean向IOC容器获取被管理的Bean

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//获取IoC容器中指定名称的Bean  
public Object getBean(String name) throws BeansException {  
   //doGetBean才是真正向IoC容器获取被管理Bean的过程  
   return doGetBean(name, null, null, false);  
}  
//获取IoC容器中指定名称和类型的Bean  
public <T> T getBean(String name, Class<T> requiredType) throws BeansException {  
   //doGetBean才是真正向IoC容器获取被管理Bean的过程  
   return doGetBean(name, requiredType, null, false);  
}  
//获取IoC容器中指定名称和参数的Bean  
public Object getBean(String name, Object... args) throws BeansException {  
   //doGetBean才是真正向IoC容器获取被管理Bean的过程  
   return doGetBean(name, null, args, false);  
}  
//获取IoC容器中指定名称、类型和参数的Bean  
public <T> T getBean(String name, Class<T> requiredType, Object... args) throws BeansException {  
//doGetBean才是真正向IoC容器获取被管理Bean的过程  
   return doGetBean(name, requiredType, args, false);  
}  
//真正实现向IoC容器获取Bean的功能，也是触发依赖注入功能的地方  
@SuppressWarnings("unchecked")  
protected <T> T doGetBean(  
       final String name, final Class<T> requiredType, final Object[] args, boolean typeCheckOnly)  
       throws BeansException {  
   //根据指定的名称获取被管理Bean的名称，剥离指定名称中对容器的相关依赖，如果指定的是别名，将别名转换为规范的Bean名称  
   final String beanName = transformedBeanName(name);  
   Object bean;  
   //先从缓存中取是否已经有被创建过的单态类型的Bean，对于单态模式的Bean整个IoC容器中只创建一次，不需要重复创建  
   Object sharedInstance = getSingleton(beanName);  
   //IOC容器创建单态模式Bean实例对象  
   if (sharedInstance != null && args == null) {  
       if (logger.isDebugEnabled()) {  
           //如果指定名称的Bean在容器中已有单态模式的Bean被创建，直接返回已经创建的Bean  
           if (isSingletonCurrentlyInCreation(beanName)) {  
               logger.debug("Returning eagerly cached instance of singleton bean '" + beanName +  
                       "' that is not fully initialized yet - a consequence of a circular reference");  
           }  
           else {  
               logger.debug("Returning cached instance of singleton bean '" + beanName + "'");  
           }  
       }  
       //获取给定Bean的实例对象，主要是完成FactoryBean的相关处理  
       //注意：BeanFactory是管理容器中Bean的工厂，而FactoryBean是创建创建对象的工厂Bean，两者之间有区别  
       bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);  
   }  
   else {　　　　//缓存没有正在创建的单态模式Bean  
       //缓存中已经有已经创建的原型模式Bean，但是由于循环引用的问题导致实例化对象失败  
       if (isPrototypeCurrentlyInCreation(beanName)) {  
           throw new BeanCurrentlyInCreationException(beanName);  
       }  
       //对IoC容器中是否存在指定名称的BeanDefinition进行检查，首先检查是否能在当前的BeanFactory中获取的所需要的Bean　　　　//如果不能则委托当前容器的父级容器去查找，如果还是找不到则沿着容器的继承体系向父级容器查找  
       BeanFactory parentBeanFactory = getParentBeanFactory();  
       //当前容器的父级容器存在，且当前容器中不存在指定名称的Bean  
       if (parentBeanFactory != null && !containsBeanDefinition(beanName)) {  
           //解析指定Bean名称的原始名称  
           String nameToLookup = originalBeanName(name);  
           if (args != null) {  
               //委派父级容器根据指定名称和显式的参数查找  
               return (T) parentBeanFactory.getBean(nameToLookup, args);  
           }  
           else {  
               //委派父级容器根据指定名称和类型查找  
               return parentBeanFactory.getBean(nameToLookup, requiredType);  
           }  
       }  
       //创建的Bean是否需要进行类型验证，一般不需要  
       if (!typeCheckOnly) {  
           //向容器标记指定的Bean已经被创建  
           markBeanAsCreated(beanName);  
       }  
        //根据指定Bean名称获取其父级的Bean定义，主要解决Bean继承时子类合并父类公共属性问题  
       final RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);  
       checkMergedBeanDefinition(mbd, beanName, args);  
       //获取当前Bean所有依赖Bean的名称  
       String[] dependsOn = mbd.getDependsOn();  
       //如果当前Bean有依赖Bean  
       if (dependsOn != null) {  
           for (String dependsOnBean : dependsOn) {  
               //递归调用getBean方法，获取当前Bean的依赖Bean  
               getBean(dependsOnBean);  
               //把被依赖Bean注册给当前依赖的Bean  
               registerDependentBean(dependsOnBean, beanName);  
           }  
       }  
       //创建单态模式Bean的实例对象  
       if (mbd.isSingleton()) {  
       //这里使用了一个匿名内部类，创建Bean实例对象，并且注册给所依赖的对象  
           sharedInstance = getSingleton(beanName, new ObjectFactory() {  
               public Object getObject() throws BeansException {  
                   try {  
                       //创建一个指定Bean实例对象，如果有父级继承，则合并子//类和父类的定义  
                       return createBean(beanName, mbd, args);  
                   }  
                   catch (BeansException ex) {  
                       //显式地从容器单态模式Bean缓存中清除实例对象  
                       destroySingleton(beanName);  
                       throw ex;  
                   }  
               }  
           });  
           //获取给定Bean的实例对象  
           bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);  
       }  
       //IoC容器创建原型模式Bean实例对象  
       else if (mbd.isPrototype()) {  
           //原型模式(Prototype)是每次都会创建一个新的对象  
           Object prototypeInstance = null;  
           try {  
               //回调beforePrototypeCreation方法，默认的功能是注册当前创//建的原型对象  
               beforePrototypeCreation(beanName);  
               //创建指定Bean对象实例  
               prototypeInstance = createBean(beanName, mbd, args);  
           }  
           finally {  
               //回调afterPrototypeCreation方法，默认的功能告诉IoC容器指//定Bean的原型对象不再创建了  
               afterPrototypeCreation(beanName);  
           }  
           //获取给定Bean的实例对象  
           bean = getObjectForBeanInstance(prototypeInstance, name, beanName, mbd);  
       }  
       //要创建的Bean既不是单态模式，也不是原型模式，则根据Bean定义资源中配置的生命周期范围，选择实例化Bean的合适方法，这种在Web应用程序中比较常用
       //如：request、session、application等生命周期  
       else {  
           String scopeName = mbd.getScope();  
           final Scope scope = this.scopes.get(scopeName);  
           //Bean定义资源中没有配置生命周期范围，则Bean定义不合法  
           if (scope == null) {  
               throw new IllegalStateException("No Scope registered for scope '" + scopeName + "'");  
           }  
           try {  
               //这里又使用了一个匿名内部类，获取一个指定生命周期范围的实例  
               Object scopedInstance = scope.get(beanName, new ObjectFactory() {  
                   public Object getObject() throws BeansException {  
                       beforePrototypeCreation(beanName);  
                       try {  
                           return createBean(beanName, mbd, args);  
                       }  
                       finally {  
                           afterPrototypeCreation(beanName);  
                       }  
                   }  
               });  
               //获取给定Bean的实例对象  
               bean = getObjectForBeanInstance(scopedInstance, name, beanName, mbd);  
           }  
           catch (IllegalStateException ex) {  
               throw new BeanCreationException(beanName,  
                       "Scope '" + scopeName + "' is not active for the current thread; " +  
                       "consider defining a scoped proxy for this bean if you intend to refer to it from a singleton",  
                       ex);  
           }  
       }  
   }  
   //对创建的Bean实例对象进行类型检查  
   if (requiredType != null && bean != null && !requiredType.isAssignableFrom(bean.getClass())) {  
       throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());  
   }  
   return (T) bean;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过上面对向IoC容器获取Bean方法的分析，我们可以看到在Spring中，如果Bean定义的单例模式(Singleton)，则容器在创建之前先从缓存中查找，以确保整个容器中只存在一个实例对象。如果Bean定义的是原型模式(Prototype)，则容器每次都会创建一个新的实例对象。除此之外，Bean定义还可以扩展为指定其生命周期范围。

　　上面的源码只是定义了根据Bean定义的模式，采取的不同创建Bean实例对象的策略，具体的Bean实例对象的创建过程由实现了ObejctFactory接口的匿名内部类的createBean方法完成，ObejctFactory使用委派模式，**具体的Bean实例创建过程交由其实现类AbstractAutowireCapableBeanFactory完成**，我们继续分析AbstractAutowireCapableBeanFactory的createBean方法的源码，理解其创建Bean实例的具体实现过程。

### 　　3、AbstractAutowireCapableBeanFactory创建Bean实例对象

　　AbstractAutowireCapableBeanFactory类实现了ObejctFactory接口，创建容器指定的Bean实例对象，同时还对创建的Bean实例对象进行初始化处理。其创建Bean实例对象的方法源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//创建Bean实例对象  
protected Object createBean(final String beanName, final RootBeanDefinition mbd, final Object[] args)  
       throws BeanCreationException {  
   if (logger.isDebugEnabled()) {  
       logger.debug("Creating instance of bean '" + beanName + "'");  
   }  
   //判断需要创建的Bean是否可以实例化，即是否可以通过当前的类加载器加载  
   resolveBeanClass(mbd, beanName);  
   //校验和准备Bean中的方法覆盖  
   try {  
       mbd.prepareMethodOverrides();  
   }  
   catch (BeanDefinitionValidationException ex) {  
       throw new BeanDefinitionStoreException(mbd.getResourceDescription(),  
               beanName, "Validation of method overrides failed", ex);  
   }  
   try {  
       //如果Bean配置了初始化前和初始化后的处理器，则试图返回一个需要创建//Bean的代理对象  
       Object bean = resolveBeforeInstantiation(beanName, mbd);  
       if (bean != null) {  
           return bean;  
       }  
   }  
   catch (Throwable ex) {  
       throw new BeanCreationException(mbd.getResourceDescription(), beanName,  
               "BeanPostProcessor before instantiation of bean failed", ex);  
   }  
   //创建Bean的入口  
   Object beanInstance = doCreateBean(beanName, mbd, args);  
   if (logger.isDebugEnabled()) {  
       logger.debug("Finished creating instance of bean '" + beanName + "'");  
   }  
   return beanInstance;  
}  
//真正创建Bean的方法  
protected Object doCreateBean(final String beanName, final RootBeanDefinition mbd, final Object[] args) {  
   //封装被创建的Bean对象  
   BeanWrapper instanceWrapper = null;  
   if (mbd.isSingleton()){//单态模式的Bean，先从容器中缓存中获取同名Bean  
       instanceWrapper = this.factoryBeanInstanceCache.remove(beanName);  
   }  
   if (instanceWrapper == null) {  
       //创建实例对象  
       instanceWrapper = createBeanInstance(beanName, mbd, args);  
   }  
   final Object bean = (instanceWrapper != null ? instanceWrapper.getWrappedInstance() : null);  
   //获取实例化对象的类型  
   Class beanType = (instanceWrapper != null ? instanceWrapper.getWrappedClass() : null);  
   //调用PostProcessor后置处理器  
   synchronized (mbd.postProcessingLock) {  
       if (!mbd.postProcessed) {  
           applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);  
           mbd.postProcessed = true;  
       }  
   }  
   // Eagerly cache singletons to be able to resolve circular references  
   //向容器中缓存单态模式的Bean对象，以防循环引用  
   boolean earlySingletonExposure = (mbd.isSingleton() && this.allowCircularReferences &&  
           isSingletonCurrentlyInCreation(beanName));  
   if (earlySingletonExposure) {  
       if (logger.isDebugEnabled()) {  
           logger.debug("Eagerly caching bean '" + beanName +  
                   "' to allow for resolving potential circular references");  
       }  
       //这里是一个匿名内部类，为了防止循环引用，尽早持有对象的引用  
       addSingletonFactory(beanName, new ObjectFactory() {  
           public Object getObject() throws BeansException {  
               return getEarlyBeanReference(beanName, mbd, bean);  
           }  
       });  
   }  
   //Bean对象的初始化，依赖注入在此触发  
   //这个exposedObject在初始化完成之后返回作为依赖注入完成后的Bean  
   Object exposedObject = bean;  
   try {  
       //将Bean实例对象封装，并且Bean定义中配置的属性值赋值给实例对象  
       populateBean(beanName, mbd, instanceWrapper);  
       if (exposedObject != null) {  
           //初始化Bean对象  
           exposedObject = initializeBean(beanName, exposedObject, mbd);  
       }  
   }  
   catch (Throwable ex) {  
       if (ex instanceof BeanCreationException && beanName.equals(((BeanCreationException) ex).getBeanName())) {  
           throw (BeanCreationException) ex;  
       }  
       else {  
           throw new BeanCreationException(mbd.getResourceDescription(), beanName, "Initialization of bean failed", ex);  
       }  
   }  
   if (earlySingletonExposure) {  
       //获取指定名称的已注册的单态模式Bean对象  
       Object earlySingletonReference = getSingleton(beanName, false);  
       if (earlySingletonReference != null) {  
           //根据名称获取的以注册的Bean和正在实例化的Bean是同一个  
           if (exposedObject == bean) {  
               //当前实例化的Bean初始化完成  
               exposedObject = earlySingletonReference;  
           }  
           //当前Bean依赖其他Bean，并且当发生循环引用时不允许新创建实例对象  
           else if (!this.allowRawInjectionDespiteWrapping && hasDependentBean(beanName)) {  
               String[] dependentBeans = getDependentBeans(beanName);  
               Set<String> actualDependentBeans = new LinkedHashSet<String>(dependentBeans.length);  
               //获取当前Bean所依赖的其他Bean  
               for (String dependentBean : dependentBeans) {  
                   //对依赖Bean进行类型检查  
                   if (!removeSingletonIfCreatedForTypeCheckOnly(dependentBean)) {  
                       actualDependentBeans.add(dependentBean);  
                   }  
               }  
               if (!actualDependentBeans.isEmpty()) {  
                   throw new BeanCurrentlyInCreationException(beanName,  
                           "Bean with name '" + beanName + "' has been injected into other beans [" +  
                           StringUtils.collectionToCommaDelimitedString(actualDependentBeans) +  
                           "] in its raw version as part of a circular reference, but has eventually been " +  
                           "wrapped. This means that said other beans do not use the final version of the " +  
                           "bean. This is often the result of over-eager type matching - consider using " +  
                           "'getBeanNamesOfType' with the 'allowEagerInit' flag turned off, for example.");  
               }  
           }  
       }  
   }  
   //注册完成依赖注入的Bean  
   try {  
       registerDisposableBeanIfNecessary(beanName, bean, mbd);  
   }  
   catch (BeanDefinitionValidationException ex) {  
       throw new BeanCreationException(mbd.getResourceDescription(), beanName, "Invalid destruction signature", ex);  
   }  
   return exposedObject;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过对方法源码的分析，我们看到具体的依赖注入实现在以下两个方法中：

- createBeanInstance：生成Bean所包含的java对象实例。
- populateBean ：对Bean属性的依赖注入进行处理。

　　下面继续分析这两个方法的代码实现。

### 　　4、createBeanInstance方法创建Bean的java实例对象

　　在createBeanInstance方法中，根据指定的初始化策略，使用静态工厂、工厂方法或者容器的自动装配特性生成java实例对象，创建对象的源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//创建Bean的实例对象  
protected BeanWrapper createBeanInstance(String beanName, RootBeanDefinition mbd, Object[] args) {  
   //检查确认Bean是可实例化的  
   Class beanClass = resolveBeanClass(mbd, beanName);  
   //使用工厂方法对Bean进行实例化  
   if (beanClass != null && !Modifier.isPublic(beanClass.getModifiers()) && !mbd.isNonPublicAccessAllowed()) {  
       throw new BeanCreationException(mbd.getResourceDescription(), beanName,  
               "Bean class isn't public, and non-public access not allowed: " + beanClass.getName());  
   }  
   if (mbd.getFactoryMethodName() != null)  {  
       //调用工厂方法实例化  
       return instantiateUsingFactoryMethod(beanName, mbd, args);  
   }  
   //使用容器的自动装配方法进行实例化  
   boolean resolved = false;  
   boolean autowireNecessary = false;  
   if (args == null) {  
       synchronized (mbd.constructorArgumentLock) {  
           if (mbd.resolvedConstructorOrFactoryMethod != null) {  
               resolved = true;  
               autowireNecessary = mbd.constructorArgumentsResolved;  
           }  
       }  
   }  
   if (resolved) {  
       if (autowireNecessary) {  
           //配置了自动装配属性，使用容器的自动装配实例化，容器的自动装配是根据参数类型匹配Bean的构造方法  
           return autowireConstructor(beanName, mbd, null, null);  
       }  
       else {  
           //使用默认的无参构造方法实例化  
           return instantiateBean(beanName, mbd);  
       }  
   }  
   //使用Bean的构造方法进行实例化  
   Constructor[] ctors = determineConstructorsFromBeanPostProcessors(beanClass, beanName);  
   if (ctors != null ||  
           mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_CONSTRUCTOR ||  
           mbd.hasConstructorArgumentValues() || !ObjectUtils.isEmpty(args))  {  
       //使用容器的自动装配特性，调用匹配的构造方法实例化  
       return autowireConstructor(beanName, mbd, ctors, args);  
   }  
   //使用默认的无参构造方法实例化  
   return instantiateBean(beanName, mbd);  
}   
//使用默认的无参构造方法实例化Bean对象  
protected BeanWrapper instantiateBean(final String beanName, final RootBeanDefinition mbd) {  
   try {  
       Object beanInstance;  
       final BeanFactory parent = this;  
       //获取系统的安全管理接口，JDK标准的安全管理API  
       if (System.getSecurityManager() != null) {  
           //这里是一个匿名内置类，根据实例化策略创建实例对象  
           beanInstance = AccessController.doPrivileged(new PrivilegedAction<Object>() {  
               public Object run() {  
                   return getInstantiationStrategy().instantiate(mbd, beanName, parent);  
               }  
           }, getAccessControlContext());  
       }  
       else {  
           //将实例化的对象封装起来  
           beanInstance = getInstantiationStrategy().instantiate(mbd, beanName, parent);  
       }  
       BeanWrapper bw = new BeanWrapperImpl(beanInstance);  
       initBeanWrapper(bw);  
       return bw;  
   }  
   catch (Throwable ex) {  
       throw new BeanCreationException(mbd.getResourceDescription(), beanName, "Instantiation of bean failed", ex);  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　经过对上面的代码分析，我们可以看出，对使用工厂方法和自动装配特性的Bean的实例化相当比较清楚，调用相应的工厂方法或者参数匹配的构造方法即可完成实例化对象的工作，但是对于我们最常使用的默认无参构造方法就需要使用相应的初始化策略(JDK的反射机制或者CGLIB)来进行初始化了，在方法getInstantiationStrategy().instantiate中就具体实现类使用初始策略实例化对象。

### 　　5、SimpleInstantiationStrategy类使用默认的无参构造方法创建Bean实例化对象

　　在使用默认的无参构造方法创建Bean的实例化对象时，方法getInstantiationStrategy().instantiate调用了SimpleInstantiationStrategy类中的实例化Bean的方法，其源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//使用初始化策略实例化Bean对象  
public Object instantiate(RootBeanDefinition beanDefinition, String beanName, BeanFactory owner) {  
   //如果Bean定义中没有方法覆盖，则就不需要CGLIB父类类的方法  
   if (beanDefinition.getMethodOverrides().isEmpty()) {  
       Constructor<?> constructorToUse;  
       synchronized (beanDefinition.constructorArgumentLock) {  
           //获取对象的构造方法或工厂方法  
           constructorToUse = (Constructor<?>) beanDefinition.resolvedConstructorOrFactoryMethod;  
           //如果没有构造方法且没有工厂方法  
           if (constructorToUse == null) {  
               //使用JDK的反射机制，判断要实例化的Bean是否是接口  
               final Class clazz = beanDefinition.getBeanClass();  
               if (clazz.isInterface()) {  
                   throw new BeanInstantiationException(clazz, "Specified class is an interface");  
               }  
               try {  
                   if (System.getSecurityManager() != null) {  
                   //这里是一个匿名内置类，使用反射机制获取Bean的构造方法  
                       constructorToUse = AccessController.doPrivileged(new PrivilegedExceptionAction<Constructor>() {  
                           public Constructor run() throws Exception {  
                               return clazz.getDeclaredConstructor((Class[]) null);  
                           }  
                       });  
                   }  
                   else {  
                       constructorToUse =  clazz.getDeclaredConstructor((Class[]) null);  
                   }  
                   beanDefinition.resolvedConstructorOrFactoryMethod = constructorToUse;  
               }  
               catch (Exception ex) {  
                   throw new BeanInstantiationException(clazz, "No default constructor found", ex);  
               }  
           }  
       }  
       //使用BeanUtils实例化，通过反射机制调用”构造方法.newInstance(arg)”来进行实例化  
       return BeanUtils.instantiateClass(constructorToUse);  
   }  
   else {  
       //使用CGLIB来实例化对象  
       return instantiateWithMethodInjection(beanDefinition, beanName, owner);  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过上面的代码分析，我们看到了如果Bean有方法被覆盖了，则使用JDK的反射机制进行实例化，否则，使用CGLIB进行实例化。instantiateWithMethodInjection方法调用SimpleInstantiationStrategy的子类CglibSubclassingInstantiationStrategy使用CGLIB来进行初始化，其源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//使用CGLIB进行Bean对象实例化  
public Object instantiate(Constructor ctor, Object[] args) {  
       //CGLIB中的类  
       Enhancer enhancer = new Enhancer();  
       //将Bean本身作为其基类  
       enhancer.setSuperclass(this.beanDefinition.getBeanClass());  
       enhancer.setCallbackFilter(new CallbackFilterImpl());  
       enhancer.setCallbacks(new Callback[] {  
               NoOp.INSTANCE,  
               new LookupOverrideMethodInterceptor(),  
               new ReplaceOverrideMethodInterceptor()  
       });  
       //使用CGLIB的create方法生成实例对象  
       return (ctor == null) ?   
               enhancer.create() :   
               enhancer.create(ctor.getParameterTypes(), args);  
   }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　CGLIB是一个常用的字节码生成器的类库，它提供了一系列API实现java字节码的生成和转换功能。我们在学习JDK的动态代理时都知道，JDK的动态代理只能针对接口，如果一个类没有实现任何接口，要对其进行动态代理只能使用CGLIB。

### 　　6、populateBean方法对Bean属性的依赖注入

　　在第3步的分析中我们已经了解到Bean的依赖注入分为以下两个过程：

- createBeanInstance：生成Bean所包含的java对象实例。
- populateBean ：对Bean属性的依赖注入进行处理。

　　第4、5步中我们已经分析了容器初始化生成Bean所包含的Java实例对象的过程，现在我们继续分析生成对象后，Spring IOC容器是如何将Bean的属性依赖关系注入Bean实例对象中并设置好的，属性依赖注入的代码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//将Bean属性设置到生成的实例对象上  
protected void populateBean(String beanName, AbstractBeanDefinition mbd, BeanWrapper bw) {  
   //获取容器在解析Bean定义资源时为BeanDefiniton中设置的属性值  
   PropertyValues pvs = mbd.getPropertyValues();  
   //实例对象为null  
   if (bw == null) {  
       //属性值不为空  
       if (!pvs.isEmpty()) {  
           throw new BeanCreationException(  
                   mbd.getResourceDescription(), beanName, "Cannot apply property values to null instance");  
       }  
       else {  
           //实例对象为null，属性值也为空，不需要设置属性值，直接返回  
           return;  
       }  
   }  
   //在设置属性之前调用Bean的PostProcessor后置处理器  
   boolean continueWithPropertyPopulation = true;  
   if (!mbd.isSynthetic() && hasInstantiationAwareBeanPostProcessors()) {  
       for (BeanPostProcessor bp : getBeanPostProcessors()) {  
           if (bp instanceof InstantiationAwareBeanPostProcessor) {  
               InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;  
               if (!ibp.postProcessAfterInstantiation(bw.getWrappedInstance(), beanName)) {  
                   continueWithPropertyPopulation = false;  
                   break;  
               }  
           }  
       }  
   }  
   if (!continueWithPropertyPopulation) {  
       return;  
   }  
   //依赖注入开始，首先处理autowire自动装配的注入  
   if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_NAME ||  
           mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_TYPE) {  
       MutablePropertyValues newPvs = new MutablePropertyValues(pvs);  
       //对autowire自动装配的处理，根据Bean名称自动装配注入  
       if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_NAME) {  
           autowireByName(beanName, mbd, bw, newPvs);  
       }  
       //根据Bean类型自动装配注入  
       if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_TYPE) {  
           autowireByType(beanName, mbd, bw, newPvs);  
       }  
       pvs = newPvs;  
   }  
   //检查容器是否持有用于处理单态模式Bean关闭时的后置处理器  
   boolean hasInstAwareBpps = hasInstantiationAwareBeanPostProcessors();  
   //Bean实例对象没有依赖，即没有继承基类  
   boolean needsDepCheck = (mbd.getDependencyCheck() != RootBeanDefinition.DEPENDENCY_CHECK_NONE);  
   if (hasInstAwareBpps || needsDepCheck) {  
       //从实例对象中提取属性描述符  
       PropertyDescriptor[] filteredPds = filterPropertyDescriptorsForDependencyCheck(bw);  
       if (hasInstAwareBpps) {  
           for (BeanPostProcessor bp : getBeanPostProcessors()) {  
               if (bp instanceof InstantiationAwareBeanPostProcessor) {  
                   InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;  
                   //使用BeanPostProcessor处理器处理属性值  
                   pvs = ibp.postProcessPropertyValues(pvs, filteredPds, bw.getWrappedInstance(), beanName);  
                   if (pvs == null) {  
                       return;  
                   }  
               }  
           }  
       }  
       if (needsDepCheck) {  
           //为要设置的属性进行依赖检查  
           checkDependencies(beanName, mbd, filteredPds, pvs);  
       }  
   }  
   //对属性进行注入  
   applyPropertyValues(beanName, mbd, bw, pvs);  
}  
//解析并注入依赖属性的过程  
protected void applyPropertyValues(String beanName, BeanDefinition mbd, BeanWrapper bw, PropertyValues pvs) {  
   if (pvs == null || pvs.isEmpty()) {  
       return;  
   }  
   //封装属性值  
   MutablePropertyValues mpvs = null;  
   List<PropertyValue> original;  
   if (System.getSecurityManager()!= null) {  
       if (bw instanceof BeanWrapperImpl) {  
           //设置安全上下文，JDK安全机制  
           ((BeanWrapperImpl) bw).setSecurityContext(getAccessControlContext());  
       }  
   }  
   if (pvs instanceof MutablePropertyValues) {  
       mpvs = (MutablePropertyValues) pvs;  
       //属性值已经转换  
       if (mpvs.isConverted()) {  
           try {  
               //为实例化对象设置属性值  
               bw.setPropertyValues(mpvs);  
               return;  
           }  
           catch (BeansException ex) {  
               throw new BeanCreationException(  
                       mbd.getResourceDescription(), beanName, "Error setting property values", ex);  
           }  
       }  
       //获取属性值对象的原始类型值  
       original = mpvs.getPropertyValueList();  
   }  
   else {  
       original = Arrays.asList(pvs.getPropertyValues());  
   }  
   //获取用户自定义的类型转换  
   TypeConverter converter = getCustomTypeConverter();  
   if (converter == null) {  
       converter = bw;  
   }  
   //创建一个Bean定义属性值解析器，将Bean定义中的属性值解析为Bean实例对象  
   //的实际值  
   BeanDefinitionValueResolver valueResolver = new BeanDefinitionValueResolver(this, beanName, mbd, converter);  
   //为属性的解析值创建一个拷贝，将拷贝的数据注入到实例对象中  
   List<PropertyValue> deepCopy = new ArrayList<PropertyValue>(original.size());  
   boolean resolveNecessary = false;  
   for (PropertyValue pv : original) {  
       //属性值不需要转换  
       if (pv.isConverted()) {  
           deepCopy.add(pv);  
       }  
       //属性值需要转换  
       else {  
           String propertyName = pv.getName();  
           //原始的属性值，即转换之前的属性值  
           Object originalValue = pv.getValue();  
           //转换属性值，例如将引用转换为IoC容器中实例化对象引用  
           Object resolvedValue = valueResolver.resolveValueIfNecessary(pv, originalValue);  
           //转换之后的属性值  
           Object convertedValue = resolvedValue;  
           //属性值是否可以转换  
           boolean convertible = bw.isWritableProperty(propertyName) &&  
                   !PropertyAccessorUtils.isNestedOrIndexedProperty(propertyName);  
           if (convertible) {  
               //使用用户自定义的类型转换器转换属性值  
               convertedValue = convertForProperty(resolvedValue, propertyName, bw, converter);  
           }  
           //存储转换后的属性值，避免每次属性注入时的转换工作  
           if (resolvedValue == originalValue) {  
               if (convertible) {  
                   //设置属性转换之后的值  
                   pv.setConvertedValue(convertedValue);  
               }  
               deepCopy.add(pv);  
           }  
           //属性是可转换的，且属性原始值是字符串类型，且属性的原始类型值不是  
           //动态生成的字符串，且属性的原始值不是集合或者数组类型  
           else if (convertible && originalValue instanceof TypedStringValue &&  
                   !((TypedStringValue) originalValue).isDynamic() &&  
                   !(convertedValue instanceof Collection || ObjectUtils.isArray(convertedValue))) {  
               pv.setConvertedValue(convertedValue);  
               deepCopy.add(pv);  
           }  
           else {  
               resolveNecessary = true;  
               //重新封装属性的值  
               deepCopy.add(new PropertyValue(pv, convertedValue));  
           }  
       }  
   }  
   if (mpvs != null && !resolveNecessary) {  
       //标记属性值已经转换过  
       mpvs.setConverted();  
   }  
   //进行属性依赖注入  
   try {  
       bw.setPropertyValues(new MutablePropertyValues(deepCopy));  
   }  
   catch (BeansException ex) {  
       throw new BeanCreationException(  
               mbd.getResourceDescription(), beanName, "Error setting property values", ex);  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　分析上述代码，我们可以看出，对属性的注入过程分以下两种情况：

- 属性值类型不需要转换时，不需要解析属性值，直接准备进行依赖注入。
- 属性值需要进行类型转换时，如对其他对象的引用等，首先需要解析属性值，然后对解析后的属性值进行依赖注入。

　　对属性值的解析是在BeanDefinitionValueResolver类中的resolveValueIfNecessary方法中进行的，对属性值的依赖注入是通过bw.setPropertyValues方法实现的，在分析属性值的依赖注入之前，我们先分析一下对属性值的解析过程。

### 　　7、BeanDefinitionValueResolver解析属性值

　　当容器在对属性进行依赖注入时，如果发现属性值需要进行类型转换，如属性值是容器中另一个Bean实例对象的引用，则容器首先需要根据属性值解析出所引用的对象，然后才能将该引用对象注入到目标实例对象的属性上去，对属性进行解析的由resolveValueIfNecessary方法实现，其源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//解析属性值，对注入类型进行转换  
public Object resolveValueIfNecessary(Object argName, Object value) {  
   //对引用类型的属性进行解析  
   if (value instanceof RuntimeBeanReference) {  
       RuntimeBeanReference ref = (RuntimeBeanReference) value;  
       //调用引用类型属性的解析方法  
       return resolveReference(argName, ref);  
   }  
   //对属性值是引用容器中另一个Bean名称的解析  
   else if (value instanceof RuntimeBeanNameReference) {  
       String refName = ((RuntimeBeanNameReference) value).getBeanName();  
       refName = String.valueOf(evaluate(refName));  
       //从容器中获取指定名称的Bean  
       if (!this.beanFactory.containsBean(refName)) {  
           throw new BeanDefinitionStoreException(  
                   "Invalid bean name '" + refName + "' in bean reference for " + argName);  
       }  
       return refName;  
   }  
   //对Bean类型属性的解析，主要是Bean中的内部类  
   else if (value instanceof BeanDefinitionHolder) {  
       BeanDefinitionHolder bdHolder = (BeanDefinitionHolder) value;  
       return resolveInnerBean(argName, bdHolder.getBeanName(), bdHolder.getBeanDefinition());  
   }  
   else if (value instanceof BeanDefinition) {  
       BeanDefinition bd = (BeanDefinition) value;  
       return resolveInnerBean(argName, "(inner bean)", bd);  
   }  
   //对集合数组类型的属性解析  
   else if (value instanceof ManagedArray) {  
       ManagedArray array = (ManagedArray) value;  
       //获取数组的类型  
       Class elementType = array.resolvedElementType;  
       if (elementType == null) {  
           //获取数组元素的类型  
           String elementTypeName = array.getElementTypeName();  
           if (StringUtils.hasText(elementTypeName)) {  
               try {  
                   //使用反射机制创建指定类型的对象  
                   elementType = ClassUtils.forName(elementTypeName, this.beanFactory.getBeanClassLoader());  
                   array.resolvedElementType = elementType;  
               }  
               catch (Throwable ex) {  
                   throw new BeanCreationException(  
                           this.beanDefinition.getResourceDescription(), this.beanName,  
                           "Error resolving array type for " + argName, ex);  
               }  
           }  
           //没有获取到数组的类型，也没有获取到数组元素的类型，则直接设置数  
           //组的类型为Object  
           else {  
               elementType = Object.class;  
           }  
       }  
       //创建指定类型的数组  
       return resolveManagedArray(argName, (List<?>) value, elementType);  
   }  
   //解析list类型的属性值  
   else if (value instanceof ManagedList) {  
       return resolveManagedList(argName, (List<?>) value);  
   }  
   //解析set类型的属性值  
   else if (value instanceof ManagedSet) {  
       return resolveManagedSet(argName, (Set<?>) value);  
   }  
   //解析map类型的属性值  
   else if (value instanceof ManagedMap) {  
       return resolveManagedMap(argName, (Map<?, ?>) value);  
   }  
   //解析props类型的属性值，props其实就是key和value均为字符串的map  
   else if (value instanceof ManagedProperties) {  
       Properties original = (Properties) value;  
       //创建一个拷贝，用于作为解析后的返回值  
       Properties copy = new Properties();  
       for (Map.Entry propEntry : original.entrySet()) {  
           Object propKey = propEntry.getKey();  
           Object propValue = propEntry.getValue();  
           if (propKey instanceof TypedStringValue) {  
               propKey = evaluate((TypedStringValue) propKey);  
           }  
           if (propValue instanceof TypedStringValue) {  
               propValue = evaluate((TypedStringValue) propValue);  
           }  
           copy.put(propKey, propValue);  
       }  
       return copy;  
   }  
   //解析字符串类型的属性值  
   else if (value instanceof TypedStringValue) {  
       TypedStringValue typedStringValue = (TypedStringValue) value;  
       Object valueObject = evaluate(typedStringValue);  
       try {  
           //获取属性的目标类型  
           Class<?> resolvedTargetType = resolveTargetType(typedStringValue);  
           if (resolvedTargetType != null) {  
               //对目标类型的属性进行解析，递归调用  
               return this.typeConverter.convertIfNecessary(valueObject, resolvedTargetType);  
           }  
           //没有获取到属性的目标对象，则按Object类型返回  
           else {  
               return valueObject;  
           }  
       }  
       catch (Throwable ex) {  
           throw new BeanCreationException(  
                   this.beanDefinition.getResourceDescription(), this.beanName,  
                   "Error converting typed String value for " + argName, ex);  
       }  
   }  
   else {  
       return evaluate(value);  
   }  
}  
//解析引用类型的属性值  
private Object resolveReference(Object argName, RuntimeBeanReference ref) {  
   try {  
       //获取引用的Bean名称  
       String refName = ref.getBeanName();  
       refName = String.valueOf(evaluate(refName));  
       //如果引用的对象在父类容器中，则从父类容器中获取指定的引用对象  
       if (ref.isToParent()) {  
           if (this.beanFactory.getParentBeanFactory() == null) {  
               throw new BeanCreationException(  
                       this.beanDefinition.getResourceDescription(), this.beanName,  
                       "Can't resolve reference to bean '" + refName +  
                       "' in parent factory: no parent factory available");  
           }  
           return this.beanFactory.getParentBeanFactory().getBean(refName);  
       }  
       //从当前的容器中获取指定的引用Bean对象，如果指定的Bean没有被实例化，则会递归触发引用Bean的初始化和依赖注入  
       else {  
           Object bean = this.beanFactory.getBean(refName);  
           //将当前实例化对象的依赖引用对象  
           this.beanFactory.registerDependentBean(refName, this.beanName);  
           return bean;  
       }  
   }  
   catch (BeansException ex) {  
       throw new BeanCreationException(  
               this.beanDefinition.getResourceDescription(), this.beanName,  
               "Cannot resolve reference to bean '" + ref.getBeanName() + "' while setting " + argName, ex);  
   }  
}   
//解析array类型的属性  
private Object resolveManagedArray(Object argName, List<?> ml, Class elementType) {  
   //创建一个指定类型的数组，用于存放和返回解析后的数组  
   Object resolved = Array.newInstance(elementType, ml.size());  
   for (int i = 0; i < ml.size(); i++) {  
   //递归解析array的每一个元素，并将解析后的值设置到resolved数组中，索引为i  
       Array.set(resolved, i,  
           resolveValueIfNecessary(new KeyedArgName(argName, i), ml.get(i)));  
   }  
   return resolved;  
}  
//解析list类型的属性  
private List resolveManagedList(Object argName, List<?> ml) {  
   List<Object> resolved = new ArrayList<Object>(ml.size());  
   for (int i = 0; i < ml.size(); i++) {  
       //递归解析list的每一个元素  
       resolved.add(  
           resolveValueIfNecessary(new KeyedArgName(argName, i), ml.get(i)));  
   }  
   return resolved;  
}  
//解析set类型的属性  
private Set resolveManagedSet(Object argName, Set<?> ms) {  
   Set<Object> resolved = new LinkedHashSet<Object>(ms.size());  
   int i = 0;  
   //递归解析set的每一个元素  
   for (Object m : ms) {  
       resolved.add(resolveValueIfNecessary(new KeyedArgName(argName, i), m));  
       i++;  
   }  
   return resolved;  
}  
//解析map类型的属性  
private Map resolveManagedMap(Object argName, Map<?, ?> mm) {  
   Map<Object, Object> resolved = new LinkedHashMap<Object, Object>(mm.size());  
   //递归解析map中每一个元素的key和value  
   for (Map.Entry entry : mm.entrySet()) {  
       Object resolvedKey = resolveValueIfNecessary(argName, entry.getKey());  
       Object resolvedValue = resolveValueIfNecessary(  
               new KeyedArgName(argName, entry.getKey()), entry.getValue());  
       resolved.put(resolvedKey, resolvedValue);  
   }  
   return resolved;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过上面的代码分析，我们明白了Spring是如何将引用类型，内部类以及集合类型等属性进行解析的，属性值解析完成后就可以进行依赖注入了，依赖注入的过程就是Bean对象实例设置到它所依赖的Bean对象属性上去，在第7步中我们已经说过，依赖注入是通过bw.setPropertyValues方法实现的，该方法也使用了委托模式，在BeanWrapper接口中至少定义了方法声明，依赖注入的具体实现交由其实现类BeanWrapperImpl来完成，下面我们就分析依BeanWrapperImpl中赖注入相关的源码。

### 　　8、BeanWrapperImpl对Bean属性的依赖注入

　　BeanWrapperImpl类主要是对容器中完成初始化的Bean实例对象进行属性的依赖注入，即把Bean对象设置到它所依赖的另一个Bean的属性中去，依赖注入的相关源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//实现属性依赖注入功能  
private void setPropertyValue(PropertyTokenHolder tokens, PropertyValue pv) throws BeansException {  
   //PropertyTokenHolder主要保存属性的名称、路径，以及集合的size等信息  
   String propertyName = tokens.canonicalName;  
   String actualName = tokens.actualName;  
   //keys是用来保存集合类型属性的size  
   if (tokens.keys != null) {  
       //将属性信息拷贝  
       PropertyTokenHolder getterTokens = new PropertyTokenHolder();  
       getterTokens.canonicalName = tokens.canonicalName;  
       getterTokens.actualName = tokens.actualName;  
       getterTokens.keys = new String[tokens.keys.length - 1];  
       System.arraycopy(tokens.keys, 0, getterTokens.keys, 0, tokens.keys.length - 1);  
       Object propValue;  
       try {  
           //获取属性值，该方法内部使用JDK的内省( Introspector)机制，调用属性//的getter(readerMethod)方法，获取属性的值  
           propValue = getPropertyValue(getterTokens);  
       }  
       catch (NotReadablePropertyException ex) {  
           throw new NotWritablePropertyException(getRootClass(), this.nestedPath + propertyName,  
                   "Cannot access indexed value in property referenced " +  
                   "in indexed property path '" + propertyName + "'", ex);  
       }  
       //获取集合类型属性的长度  
       String key = tokens.keys[tokens.keys.length - 1];  
       if (propValue == null) {  
           throw new NullValueInNestedPathException(getRootClass(), this.nestedPath + propertyName,  
                   "Cannot access indexed value in property referenced " +  
                   "in indexed property path '" + propertyName + "': returned null");  
       }  
       //注入array类型的属性值  
       else if (propValue.getClass().isArray()) {  
           //获取属性的描述符  
           PropertyDescriptor pd = getCachedIntrospectionResults().getPropertyDescriptor(actualName);  
           //获取数组的类型  
           Class requiredType = propValue.getClass().getComponentType();  
           //获取数组的长度  
           int arrayIndex = Integer.parseInt(key);  
           Object oldValue = null;  
           try {  
               //获取数组以前初始化的值  
               if (isExtractOldValueForEditor()) {  
                   oldValue = Array.get(propValue, arrayIndex);  
               }  
               //将属性的值赋值给数组中的元素  
               Object convertedValue = convertIfNecessary(propertyName, oldValue, pv.getValue(), requiredType,  
                       new PropertyTypeDescriptor(pd, new MethodParameter(pd.getReadMethod(), -1), requiredType));  
               Array.set(propValue, arrayIndex, convertedValue);  
           }  
           catch (IndexOutOfBoundsException ex) {  
               throw new InvalidPropertyException(getRootClass(), this.nestedPath + propertyName,  
                       "Invalid array index in property path '" + propertyName + "'", ex);  
           }  
       }  
       //注入list类型的属性值  
       else if (propValue instanceof List) {  
           PropertyDescriptor pd = getCachedIntrospectionResults().getPropertyDescriptor(actualName);  
           //获取list集合的类型  
           Class requiredType = GenericCollectionTypeResolver.getCollectionReturnType(  
                   pd.getReadMethod(), tokens.keys.length);  
           List list = (List) propValue;  
           //获取list集合的size  
           int index = Integer.parseInt(key);  
           Object oldValue = null;  
           if (isExtractOldValueForEditor() && index < list.size()) {  
               oldValue = list.get(index);  
           }  
           //获取list解析后的属性值  
           Object convertedValue = convertIfNecessary(propertyName, oldValue, pv.getValue(), requiredType,  
                   new PropertyTypeDescriptor(pd, new MethodParameter(pd.getReadMethod(), -1), requiredType));  
           if (index < list.size()) {  
               //为list属性赋值  
               list.set(index, convertedValue);  
           }  
           //如果list的长度大于属性值的长度，则多余的元素赋值为null  
           else if (index >= list.size()) {  
               for (int i = list.size(); i < index; i++) {  
                   try {  
                       list.add(null);  
                   }  
                   catch (NullPointerException ex) {  
                       throw new InvalidPropertyException(getRootClass(), this.nestedPath + propertyName,  
                               "Cannot set element with index " + index + " in List of size " +  
                               list.size() + ", accessed using property path '" + propertyName +  
                               "': List does not support filling up gaps with null elements");  
                   }  
               }  
               list.add(convertedValue);  
           }  
       }  
       //注入map类型的属性值  
       else if (propValue instanceof Map) {  
           PropertyDescriptor pd = getCachedIntrospectionResults().getPropertyDescriptor(actualName);  
           //获取map集合key的类型  
           Class mapKeyType = GenericCollectionTypeResolver.getMapKeyReturnType(  
                   pd.getReadMethod(), tokens.keys.length);  
           //获取map集合value的类型  
           Class mapValueType = GenericCollectionTypeResolver.getMapValueReturnType(  
                   pd.getReadMethod(), tokens.keys.length);  
           Map map = (Map) propValue;  
           //解析map类型属性key值  
           Object convertedMapKey = convertIfNecessary(null, null, key, mapKeyType,  
                   new PropertyTypeDescriptor(pd, new MethodParameter(pd.getReadMethod(), -1), mapKeyType));  
           Object oldValue = null;  
           if (isExtractOldValueForEditor()) {  
               oldValue = map.get(convertedMapKey);  
           }  
           //解析map类型属性value值  
           Object convertedMapValue = convertIfNecessary(  
                   propertyName, oldValue, pv.getValue(), mapValueType,  
                   new TypeDescriptor(new MethodParameter(pd.getReadMethod(), -1, tokens.keys.length + 1)));  
           //将解析后的key和value值赋值给map集合属性  
           map.put(convertedMapKey, convertedMapValue);  
       }  
       else {  
           throw new InvalidPropertyException(getRootClass(), this.nestedPath + propertyName,  
                   "Property referenced in indexed property path '" + propertyName +  
                   "' is neither an array nor a List nor a Map; returned value was [" + pv.getValue() + "]");  
       }  
   }  
   //对非集合类型的属性注入  
   else {  
       PropertyDescriptor pd = pv.resolvedDescriptor;  
       if (pd == null || !pd.getWriteMethod().getDeclaringClass().isInstance(this.object)) {  
           pd = getCachedIntrospectionResults().getPropertyDescriptor(actualName);  
           //无法获取到属性名或者属性没有提供setter(写方法)方法  
           if (pd == null || pd.getWriteMethod() == null) {  
               //如果属性值是可选的，即不是必须的，则忽略该属性值  
               if (pv.isOptional()) {  
                   logger.debug("Ignoring optional value for property '" + actualName +  
                           "' - property not found on bean class [" + getRootClass().getName() + "]");  
                   return;  
               }  
               //如果属性值是必须的，则抛出无法给属性赋值，因为每天提供setter方法异常  
               else {  
                   PropertyMatches matches = PropertyMatches.forProperty(propertyName, getRootClass());  
                   throw new NotWritablePropertyException(  
                           getRootClass(), this.nestedPath + propertyName,  
                           matches.buildErrorMessage(), matches.getPossibleMatches());  
               }  
           }  
           pv.getOriginalPropertyValue().resolvedDescriptor = pd;  
       }  
       Object oldValue = null;  
       try {  
           Object originalValue = pv.getValue();  
           Object valueToApply = originalValue;  
           if (!Boolean.FALSE.equals(pv.conversionNecessary)) {  
               if (pv.isConverted()) {  
                   valueToApply = pv.getConvertedValue();  
               }  
               else {  
                   if (isExtractOldValueForEditor() && pd.getReadMethod() != null) {  
                       //获取属性的getter方法(读方法)，JDK内省机制  
                       final Method readMethod = pd.getReadMethod();  
                       //如果属性的getter方法不是public访问控制权限的，即访问控制权限比较严格，  
                       //则使用JDK的反射机制强行访问非public的方法(暴力读取属性值)  
                       if (!Modifier.isPublic(readMethod.getDeclaringClass().getModifiers()) &&  
                               !readMethod.isAccessible()) {  
                           if (System.getSecurityManager()!= null) {  
                               //匿名内部类，根据权限修改属性的读取控制限制  
                               AccessController.doPrivileged(new PrivilegedAction<Object>() {  
                                   public Object run() {  
                                       readMethod.setAccessible(true);  
                                       return null;  
                                   }  
                               });  
                           }  
                           else {  
                               readMethod.setAccessible(true);  
                           }  
                       }  
                       try {  
                           //属性没有提供getter方法时，调用潜在的读取属性值//的方法，获取属性值  
                           if (System.getSecurityManager() != null) {  
                               oldValue = AccessController.doPrivileged(new PrivilegedExceptionAction<Object>() {  
                                   public Object run() throws Exception {  
                                       return readMethod.invoke(object);  
                                   }  
                               }, acc);  
                           }  
                           else {  
                               oldValue = readMethod.invoke(object);  
                           }  
                       }  
                       catch (Exception ex) {  
                           if (ex instanceof PrivilegedActionException) {  
                               ex = ((PrivilegedActionException) ex).getException();  
                           }  
                           if (logger.isDebugEnabled()) {  
                               logger.debug("Could not read previous value of property '" +  
                                       this.nestedPath + propertyName + "'", ex);  
                           }  
                       }  
                   }  
                   //设置属性的注入值  
                   valueToApply = convertForProperty(propertyName, oldValue, originalValue, pd);  
               }  
               pv.getOriginalPropertyValue().conversionNecessary = (valueToApply != originalValue);  
           }  
           //根据JDK的内省机制，获取属性的setter(写方法)方法  
           final Method writeMethod = (pd instanceof GenericTypeAwarePropertyDescriptor ?  
                   ((GenericTypeAwarePropertyDescriptor) pd).getWriteMethodForActualAccess() :  
                   pd.getWriteMethod());  
           //如果属性的setter方法是非public，即访问控制权限比较严格，则使用JDK的反射机制，  
           //强行设置setter方法可访问(暴力为属性赋值)  
           if (!Modifier.isPublic(writeMethod.getDeclaringClass().getModifiers()) && !writeMethod.isAccessible()) {  
               //如果使用了JDK的安全机制，则需要权限验证  
               if (System.getSecurityManager()!= null) {  
                   AccessController.doPrivileged(new PrivilegedAction<Object>() {  
                       public Object run() {  
                           writeMethod.setAccessible(true);  
                           return null;  
                       }  
                   });  
               }  
               else {  
                   writeMethod.setAccessible(true);  
               }  
           }  
           final Object value = valueToApply;  
           if (System.getSecurityManager() != null) {  
               try {  
                   //将属性值设置到属性上去  
                   AccessController.doPrivileged(new PrivilegedExceptionAction<Object>() {  
                       public Object run() throws Exception {  
                           writeMethod.invoke(object, value);  
                           return null;  
                       }  
                   }, acc);  
               }  
               catch (PrivilegedActionException ex) {  
                   throw ex.getException();  
               }  
           }  
           else {  
               writeMethod.invoke(this.object, value);  
           }  
       }  
       catch (TypeMismatchException ex) {  
           throw ex;  
       }  
       catch (InvocationTargetException ex) {  
           PropertyChangeEvent propertyChangeEvent =  
                   new PropertyChangeEvent(this.rootObject, this.nestedPath + propertyName, oldValue, pv.getValue());  
           if (ex.getTargetException() instanceof ClassCastException) {  
               throw new TypeMismatchException(propertyChangeEvent, pd.getPropertyType(), ex.getTargetException());  
           }  
           else {  
               throw new MethodInvocationException(propertyChangeEvent, ex.getTargetException());  
           }  
       }  
       catch (Exception ex) {  
           PropertyChangeEvent pce =  
                   new PropertyChangeEvent(this.rootObject, this.nestedPath + propertyName, oldValue, pv.getValue());  
           throw new MethodInvocationException(pce, ex);  
       }  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过对上面注入依赖代码的分析，我们已经明白了Spring IOC容器是如何将属性的值注入到Bean实例对象中去的：

- 对于集合类型的属性，将其属性值解析为目标类型的集合后直接赋值给属性。
- 对于非集合类型的属性，大量使用了JDK的反射和内省机制，通过属性的getter方法(reader method)获取指定属性注入以前的值，同时调用属性的setter方法(writer method)为属性设置注入后的值。看到这里相信很多人都明白了Spring的setter注入原理。

　　至此Spring IOC容器对Bean定义资源文件的定位，载入、解析和依赖注入已经全部分析完毕，现在Spring IOC容器中管理了一系列靠依赖关系联系起来的Bean，程序不需要应用自己手动创建所需的对象，Spring IOC容器会在我们使用的时候自动为我们创建，并且为我们注入好相关的依赖，这就是Spring核心功能的控制反转和依赖注入的相关功能。

## 六、IOC容器高级特性

### 　　1、介绍

   通过前面对Spring IoC容器的源码分析，我们已经基本上了解了Spring IOC容器对Bean定义资源的定位、读入和解析过程，同时也清楚了当用户通过getBean方法向IOC容器获取被管理的Bean时，IOC容器对Bean进行的初始化和依赖注入过程，这些是Spring IOC容器的基本功能特性。Spring IOC容器还有一些高级特性，如使用lazy-init属性对Bean预初始化、FactoryBean产生或者修饰Bean对象的生成、IOC容器初始化Bean过程中使用BeanPostProcessor后置处理器对Bean声明周期事件管理和IOC容器的autowiring自动装配功能等。

### 　　2、Spring IoC容器的lazy-init属性实现预实例化

   通过前面我们对IOC容器的实现和工作原理分析，我们知道IOC容器的初始化过程就是对Bean定义资源的定位、载入和注册，此时容器对Bean的依赖注入并没有发生，依赖注入主要是在应用程序第一次向容器索取Bean时，通过getBean方法的调用完成。

　　当Bean定义资源的<Bean>元素中配置了lazy-init属性时，容器将会在初始化的时候对所配置的Bean进行预实例化，Bean的依赖注入在容器初始化的时候就已经完成。这样，当应用程序第一次向容器索取被管理的Bean时，就不用再初始化和对Bean进行依赖注入了，直接从容器中获取已经完成依赖注入的现成Bean，可以提高应用第一次向容器获取Bean的性能。

　　下面我们通过代码分析容器预实例化的实现过程：

#### 　　(1). refresh()

　　先从IOC容器的初始化过程开始，通过前面文章分析，我们知道IOC容器读入已经定位的Bean定义资源是从refresh方法开始的，我们首先从AbstractApplicationContext类的refresh方法入手分析，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//容器初始化的过程，读入Bean定义资源，并解析注册  
public void refresh() throws BeansException, IllegalStateException {  
   synchronized (this.startupShutdownMonitor) {  
        //调用容器准备刷新的方法，获取容器的当时时间，同时给容器设置同步标识  
        prepareRefresh();  
        //告诉子类启动refreshBeanFactory()方法，Bean定义资源文件的载入从  
        //子类的refreshBeanFactory()方法启动  
        ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();  
        //为BeanFactory配置容器特性，例如类加载器、事件处理器等  
       prepareBeanFactory(beanFactory);  
       try {  
           //为容器的某些子类指定特殊的BeanPost事件处理器  
           postProcessBeanFactory(beanFactory);  
           //调用所有注册的BeanFactoryPostProcessor的Bean  
           invokeBeanFactoryPostProcessors(beanFactory);  
           //为BeanFactory注册BeanPost事件处理器.  
           //BeanPostProcessor是Bean后置处理器，用于监听容器触发的事件  
           registerBeanPostProcessors(beanFactory);  
           //初始化信息源，和国际化相关.  
           initMessageSource();  
           //初始化容器事件传播器.  
           initApplicationEventMulticaster();  
           //调用子类的某些特殊Bean初始化方法  
           onRefresh();  
           //为事件传播器注册事件监听器.  
           registerListeners();  
           //这里是对容器lazy-init属性进行处理的入口方法  
           finishBeanFactoryInitialization(beanFactory);  
           //初始化容器的生命周期事件处理器，并发布容器的生命周期事件  
           finishRefresh();  
       }  
       catch (BeansException ex) {  
           //销毁以创建的单态Bean  
           destroyBeans();  
           //取消refresh操作，重置容器的同步标识.  
           cancelRefresh(ex);  
           throw ex;  
       }  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　在refresh方法中ConfigurableListableBeanFactorybeanFactory = obtainFreshBeanFactory()；启动了Bean定义资源的载入、注册过程，而finishBeanFactoryInitialization方法是对注册后的Bean定义中的预实例化(lazy-init=false，Spring默认就是预实例化，即为true)的Bean进行处理的地方。

#### 　　(2). finishBeanFactoryInitialization处理预实例化Bean

　　当Bean定义资源被载入IOC容器之后，容器将Bean定义资源解析为容器内部的数据结构BeanDefinition注册到容器中，AbstractApplicationContext类中的finishBeanFactoryInitialization方法对配置了预实例化属性的Bean进行预初始化过程，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//对配置了lazy-init属性的Bean进行预实例化处理  
protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {  
   //这是Spring3以后新加的代码，为容器指定一个转换服务(ConversionService)  
   //在对某些Bean属性进行转换时使用  
   if (beanFactory.containsBean(CONVERSION_SERVICE_BEAN_NAME) &&  
           beanFactory.isTypeMatch(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class)) {  
       beanFactory.setConversionService(  
               beanFactory.getBean(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class));  
   }  
   //为了类型匹配，停止使用临时的类加载器  
   beanFactory.setTempClassLoader(null);  
   //缓存容器中所有注册的BeanDefinition元数据，以防被修改  
   beanFactory.freezeConfiguration();  
   //对配置了lazy-init属性的单态模式Bean进行预实例化处理  
   beanFactory.preInstantiateSingletons();  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　ConfigurableListableBeanFactory是一个接口，其preInstantiateSingletons方法由其子类DefaultListableBeanFactory提供。 

#### 　　(3)、DefaultListableBeanFactory对配置lazy-init属性单态Bean的预实例化

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//对配置lazy-init属性单态Bean的预实例化  
public void preInstantiateSingletons() throws BeansException {  
   if (this.logger.isInfoEnabled()) {  
       this.logger.info("Pre-instantiating singletons in " + this);  
   }  
   //在对配置lazy-init属性单态Bean的预实例化过程中，必须多线程同步，以确保数据一致性  
   synchronized (this.beanDefinitionMap) {  
       for (String beanName : this.beanDefinitionNames) {  
           //获取指定名称的Bean定义  
           RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);  
           //Bean不是抽象的，是单态模式的，且lazy-init属性配置为false  
           if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit()) {  
               //如果指定名称的bean是创建容器的Bean  
               if (isFactoryBean(beanName)) {  
               //FACTORY_BEAN_PREFIX=”&”，当Bean名称前面加”&”符号  
              //时，获取的是产生容器对象本身，而不是容器产生的Bean.  
              //调用getBean方法，触发容器对Bean实例化和依赖注入过程  
                   final FactoryBean factory = (FactoryBean) getBean(FACTORY_BEAN_PREFIX + beanName);  
                   //标识是否需要预实例化  
                   boolean isEagerInit;  
                   if (System.getSecurityManager() != null && factory instanceof SmartFactoryBean) {  
                       //一个匿名内部类  
                       isEagerInit = AccessController.doPrivileged(new PrivilegedAction<Boolean>() {  
                           public Boolean run() {  
                               return ((SmartFactoryBean) factory).isEagerInit();  
                           }  
                       }, getAccessControlContext());  
                   }  
                   else {  
                       isEagerInit = factory instanceof SmartFactoryBean && ((SmartFactoryBean) factory).isEagerInit();   
                   }  
                   if (isEagerInit) {  
                      //调用getBean方法，触发容器对Bean实例化和依赖注入过程  
                       getBean(beanName);  
                   }  
               }  
               else {  
                    //调用getBean方法，触发容器对Bean实例化和依赖注入过程  
                   getBean(beanName);  
               }  
           }  
       }  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过对lazy-init处理源码的分析，我们可以看出，如果设置了lazy-init属性，则容器在完成Bean定义的注册之后，会通过getBean方法，触发对指定Bean的初始化和依赖注入过程，这样当应用第一次向容器索取所需的Bean时，容器不再需要对Bean进行初始化和依赖注入，直接从已经完成实例化和依赖注入的Bean中取一个线程的Bean，这样就提高了第一次获取Bean的性能。

### 　　3、FactoryBean的实现

　　在Spring中，有两个很容易混淆的类：BeanFactory和FactoryBean。

- BeanFactory：Bean工厂，是一个工厂(Factory)，我们Spring IoC容器的最顶层接口就是这个BeanFactory，它的作用是管理Bean，即实例化、定位、配置应用程序中的对象及建立这些对象间的依赖。
- FactoryBean：工厂Bean，是一个Bean，作用是产生其他bean实例。通常情况下，这种bean没有什么特别的要求，仅需要提供一个工厂方法，该方法用来返回其他bean实例。通常情况下，bean无须自己实现工厂模式，Spring容器担任工厂角色；但少数情况下，容器中的bean本身就是工厂，其作用是产生其它bean实例。

　　**当用户使用容器本身时，可以使用转义字符”&”来得到FactoryBean本身，以区别通过FactoryBean产生的实例对象和FactoryBean对象本身**。在BeanFactory中通过如下代码定义了该转义字符：

```
String FACTORY_BEAN_PREFIX = "&";
```

　　如果myJndiObject是一个FactoryBean，则使用&myJndiObject得到的是myJndiObject对象，而不是myJndiObject产生出来的对象。

#### 　　(1). FactoryBean的源码

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//工厂Bean，用于产生其他对象  
public interface FactoryBean<T> {  
   //获取容器管理的对象实例  
    T getObject() throws Exception;  
    //获取Bean工厂创建的对象的类型  
    Class<?> getObjectType();  
    //Bean工厂创建的对象是否是单态模式，如果是单态模式，则整个容器中只有一个实例  
   //对象，每次请求都返回同一个实例对象  
    boolean isSingleton();  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

#### 　　(2). AbstractBeanFactory的getBean方法调用FactoryBean

　　在前面我们分析Spring IOC 容器实例化Bean并进行依赖注入过程的源码时，提到在getBean方法触发容器实例化Bean的时候会调用AbstractBeanFactory的doGetBean方法来进行实例化的过程，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//真正实现向IoC容器获取Bean的功能，也是触发依赖注入功能的地方    
 @SuppressWarnings("unchecked")    
 protected <T> T doGetBean(    
         final String name, final Class<T> requiredType, final Object[] args, boolean typeCheckOnly)    
         throws BeansException {    
     //根据指定的名称获取被管理Bean的名称，剥离指定名称中对容器的相关依赖，如果指定的是别名，将别名转换为规范的Bean名称    
     final String beanName = transformedBeanName(name);    
     Object bean;    
     //先从缓存中取是否已经有被创建过的单态类型的Bean，对于单态模式的Bean整个IoC容器中只创建一次，不需要重复创建    
     Object sharedInstance = getSingleton(beanName);    
     //IoC容器创建单态模式Bean实例对象    
     if (sharedInstance != null && args == null) {    
         if (logger.isDebugEnabled()) {    
         　　 //如果指定名称的Bean在容器中已有单态模式的Bean被创建，直接返回已经创建的Bean    
             if (isSingletonCurrentlyInCreation(beanName)) {    
                 logger.debug("Returning eagerly cached instance of singleton bean '" + beanName +    
                         "' that is not fully initialized yet - a consequence of a circular reference");    
             }    
             else {    
                 logger.debug("Returning cached instance of singleton bean '" + beanName + "'");    
             }    
         }    
         //获取给定Bean的实例对象，主要是完成FactoryBean的相关处理   
         bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);    
     }    
    ……  
}  
//获取给定Bean的实例对象，主要是完成FactoryBean的相关处理 
protected Object getObjectForBeanInstance(  
       Object beanInstance, String name, String beanName, RootBeanDefinition mbd) {  
   //容器已经得到了Bean实例对象，这个实例对象可能是一个普通的Bean，也可能是一个工厂Bean，如果是一个工厂Bean，则使用它创建一个Bean实例对象
   //如果调用本身就想获得一个容器的引用，则指定返回这个工厂Bean实例对象  
   //如果指定的名称是容器的解引用(dereference，即是对象本身而非内存地址)，且Bean实例也不是创建Bean实例对象的工厂Bean  
   if (BeanFactoryUtils.isFactoryDereference(name) && !(beanInstance instanceof FactoryBean)) {  
       throw new BeanIsNotAFactoryException(transformedBeanName(name), beanInstance.getClass());  
   }  
   //如果Bean实例不是工厂Bean，或者指定名称是容器的解引用，调用者向获取对容器的引用，则直接返回当前的Bean实例  
   if (!(beanInstance instanceof FactoryBean) || BeanFactoryUtils.isFactoryDereference(name)) {  
       return beanInstance;  
   }  
   //处理指定名称不是容器的解引用，或者根据名称获取的Bean实例对象是一个工厂Bean，使用工厂Bean创建一个Bean的实例对象  
   Object object = null;  
   if (mbd == null) {  
       //从Bean工厂缓存中获取给定名称的Bean实例对象  
       object = getCachedObjectForFactoryBean(beanName);  
   }  
   //让Bean工厂生产给定名称的Bean对象实例  
   if (object == null) {  
       FactoryBean factory = (FactoryBean) beanInstance;  
       //如果从Bean工厂生产的Bean是单态模式的，则缓存  
       if (mbd == null && containsBeanDefinition(beanName)) {  
           //从容器中获取指定名称的Bean定义，如果继承基类，则合并基类相关属性  
           mbd = getMergedLocalBeanDefinition(beanName);  
       }  
       //如果从容器得到Bean定义信息，并且Bean定义信息不是虚构的，则让工厂Bean生产Bean实例对象  
       boolean synthetic = (mbd != null && mbd.isSynthetic());  
       //调用FactoryBeanRegistrySupport类的getObjectFromFactoryBean方法，实现工厂Bean生产Bean对象实例的过程  
       object = getObjectFromFactoryBean(factory, beanName, !synthetic);  
   }  
   return object;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　在上面获取给定Bean的实例对象的getObjectForBeanInstance方法中，会调用FactoryBeanRegistrySupport类的getObjectFromFactoryBean方法，该方法实现了Bean工厂生产Bean实例对象。

　　**Dereference(解引用)**：一个在C/C++中应用比较多的术语，在C++中，”*”是解引用符号，而”&”是引用符号，解引用是指变量指向的是所引用对象的本身数据，而不是引用对象的内存地址。

#### 　　(3)、AbstractBeanFactory生产Bean实例对象

　　AbstractBeanFactory类中生产Bean实例对象的主要源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//Bean工厂生产Bean实例对象  
protected Object getObjectFromFactoryBean(FactoryBean factory, String beanName, boolean shouldPostProcess) {  
   //Bean工厂是单态模式，并且Bean工厂缓存中存在指定名称的Bean实例对象  
   if (factory.isSingleton() && containsSingleton(beanName)) {  
       //多线程同步，以防止数据不一致  
       synchronized (getSingletonMutex()) {  
           //直接从Bean工厂缓存中获取指定名称的Bean实例对象  
           Object object = this.factoryBeanObjectCache.get(beanName);  
           //Bean工厂缓存中没有指定名称的实例对象，则生产该实例对象  
           if (object == null) {  
               //调用Bean工厂的getObject方法生产指定Bean的实例对象  
               object = doGetObjectFromFactoryBean(factory, beanName, shouldPostProcess);  
               //将生产的实例对象添加到Bean工厂缓存中  
               this.factoryBeanObjectCache.put(beanName, (object != null ? object : NULL_OBJECT));  
           }  
           return (object != NULL_OBJECT ? object : null);  
       }  
   }  
   //调用Bean工厂的getObject方法生产指定Bean的实例对象  
   else {  
       return doGetObjectFromFactoryBean(factory, beanName, shouldPostProcess);  
   }  
}  
//调用Bean工厂的getObject方法生产指定Bean的实例对象  
private Object doGetObjectFromFactoryBean(  
       final FactoryBean factory, final String beanName, final boolean shouldPostProcess)  
       throws BeanCreationException {  
   Object object;  
   try {  
       if (System.getSecurityManager() != null) {  
           AccessControlContext acc = getAccessControlContext();  
           try {  
               //实现PrivilegedExceptionAction接口的匿名内置类，根据JVM检查权限，然后决定BeanFactory创建实例对象  
               object = AccessController.doPrivileged(new PrivilegedExceptionAction<Object>() {  
                   public Object run() throws Exception {  
                           //调用BeanFactory接口实现类的创建对象方法  
                           return factory.getObject();  
                       }  
                   }, acc);  
           }  
           catch (PrivilegedActionException pae) {  
               throw pae.getException();  
           }  
       }  
       else {  
           //调用BeanFactory接口实现类的创建对象方法  
           object = factory.getObject();  
       }  
   }  
   catch (FactoryBeanNotInitializedException ex) {  
       throw new BeanCurrentlyInCreationException(beanName, ex.toString());  
   }  
   catch (Throwable ex) {  
       throw new BeanCreationException(beanName, "FactoryBean threw exception on object creation", ex);  
   }  
   //创建出来的实例对象为null，或者因为单态对象正在创建而返回null  
   if (object == null && isSingletonCurrentlyInCreation(beanName)) {  
       throw new BeanCurrentlyInCreationException(  
               beanName, "FactoryBean which is currently in creation returned null from getObject");  
   }  
   //为创建出来的Bean实例对象添加BeanPostProcessor后置处理器  
   if (object != null && shouldPostProcess) {  
       try {  
           object = postProcessObjectFromFactoryBean(object, beanName);  
       }  
       catch (Throwable ex) {  
           throw new BeanCreationException(beanName, "Post-processing of the FactoryBean's object failed", ex);  
       }  
   }  
   return object;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　从上面的源码分析中，我们可以看出，**BeanFactory接口调用其实现类的getObject方法来实现创建Bean实例对象的功能**。

#### 　　(4). 工厂Bean的实现类getObject方法创建Bean实例对象

　　FactoryBean的实现类有非常多，比如：Proxy、RMI、JNDI、ServletContextFactoryBean等等，FactoryBean接口为Spring容器提供了一个很好的封装机制，具体的getObject有不同的实现类根据不同的实现策略来具体提供，我们分析一个最简单的AnnotationTestFactoryBean的实现源码：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public class AnnotationTestBeanFactory implements FactoryBean<IJmxTestBean> {  
       private final FactoryCreatedAnnotationTestBean instance = new FactoryCreatedAnnotationTestBean();  
       public AnnotationTestBeanFactory() {  
           this.instance.setName("FACTORY");  
       }  
       //AnnotationTestBeanFactory产生Bean实例对象的实现  
       public IJmxTestBean getObject() throws Exception {  
           return this.instance;  
       }  
       public Class<? extends IJmxTestBean> getObjectType() {  
           return FactoryCreatedAnnotationTestBean.class;  
       }  
       public boolean isSingleton() {  
           return true;  
       }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　其他的Proxy，RMI，JNDI等等，都是根据相应的策略提供getObject的实现。这里不做一一分析，这已经不是Spring的核心功能，有需要的时候再去深入研究。

### 　　4. BeanPostProcessor后置处理器的实现

　　BeanPostProcessor后置处理器是Spring IoC容器经常使用到的一个特性，这个Bean后置处理器是一个监听器，可以监听容器触发的Bean声明周期事件。后置处理器向容器注册以后，容器中管理的Bean就具备了接收IOC容器事件回调的能力。

　　BeanPostProcessor的使用非常简单，只需要提供一个实现接口BeanPostProcessor的实现类，然后在Bean的配置文件中设置即可。

　　(1). BeanPostProcessor源码

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package org.springframework.beans.factory.config;  
import org.springframework.beans.BeansException;  
public interface BeanPostProcessor {  
   //为在Bean的初始化前提供回调入口  
   Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException;  
   //为在Bean的初始化之后提供回调入口  
   Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException;  
 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　这两个回调的入口都是和容器管理的Bean的生命周期事件紧密相关，可以为用户提供在Spring IOC容器初始化Bean过程中自定义的处理操作。

　　(2). AbstractAutowireCapableBeanFactory类对容器生成的Bean添加后置处理器

　　**BeanPostProcessor后置处理器的调用发生在Spring IOC容器完成对Bean实例对象的创建和属性的依赖注入完成之后**，在对Spring依赖注入的源码分析过程中我们知道，当应用程序第一次调用getBean方法(lazy-init预实例化除外)向Spring IoC容器索取指定Bean时触发Spring IoC容器创建Bean实例对象并进行依赖注入的过程，其中真正实现创建Bean对象并进行依赖注入的方法是AbstractAutowireCapableBeanFactory类的doCreateBean方法，主要源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//真正创建Bean的方法  
protected Object doCreateBean(final String beanName, final RootBeanDefinition mbd, final Object[] args) {  
   //创建Bean实例对象  
   ……  
   try {  
       //对Bean属性进行依赖注入  
       populateBean(beanName, mbd, instanceWrapper);  
       if (exposedObject != null) {  
          //在对Bean实例对象生成和依赖注入完成以后，开始对Bean实例对象/进行初始化 ，为Bean实例对象应用BeanPostProcessor后置处理器  
          exposedObject = initializeBean(beanName, exposedObject, mbd);  
       }  
   }  
   catch (Throwable ex) {  
       if (ex instanceof BeanCreationException && beanName.equals(((BeanCreationException) ex).getBeanName())) {  
           throw (BeanCreationException) ex;  
       }  
   ……  
   //为应用返回所需要的实例对象  
   return exposedObject;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　从上面的代码中我们知道，为Bean实例对象添加BeanPostProcessor后置处理器的入口的是initializeBean方法。

　　(3). initializeBean方法为容器产生的Bean实例对象添加BeanPostProcessor后置处理器

　　同样在AbstractAutowireCapableBeanFactory类中，initializeBean方法实现为容器创建的Bean实例对象添加BeanPostProcessor后置处理器，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//初始容器创建的Bean实例对象，为其添加BeanPostProcessor后置处理器  
protected Object initializeBean(final String beanName, final Object bean, RootBeanDefinition mbd) {  
   //JDK的安全机制验证权限  
   if (System.getSecurityManager() != null) {  
       //实现PrivilegedAction接口的匿名内部类  
       AccessController.doPrivileged(new PrivilegedAction<Object>() {  
           public Object run() {  
               invokeAwareMethods(beanName, bean);  
               return null;  
           }  
       }, getAccessControlContext());  
   }  
   else {  
       //为Bean实例对象包装相关属性，如名称，类加载器，所属容器等信息  
       invokeAwareMethods(beanName, bean);  
   }  
   Object wrappedBean = bean;  
   //对BeanPostProcessor后置处理器的postProcessBeforeInitialization回调方法的调用，为Bean实例初始化前做一些处理  
   if (mbd == null || !mbd.isSynthetic()) {  
       wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);  
   }  
   //调用Bean实例对象初始化的方法，这个初始化方法是在Spring Bean定义配置文件中通过init-method属性指定的  
   try {  
       invokeInitMethods(beanName, wrappedBean, mbd);  
   }  
   catch (Throwable ex) {  
       throw new BeanCreationException(  
               (mbd != null ? mbd.getResourceDescription() : null),  
               beanName, "Invocation of init method failed", ex);  
   }  
   //对BeanPostProcessor后置处理器的postProcessAfterInitialization回调方法的调用，为Bean实例初始化之后做一些处理  
   if (mbd == null || !mbd.isSynthetic()) {  
       wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);  
   }  
   return wrappedBean;  
}  
//调用BeanPostProcessor后置处理器实例对象初始化之前的处理方法  
public Object applyBeanPostProcessorsBeforeInitialization(Object existingBean, String beanName)  
       throws BeansException {  
   Object result = existingBean;  
   //遍历容器为所创建的Bean添加的所有BeanPostProcessor后置处理器  
   for (BeanPostProcessor beanProcessor : getBeanPostProcessors()) {  
       //调用Bean实例所有的后置处理中的初始化前处理方法，为Bean实例对象在初始化之前做一些自定义的处理操作  
       result = beanProcessor.postProcessBeforeInitialization(result, beanName);  
       if (result == null) {  
           return result;  
       }  
   }  
   return result;  
}  
//调用BeanPostProcessor后置处理器实例对象初始化之后的处理方法  
public Object applyBeanPostProcessorsAfterInitialization(Object existingBean, String beanName)  
       throws BeansException {  
   Object result = existingBean;  
   //遍历容器为所创建的Bean添加的所有BeanPostProcessor后置处理器  
   for (BeanPostProcessor beanProcessor : getBeanPostProcessors()) {  
       //调用Bean实例所有的后置处理中的初始化后处理方法，为Bean实例对象在初始化之后做一些自定义的处理操作  
       result = beanProcessor.postProcessAfterInitialization(result, beanName);  
       if (result == null) {  
           return result;  
       }  
   }  
   return result;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　BeanPostProcessor是一个接口，其初始化前的操作方法和初始化后的操作方法均委托其实现子类来实现，在Spring中，BeanPostProcessor的实现子类非常的多，分别完成不同的操作，如：AOP面向切面编程的注册通知适配器、Bean对象的数据校验、Bean继承属性/方法的合并等等，我们以最简单的AOP切面织入来简单了解其主要的功能。

#### 　　(4). AdvisorAdapterRegistrationManager在Bean对象初始化后注册通知适配器

　　AdvisorAdapterRegistrationManager是BeanPostProcessor的一个实现类，其主要的作用为容器中管理的Bean注册一个面向切面编程的通知适配器，以便在Spring容器为所管理的Bean进行面向切面编程时提供方便，其源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//为容器中管理的Bean注册一个面向切面编程的通知适配器  
public class AdvisorAdapterRegistrationManager implements BeanPostProcessor {  
   //容器中负责管理切面通知适配器注册的对象  
   private AdvisorAdapterRegistry advisorAdapterRegistry = GlobalAdvisorAdapterRegistry.getInstance();  
   public void setAdvisorAdapterRegistry(AdvisorAdapterRegistry advisorAdapterRegistry) {  
       this.advisorAdapterRegistry = advisorAdapterRegistry;  
   }  
   //BeanPostProcessor在Bean对象初始化前的操作  
   public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {  
       //没有做任何操作，直接返回容器创建的Bean对象  
       return bean;  
   }  
   //BeanPostProcessor在Bean对象初始化后的操作  
   public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {  
       if (bean instanceof AdvisorAdapter){  
           //如果容器创建的Bean实例对象是一个切面通知适配器，则向容器的注册
           this.advisorAdapterRegistry.registerAdvisorAdapter((AdvisorAdapter) bean);  
       }  
       return bean;  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　其他的BeanPostProcessor接口实现类的也类似，都是对Bean对象使用到的一些特性进行处理，或者向IOC容器中注册，为创建的Bean实例对象做一些自定义的功能增加，这些操作是容器初始化Bean时自动触发的，不需要人为的干预。

### 　　5. Spring IOC容器autowiring实现原理

　　Spring IOC容器提供了两种管理Bean依赖关系的方式：

- 显式管理：通过BeanDefinition的属性值和构造方法实现Bean依赖关系管理。
- autowiring：Spring IoC容器的依赖自动装配功能，不需要对Bean属性的依赖关系做显式的声明，只需要在配置好autowiring属性，IOC容器会自动使用反射查找属性的类型和名称，然后基于属性的类型或者名称来自动匹配容器中管理的Bean，从而自动地完成依赖注入。

　　通过对autowiring自动装配特性的理解，我们知道容器对Bean的自动装配发生在容器对Bean依赖注入的过程中。在前面对Spring IOC容器的依赖注入过程源码分析中，我们已经知道了容器对Bean实例对象的属性注入的处理发生在AbstractAutoWireCapableBeanFactory类中的populateBean方法中，我们通过程序流程分析autowiring的实现原理。

#### 　　(1). AbstractAutoWireCapableBeanFactory对Bean实例进行属性依赖注入

　　应用第一次通过getBean方法(配置了lazy-init预实例化属性的除外)向IOC容器索取Bean时，容器创建Bean实例对象，并且对Bean实例对象进行属性依赖注入，AbstractAutoWireCapableBeanFactory的性依赖注入的功能，其主要源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
protected void populateBean(String beanName, AbstractBeanDefinition mbd, BeanWrapper bw) {  
   //获取Bean定义的属性值，并对属性值进行处理  
   PropertyValues pvs = mbd.getPropertyValues();  
   ……  
   //对依赖注入处理，首先处理autowiring自动装配的依赖注入  
   if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_NAME ||  
           mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_TYPE) {  
       MutablePropertyValues newPvs = new MutablePropertyValues(pvs);  
       //根据Bean名称进行autowiring自动装配处理  
       if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_NAME) {  
           autowireByName(beanName, mbd, bw, newPvs);  
       }  
       //根据Bean类型进行autowiring自动装配处理  
       if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_TYPE) {  
           autowireByType(beanName, mbd, bw, newPvs);  
       }  
   }  
   //对非autowiring的属性进行依赖注入处理  
    ……  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

#### 　　(2). Spring IOC容器根据Bean名称或者类型进行autowiring自动依赖注入

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//根据名称对属性进行自动依赖注入  
protected void autowireByName(  
       String beanName, AbstractBeanDefinition mbd, BeanWrapper bw, MutablePropertyValues pvs) {  
    //对Bean对象中非简单属性(不是简单继承的对象，如8中原始类型，字符串，URL等//都是简单属性)进行处理  
   String[] propertyNames = unsatisfiedNonSimpleProperties(mbd, bw);  
   for (String propertyName : propertyNames) {  
       //如果Spring IOC容器中包含指定名称的Bean  
       if (containsBean(propertyName)) {  
           //调用getBean方法向IoC容器索取指定名称的Bean实例，迭代触发属性的//初始化和依赖注入  
           Object bean = getBean(propertyName);  
           //为指定名称的属性赋予属性值  
           pvs.add(propertyName, bean);  
           //指定名称属性注册依赖Bean名称，进行属性依赖注入  
           registerDependentBean(propertyName, beanName);  
           if (logger.isDebugEnabled()) {  
               logger.debug("Added autowiring by name from bean name '" + beanName +  
                       "' via property '" + propertyName + "' to bean named '" + propertyName + "'");  
           }  
       }  
       else {  
           if (logger.isTraceEnabled()) {  
               logger.trace("Not autowiring property '" + propertyName + "' of bean '" + beanName +  
                       "' by name: no matching bean found");  
           }  
       }  
   }  
}  
//根据类型对属性进行自动依赖注入  
protected void autowireByType(  
       String beanName, AbstractBeanDefinition mbd, BeanWrapper bw, MutablePropertyValues pvs) {  
   //获取用户定义的类型转换器  
   TypeConverter converter = getCustomTypeConverter();  
   if (converter == null) {  
       converter = bw;  
   }  
   //存放解析的要注入的属性  
   Set<String> autowiredBeanNames = new LinkedHashSet<String>(4);  
   //对Bean对象中非简单属性(不是简单继承的对象，如8中原始类型、字符、URL等都是简单属性)进行处理  
   String[] propertyNames = unsatisfiedNonSimpleProperties(mbd, bw);  
   for (String propertyName : propertyNames) {  
       try {  
           //获取指定属性名称的属性描述器  
           PropertyDescriptor pd = bw.getPropertyDescriptor(propertyName);  
           //不对Object类型的属性进行autowiring自动依赖注入  
           if (!Object.class.equals(pd.getPropertyType())) {  
               //获取属性的setter方法  
               MethodParameter methodParam = BeanUtils.getWriteMethodParameter(pd);  
               //检查指定类型是否可以被转换为目标对象的类型  
               boolean eager = !PriorityOrdered.class.isAssignableFrom(bw.getWrappedClass());  
               //创建一个要被注入的依赖描述  
               DependencyDescriptor desc = new AutowireByTypeDependencyDescriptor(methodParam, eager);  
               //根据容器的Bean定义解析依赖关系，返回所有要被注入的Bean对象  
               Object autowiredArgument = resolveDependency(desc, beanName, autowiredBeanNames, converter);  
               if (autowiredArgument != null) {  
                   //为属性赋值所引用的对象  
                   pvs.add(propertyName, autowiredArgument);  
               }  
               for (String autowiredBeanName : autowiredBeanNames) {  
                   //指定名称属性注册依赖Bean名称，进行属性依赖注入  
                   registerDependentBean(autowiredBeanName, beanName);  
                   if (logger.isDebugEnabled()) {  
                       logger.debug("Autowiring by type from bean name '" + beanName + "' via property '" +  
                               propertyName + "' to bean named '" + autowiredBeanName + "'");  
                   }  
               }  
               //释放已自动注入的属性  
               autowiredBeanNames.clear();  
           }  
       }  
       catch (BeansException ex) {  
           throw new UnsatisfiedDependencyException(mbd.getResourceDescription(), beanName, propertyName, ex);  
       }  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过上面的源码分析，我们可以看出来通过属性名进行自动依赖注入的相对比通过属性类型进行自动依赖注入要稍微简单一些，但是真正实现属性注入的是DefaultSingletonBeanRegistry类的registerDependentBean方法。

#### 　　(3). DefaultSingletonBeanRegistry的registerDependentBean方法对属性注入

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//为指定的Bean注入依赖的Bean  
public void registerDependentBean(String beanName, String dependentBeanName) {  
   //处理Bean名称，将别名转换为规范的Bean名称  
   String canonicalName = canonicalName(beanName);  
   //多线程同步，保证容器内数据的一致性 。先从容器中：bean名称-->全部依赖Bean名称集合找查找给定名称Bean的依赖Bean  
   synchronized (this.dependentBeanMap) {  
       //获取给定名称Bean的所有依赖Bean名称  
       Set<String> dependentBeans = this.dependentBeanMap.get(canonicalName);  
       if (dependentBeans == null) {  
           //为Bean设置依赖Bean信息  
           dependentBeans = new LinkedHashSet<String>(8);  
           this.dependentBeanMap.put(canonicalName, dependentBeans);  
       }  
       //向容器中：bean名称-->全部依赖Bean名称集合添加Bean的依赖信息，即：将Bean所依赖的Bean添加到容器的集合中  
       dependentBeans.add(dependentBeanName);  
   }  
   //从容器中：bean名称-->指定名称Bean的依赖Bean集合找查找给定名称Bean的依赖Bean  
   synchronized (this.dependenciesForBeanMap) {  
       Set<String> dependenciesForBean = this.dependenciesForBeanMap.get(dependentBeanName);  
       if (dependenciesForBean == null) {  
           dependenciesForBean = new LinkedHashSet<String>(8);  
           this.dependenciesForBeanMap.put(dependentBeanName, dependenciesForBean);  
       }  
       //向容器中：bean名称-->指定Bean的依赖Bean名称集合添加Bean的依赖信息。即：将Bean所依赖的Bean添加到容器的集合中  
       dependenciesForBean.add(canonicalName);  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过对autowiring的源码分析，我们可以看出，autowiring的实现过程：

- a. 对Bean的属性迭代调用getBean方法，完成依赖Bean的初始化和依赖注入。
- b. 将依赖Bean的属性引用设置到被依赖的Bean属性上。
- c. 将依赖Bean的名称和被依赖Bean的名称存储在IOC容器的集合中。

　　Spring IoC容器的autowiring属性自动依赖注入是一个很方便的特性，可以简化开发时的配置，但是凡是都有两面性，自动属性依赖注入也有不足，首先，Bean的依赖关系在配置文件中无法很清楚地看出来，对于维护造成一定困难。其次，由于自动依赖注入是Spring容器自动执行的，容器是不会智能判断的，如果配置不当，将会带来无法预料的后果，所以自动依赖注入特性在使用时还是综合考虑。

### 　　6、多容器/父子容器概念

　　Spring 框架允许在一个应用中创建多个上下文容器。但是建议容器之间有父子关系。可以通过 ConfigurableApplicationContext 接口中定义的 setParent 方法设置父容器。一旦设置父 子关系，则**可以通过子容器获取父容器中除 PropertyPlaceHolder 以外的所有资源**，父容器不能获取子容器中的任意资源(类似 Java 中的类型继承)。

　　典型的父子容器: spring 和 springmvc 同时使用的时候。ContextLoaderListener 创建的容器是父容器，DispatcherServlet 创建的容器是子容器。

　　父子容器的存在是保证一个 JVM 中，只有一个树状结构的容器树,每个容器都有一定的作用范围和访问域，起到一个隔离的作用。可以通过子容器访问父容器资源。

### 　　7、p 域/c 域

　　Spring2.0 之后引入了 p(property 标签)域、Spring3.1 之后引入了 c(constractor-arg 标签) 域。可以简化配置文件中对 property 和 constructor-arg 的配置。

```
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.spring framework.org/schema /c" xmlns:p="http://www.sprin gframework.org/schema/p" xsi:schemaLocation="http://www.springframework.org /schema/beans
http://www.springframework.org /schema /beans/spri ng-bean s.xsd">
　　<bean id="oneBean" class="com.sxt.OneBean" p:a="10" p:o-ref="otherBean" c:a="20" c:o-ref="otherBean"/>
　　<bean id="otherBean" class="com.sxt.OtherBean" />
</beans>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class OneBean{ 
    int a;
    Object o;
    public OneBean(int a, Object o){ 
        this.a = a; 
        this.o = o;
    } // getters and setters for fields.
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 　　8、lookup-method

　　lookup-method 一旦应用，Spring 框架会自动使用 CGLIB 技术为指定类型创建一个动态子类型，并自动实现抽象方法。可以动态的实现依赖注入的数据准备。

　　在效率上，比直接自定义子类型慢。相对来说更加通用。可以只提供 lookup-method 方法的返回值对象即可实现动态的对象返回。

　　在工厂方法难以定制的时候使用，也是模板的一种应用，工厂方法的扩展。如:工厂方法返回对象类型为接口类型，且不同版本应用返回的对象未必相同时使用，可以避免多次开发工厂类。如：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestLookupMethod {
    public static void main(String[] args) {
        ApplicationContext context =
                new ClassPathXmlApplicationContext("classpath:lookupmethod/applicationContext.xml");

        CommandManager manager = context.getBean("manager", CommandManager.class);

        System.out.println(manager.getClass().getName());
        manager.process();
    }
}

abstract class CommandManager {
    public void process() {
        MyCommand command = createCommand();
        // do something ...
        System.out.println(command);
    }

    protected abstract MyCommand createCommand();
}

interface MyCommand {
}

class MyCommand1 implements MyCommand {
    public MyCommand1() {
        System.out.println("MyCommand1 instanced");
    }
}

class MyCommand2 implements MyCommand {
    public MyCommand2() {
        System.out.println("MyCommand2 instanced");
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　applicationContext.xml中配置为：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="manager" class="com.sxt.lookupmethod.CommandManager">
        <lookup-method bean="command2" name="createCommand"/>
    </bean>

    <bean id="command1" class="com.sxt.lookupmethod.MyCommand1"></bean>
    <bean id="command2" class="com.sxt.lookupmethod.MyCommand2"></bean>

</beans>
=======
转载:https://www.cnblogs.com/jing99/p/11809770.html

## 一、什么是IOC

　　引用 Spring 官方原文:This chapter covers the Spring Framework implementation of the Inversion of Control (IoC) [1] principle. IoC is also known as dependency injection (DI). It is a process whereby objects define their dependencies, that is, the other objects they work with, only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then injects those dependencies when it creates the bean. This process is fundamentally the inverse, hence the name Inversion of Control (IoC), of the bean itself controlling the instantiation or location of its dependencies by using direct construction of classes, or a mechanism such as the Service Locator pattern.

　　“控制反转(IoC)”也称为“依赖注入(DI)”，是一个定义对象依赖的过程，对象只和 构造参数，工厂方法参数，对象实例属性或工厂方法返回相关。容器在创建这些 bean 的时 候注入这些依赖。这个过程是一个反向的过程，所以命名为依赖反转，对象实例的创建由其 提供的构造方法或服务定位机制来实现。

　　IOC 最大的好处就是“解耦”。

## 二、IOC底层原理使用技术

　　1、xml配置文件

　　2、dom4j解析配置的xml文件

　　3、工厂、策略、委托等设计模式

　　4、反射

　　5、动态代理（JDK自带动态代理(实现了接口的类)、CGLib(未实现接口的类)）

## 三、Spring容器基本概念

　　Spring 通过一个配置文件描述 Bean 及 Bean 之间的依赖关系，利用 Java 语言的反射功能实例化 Bean 并建立 Bean 之间的依赖关系。 Spring 的 IoC 容器在完成这些底层工作的基础上，还提供了 Bean 实例缓存、生命周期管理、 Bean 实例代理、事件发布、资源装载等高级服务。

　　BeanFactory 是 Spring 框架的基础设施，面向 Spring 本身；

　　ApplicationContext 面向使用 Spring 框架的开发者，几乎所有的应用场合我们都直接使用 ApplicationContext 而非底层的 BeanFactory。

### 　　1、BeanFactory

　  　Spring Bean的创建是典型的工厂模式，这一系列的Bean工厂，也即IOC容器为开发者管理对象间的依赖关系提供了很多便利和基础服务，在Spring中有许多的IOC容器的实现供用户选择和使用。如图：

　　　　　　![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109020841986-812672699.png)

　　其中BeanFactory作为最顶层的一个接口类，它定义了IOC容器的基本功能规范，BeanFactory 有三个子类：ListableBeanFactory、HierarchicalBeanFactory 和AutowireCapableBeanFactory。但是最终的默认实现类是 DefaultListableBeanFactory，他实现了所有的接口。那为何要定义这么多层次的接口呢？其实每个接口都有他使用的场合，它主要是为了区分在 Spring 内部在操作过程中对象的传递和转化过程中，对对象的数据访问所做的限制。例如：

- ListableBeanFactory 接口：表示这些 Bean 是可列表的。
- HierarchicalBeanFactory 接口：表示的是这些 Bean 是有继承关系的，也就是每个Bean 有可能有父 Bean。
- AutowireCapableBeanFactory 接口：定义 Bean 的自动装配规则。

　　这四个接口共同定义了 Bean 的集合、Bean 之间的关系、以及 Bean 行为。BeanFactory接口定义如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public interface BeanFactory {    
     
     //对FactoryBean的转义定义，因为如果使用bean的名字检索FactoryBean得到的对象是工厂生成的对象，    
     //如果需要得到工厂本身，需要转义           
     String FACTORY_BEAN_PREFIX = "&"; 
        
     //根据bean的名字，获取在IOC容器中得到bean实例    
     Object getBean(String name) throws BeansException;    
       
    //根据bean的名字和Class类型来得到bean实例，增加了类型安全验证机制。    
     Object getBean(String name, Class requiredType) throws BeansException;    
    
    //提供对bean的检索，看看是否在IOC容器有这个名字的bean    
     boolean containsBean(String name);    
    
    //根据bean名字得到bean实例，并同时判断这个bean是不是单例    
    boolean isSingleton(String name) throws NoSuchBeanDefinitionException;    
    
    //得到bean实例的Class类型    
    Class getType(String name) throws NoSuchBeanDefinitionException;    
    
    //得到bean的别名，如果根据别名检索，那么其原名也会被检索出来    
    String[] getAliases(String name);    
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 　　2、BeanDefinition

　　SpringIOC容器管理了我们定义的各种Bean对象及其相互的关系。在`BeanFactory`容器中，每一个注入对象都对应一个`BeanDefinition`实例对象，该实例对象负责保存注入对象的所有必要信息，包括其对应的对象的class类型、是否是抽象类、构造方法参数以及其他属性等。当客户端向`BeanFactory`请求相应对象的时候，`BeanFactory`会通过这些信息为客户端返回一个完备可用的对象实例。其继承体系如下：

　　　　　　　　　　　　　　![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109021249026-1634095720.png)

　　那么`BeanDefinition`实例对象的信息是从哪而来呢？这里就要引出一个专门加载解析配置文件的类了，他就是`BeanDefinitionReader`，对应到`xml`配置文件，就是他的子类`XmlBeanDefinitionReader`，`XmlBeanDefinitionReader`负责读取`Spring`指定格式的`XML`配置文件并解析，之后将解析后的文件内容映射到相应的`BeanDefinition`。这个解析过程主要通过下图中的类完成：

　　　　　　　　　　![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109021325017-1587844524.png)

### 　　3、XmlBeanFactory     

　　在BeanFactory里只对IOC容器的基本行为作了定义，根本不关心bean是如何定义、加载、生产的。工厂是如何产生对象的，我们需要看具体的IOC容器实现，spring提供了许多IOC容器的实现。比如XmlBeanFactory，ClasspathXmlApplicationContext等。其中XmlBeanFactory就是针对最基本的ioc容器的实现，这个IOC容器可以读取XML文件定义的BeanDefinition(XML文件中对bean的描述)，因此XmlBeanFactory是一个低级容器。

　　**XmlBeanFactory 初级容器是通过 Resource 装载 Spring 配置信息并启动 IOC 容器**，然后就可以通过 BeanFactory#getBean(beanName)方法从 IOC 容器中获取 Bean 了。通过 BeanFactory 启动IOC 容器时，并不会初始化配置文件中定义的 Bean，初始化动作发生在第一次调用时。

　　对于单实例 (singleton) 的 Bean 来说，BeanFactory 会缓存 Bean 实例，所以第二次使用 getBean() 获取 Bean 时将直接从 IOC 容器的缓存中获取 Bean 实例。**Spring 在 DefaultSingletonBeanRegistry 类中提供了一个用于缓存单实例 Bean 的缓存器，它是一个用ConcurrentHashMap 实现的缓存器，单实例的 Bean 以 beanName 为键保存在这个\**ConcurrentHashMap\** 中。**

　　注意：在初始化 BeanFactory 时，必须为其提供一种日志框架，比如使用Log4J， 即在类路径下提供 Log4J 配置文件，这样启动 Spring 容器才不会报错。

　　XMLBeanFactory定义如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public class XmlBeanFactory extends DefaultListableBeanFactory{
     private final XmlBeanDefinitionReader reader; 
     public XmlBeanFactory(Resource resource)throws BeansException{
         this(resource, null);
     }
     public XmlBeanFactory(Resource resource, BeanFactory parentBeanFactory)
          throws BeansException{
         super(parentBeanFactory);
         this.reader = new XmlBeanDefinitionReader(this);
         this.reader.loadBeanDefinitions(resource);
    }
 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　XMLBeanFactory可以如下使用，实际上初级容器大致使用就是如下方法。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//根据Xml配置文件创建Resource资源对象，该对象中包含了BeanDefinition的信息
 ClassPathResource resource =new ClassPathResource("application-context.xml");
//创建DefaultListableBeanFactory
 DefaultListableBeanFactory factory =new DefaultListableBeanFactory();
//创建XmlBeanDefinitionReader读取器，用于载入BeanDefinition。之所以需要BeanFactory作为参数，是因为会将读取的信息回调配置给factory
 XmlBeanDefinitionReader reader =new XmlBeanDefinitionReader(factory);
//XmlBeanDefinitionReader执行载入BeanDefinition的方法，最后会完成Bean的载入和注册。完成后Bean就成功的放置到IOC容器当中，以后我们就可以从中取得Bean来使用
 reader.loadBeanDefinitions(resource);
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 　　4、ApplicationContext

　　ApplicationContext是Spring提供的一个高级IOC容器，它除了能够提供IOC容器的基本功能外，还为用户提供了以下的附加服务。从ApplicationContext接口的实现，我们看出其特点：

- 支持信息源，可以实现国际化。（实现MessageSource接口）
- 访问资源。(实现ResourcePatternResolver接口，这个后面要讲)
- 支持应用事件。(实现ApplicationEventPublisher接口)

　　其与BeanFactory关系如下：

![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109013948856-338064772.png)

　　Spring为ApplicationContext提供了3种实现，分别是ClassPathXmlApplicationContext、FileSystemXmlApplicationContext、XmlWebApplicationContext，其中XmlWebApplicationContext是专为Web工程定制的。如下图：

　　　　　　　　　　　　![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109023530488-2065388793.png)

　　ApplicationContext允许上下文嵌套，通过保持父上下文可以维持一个上下文体系。对于bean的查找可以在这个上下文体系中发生，首先检查当前上下文，其次是父上下文，逐级向上，这样为不同的Spring应用提供了一个共享的bean定义环境。

### 　　5、ClassPathXmlApplicationContext和FileSystemXmlApplicationContext

　　FileSystemXmlApplicationContext和ClassPathXmlApplicationContext是Spring定义的一种高级容器，不仅仅是IOC。它支持不同信息源头，支持 BeanFactory 工具类，支持层级容器，支持访问文件资源，支持事件发布通知，支持接口回调等。

　　ClassPathXmlApplicationContext的类外部结构关系为：![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191107034407323-1426055756.png)

　　FileSystemXmlApplicationContext的类外部结构关系为（与ClassPathXmlApplicationContext一致）：

![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109025044410-1370859547.jpg)

　　与BeanFactory的xml文件定位方式一样是基于路径的。如：　

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
AplicationContext ctx = new FileSystemXmlApplicationContext("bean.xml"); //加载单个配置文件 

String[] locations = {"bean1.xml", "bean2.xml", "bean3.xml"};  
ApplicationContext ctx = new FileSystemXmlApplicationContext(locations ); //加载多个配置文件

ApplicationContext ctx =new FileSystemXmlApplicationContext("D:roject/bean.xml");//根据具体路径加载文件

// ClassPathXmlApplicationContext同FileSystemXmlApplicationContext一样的初始化方式

// FileSystemXmlApplicationContext这个方法是从文件绝对路径加载配置文件，如果在参数中写的不是绝对路径，那么方法调用的时候也会默认用绝对路径来找，
// 默认文件路径是项目名下一级，与src同级。如果前边加了file:则说明后边的路径就要写全路径了,如：file:D:/workspace/applicationContext.xml。

// ClassPathXmlApplicationContext这个方法是从classpath下加载配置文件(适合于相对路径方式加载)，默认文件路径是src下面那一级。
// 该方法参数中classpath: 前缀是不需要的，默认就是指项目的classpath路径下面；如果要写classpath，请注意classpath:和classpath*:的区别:
// classpath: 只能加载一个配置文件,如果配置了多个,则只加载第一个
// classpath*: 可以加载多个配置文件,如果有多个配置文件,就用这个
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 　　6、XMLWebApplicationContext

　　XMLWebApplicationContext是专门为web应用准备的，他允许从相对于web根目录的路劲中装载配置文件完成初始化工作，从XMLWebApplicationContext中可以获得ServletContext的引用，整个Web应用上下文对象将作为属性放置在ServletContext中，以便web应用可以访问spring上下文，spring中提供WebApplicationContextUtils的getWebApplicationContext(ServletContext serviceContext)方法来获得XMLWebApplicationContext对象。

```
ServletContext servletContext = request.getSession().getServletContext();      
ApplicationContext ctx = WebApplicationContextUtils.getWebApplicationContext(servletContext);  
```

　　ContextLoaderListener和DispatcherServlet 会创建XMLWebApplicationContext容器，但是ContextLoaderListener监听器初始化完毕后，才会初始化Servlet，：

　　XMLWebApplicationContext的类外部结构关系为：

![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191107034526454-1450884761.png)

## 四、Spring IOC容器初始化

　　ApplicationContext 容器的初始化流程主要由 AbstractApplicationContext 类中的 refresh 方法实现。大致过程为:为 BeanFactory 对象执行后续处理(如:context:propertyPlaceholder等)->在上下文(Context)中注册 bean->为 bean 注册拦截处理器(AOP 相关)-非 lazy-init 的 singleton 实例->发布相应事件(Lifecycle 接口相关实现类的生命周期事件发布)。

　　在 spring 中，构建容器的过程都是同步的。同步操作是为了保证容器构建的过程中，不出现多线程资源冲突问题（因为对象的构建、资源的扫描、文件的扫描如果存在多线程对文件的扫描问题，会出现锁的问题）。

### 　　1、构造函数触发IOC初始化

　　从 FileSystemXmlApplicationContext 开始剖析，创建 FileSystemXmlApplicationContext 的构造方法：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public class Test {
    public static void main(String[] args) throws ClassNotFoundException {

        ApplicationContext ctx = new FileSystemXmlApplicationContext
                ("spring-beans/src/test/resources/beans.xml");
        System.out.println("number : " + ctx.getBeanDefinitionCount());
        ((Person) ctx.getBean("person")).work();
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　进入FileSystemXmlApplicationContext类查看构造方法：

　　　　　　![img](https://img2018.cnblogs.com/blog/1010726/201911/1010726-20191109033236309-796849512.png)

　　可以看到该构造方法被重载了，可以传递 configLocation 字符串或者字符串数组，也就是说，可以传递过个配置文件的地址。默认刷新为true，parent 容器为null。并进入另一个构造器：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public FileSystemXmlApplicationContext(String[] configLocations, boolean refresh, @Nullable ApplicationContext parent) throws BeansException {
    super(parent);　　// parent为null
    this.setConfigLocations(configLocations);
    if (refresh) {
        this.refresh();
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 　　2、设置资源加载器和资源定位

　　由ApplicationContext高级容器的构造方法可知它做了3件事情，一是设置设置资源加载器，二是设置资源定位，二是刷新容器（开始IOC容器初始化）。

　　首先，调用父类容器的构造方法(super(parent)方法)为容器设置好Bean资源加载器。

　　然后，再调用父类AbstractRefreshableConfigApplicationContext的setConfigLocations(configLocations)方法设置Bean定义资源文件的定位路径。

　　通过追踪FileSystemXmlApplicationContext的继承体系，发现其父类的父类AbstractApplicationContext中初始化IoC容器所做的主要源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public abstract class AbstractApplicationContext extends DefaultResourceLoader  
        implements ConfigurableApplicationContext, DisposableBean {  
    //静态初始化块，在整个容器创建过程中只执行一次  
    static {  
        //为了避免应用程序在Weblogic8.1关闭时出现类加载异常加载问题，加载IoC容  
        //器关闭事件(ContextClosedEvent)类  
        ContextClosedEvent.class.getName();  
    }  
    //FileSystemXmlApplicationContext调用父类构造方法调用的就是该方法  
    public AbstractApplicationContext(ApplicationContext parent) {  
        this.parent = parent;  
        this.resourcePatternResolver = getResourcePatternResolver();  
    }  
    //获取一个Spring Source的加载器用于读入Spring Bean定义资源文件  
    protected ResourcePatternResolver getResourcePatternResolver() {  
        // AbstractApplicationContext继承DefaultResourceLoader，也是一个S  
        //Spring资源加载器，其getResource(String location)方法用于载入资源  
        return new PathMatchingResourcePatternResolver(this);  
    }   
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　AbstractApplicationContext构造方法中调用PathMatchingResourcePatternResolver的构造方法创建Spring资源加载器：

```
public PathMatchingResourcePatternResolver(ResourceLoader resourceLoader) {  
        Assert.notNull(resourceLoader, "ResourceLoader must not be null");  
        //设置Spring的资源加载器  
        this.resourceLoader = resourceLoader;  
} 
```

　　在设置容器的资源加载器之后，接下来FileSystemXmlApplicationContet执行setConfigLocations方法通过调用其父类AbstractRefreshableConfigApplicationContext的方法进行对Bean定义资源文件的定位，该方法的源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//处理单个资源文件路径为一个字符串的情况  
public void setConfigLocation(String location) {  
   //String CONFIG_LOCATION_DELIMITERS = ",; /t/n";  
   //即多个资源文件路径之间用” ,; /t/n”分隔，解析成数组形式  
    setConfigLocations(StringUtils.tokenizeToStringArray(location, CONFIG_LOCATION_DELIMITERS));  
}  

//解析Bean定义资源文件的路径，处理多个资源文件字符串数组  
 public void setConfigLocations(String[] locations) {  
    if (locations != null) {  
        Assert.noNullElements(locations, "Config locations must not be null");  
        this.configLocations = new String[locations.length];  
        for (int i = 0; i < locations.length; i++) {  
            // resolvePath为同一个类中将字符串解析为路径的方法  
            this.configLocations[i] = resolvePath(locations[i]).trim();  
        }  
    }  
    else {  
        this.configLocations = null;  
    }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 　　3、高级容器初始化入口

　　Spring IoC容器对Bean定义资源的载入是从refresh()函数开始的，refresh()是一个模板方法，refresh()方法的作用是：在创建IoC容器前，如果已经有容器存在，则需要把已有的容器销毁和关闭，以保证在refresh之后使用的是新建立起来的IoC容器。refresh的作用类似于对IoC容器的重启，在新建立好的容器中对容器进行初始化，对Bean定义资源进行载入

　　FileSystemXmlApplicationContext通过调用其父类AbstractApplicationContext的refresh()函数启动整个IoC容器对Bean定义的载入过程：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
/**
 *
 * 1. 构建BeanFactory，以便产生所需要的bean定义实例
 * 2. 注册可能感兴趣的事件
 * 3. 创建bean 实例对象
 * 4. 触发被监听的事件
 *
 */
@Override
public void refresh() throws BeansException, IllegalStateException {
      synchronized (this.startupShutdownMonitor) {
        // 为刷新准备应用上下文
        prepareRefresh();
        // 告诉子类刷新内部bean工厂，即在子类中启动refreshBeanFactory()的地方----创建bean工厂，根据配置文件生成bean定义
        ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();
        // 在这个上下文中使用bean工厂
        prepareBeanFactory(beanFactory);

        try {
            // 设置BeanFactory的后置处理器
            postProcessBeanFactory(beanFactory);
            // 调用BeanFactory的后处理器，这些后处理器是在Bean定义中向容器注册的
            invokeBeanFactoryPostProcessors(beanFactory);
            // 注册Bean的后处理器，在Bean创建过程中调用
            registerBeanPostProcessors(beanFactory);
            //对上下文的消息源进行初始化
            initMessageSource();
            // 初始化上下文中的事件机制
            initApplicationEventMulticaster();
            // 初始化其他的特殊Bean
            onRefresh();
            // 检查监听Bean并且将这些Bean向容器注册
            registerListeners();
            // 实例化所有的（non-lazy-init）单件
            finishBeanFactoryInitialization(beanFactory);
            //  发布容器事件，结束refresh过程
            finishRefresh();
        }catch (BeansException ex) {
            if (logger.isWarnEnabled()) {
                logger.warn("Exception encountered during context initialization - " +
                        "cancelling refresh attempt: " + ex);
            }
            // 为防止bean资源占用，在异常处理中，销毁已经在前面过程中生成的单件bean
            destroyBeans();
            // 重置“active”标志
            cancelRefresh(ex);
            throw ex;
        }

        finally {
            // Reset common introspection caches in Spring's core, since we might not ever need metadata for singleton beans anymore...
            resetCommonCaches();
        }
      }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

- **prepareRefresh** - 准备上下文环境信息，为接下来ApplicationContext 容器的初始化流程做准备。
- **BeanFactory** 的构建。 BeanFactory 是 ApplicationContext 的父接口。是 spring 框架中的顶级容器工厂对象。BeanFactory 只能管理 bean 对象。没有其他功能。如：aop 切面管理、propertyplaceholder 的加载等就不属于BeanFactory的管理范围。 构建 BeanFactory 的功能就是管理 bean 对象。创建 BeanFactory 中管理的 bean 对象。
- **postProcessBeanFactory** - 加载配置中 BeanFactory 无法处理的内容 。如:propertyplacehodler 的加载。
- **invokeBeanFactoryPostProcessors**- 将上一步加载的内容，作为一个容器可以管理的bean对象注册到 ApplicationContext 中。底层实质是在将 postProcessBeanFactory 中加载的内容包装成一个容器 ApplicationContext 可以管理的 bean 对象。
- **registerBeanPostProcessors** - 继续完成上一步的注册操作。配置文件中配置的 bean 对象都创建并注册完成。
- **initMessageSource** - i18n,国际化。初始化国际化消息源。
- **initApplicationEventMulticaster** - 注册事件多播监听。如 ApplicationEvent 事件。是 spring 框架中的观察者模式实现机制。
- **onRefresh** - 初始化主题资源(ThemeSource)。spring 框架提供的视图主题信息。 registerListeners- 创建监听器，并注册。
- **finishBeanFactoryInitialization** - 初始化配置中出现的所有的 lazy-init=false 的 bean 对象，且 bean 对象必须是 singleton 的。
- **finishRefresh** - 最后一步。 发布最终事件。生命周期监听事件。 spring 容器定义了生命周期接口。可以实现容器启动调用初始化，容器销毁之前调用回收资源。Lifecycle 接口。

　　refresh()方法主要为IoC容器Bean的生命周期管理提供条件，Spring IOC容器载入Bean定义资源文件从其子类容器的refreshBeanFactory()方法启动，所以整个refresh()中“ConfigurableListableBeanFactory beanFactory =obtainFreshBeanFactory();” 这句以后代码的都是注册容器的信息源和生命周期事件，载入过程就是从这句代码启动。

　　refresh()方法的作用是：在创建IOC容器前，如果已经有容器存在，则需要把已有的容器销毁和关闭，以保证在refresh之后使用的是新建立起来的IOC容器。refresh的作用类似于对IOC容器的重启，在新建立好的容器中对容器进行初始化，对Bean定义资源进行载入。

### 　　4、XMLBeanFactory低级容器初始化入口

　　AbstractApplicationContext的obtainFreshBeanFactory()方法调用子类容器的refreshBeanFactory()方法，启动容器载入Bean定义资源文件的过程，代码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {  
    //这里使用了委派设计模式，父类定义了抽象的refreshBeanFactory()方法，具体实现调用子类容器的refreshBeanFactory()方法
    refreshBeanFactory();  
    ConfigurableListableBeanFactory beanFactory = getBeanFactory();  
    if (logger.isDebugEnabled()) {  
        logger.debug("Bean factory for " + getDisplayName() + ": " + beanFactory);  
    }  
    return beanFactory;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　AbstractApplicationContext子类的refreshBeanFactory()方法只抽象定义了refreshBeanFactory()方法，容器真正调用的是其子类AbstractRefreshableApplicationContext实现的  refreshBeanFactory()方法，方法的源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
protected final void refreshBeanFactory() throws BeansException {  
   if (hasBeanFactory()) {//如果已经有容器，销毁容器中的bean，关闭容器  
       destroyBeans();  
       closeBeanFactory();  
   }  
   try {  
        // 创建IoC容器 
        // new DefaultListableBeanFactory(getInternalParentBeanFactory()) 
        DefaultListableBeanFactory beanFactory = createBeanFactory();  
        // 设置序列化
        beanFactory.setSerializationId(getId());  
        // 对IoC容器进行定制化，如设置启动参数，开启注解的自动装配等  
        customizeBeanFactory(beanFactory);  
        // 调用载入Bean定义的方法，主要这里又使用了一个委派模式，在当前类中只定义了抽象的loadBeanDefinitions方法，具体的实现调用子类容器  
        loadBeanDefinitions(beanFactory);  
        synchronized (this.beanFactoryMonitor) {  
            this.beanFactory = beanFactory;  
        }  
   }  
   catch (IOException ex) {  
       throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　首先判断是否存在了 BeanFactory，如果有则销毁重新创建，调用 createBeanFactory 方法，该方法中就是像注释写的：创建了 DefaultListableBeanFactory ，也既是说，DefaultListableBeanFactory 就是 BeanFactory的默认实现。

　　接着调用loadBeanDefinitions(beanFactory)装载bean的Definitions， Definition 是核心之一，代表着 IOC 中的基本数据结构。

### 　　5、调用loadBeanDefinitions载入Bean定义

　　该方法也是个抽象方法，默认实现是AbstractRefreshableApplicationContext的子类AbstractXmlApplicationContext ，继续看该方法实现：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public abstract class AbstractXmlApplicationContext extends AbstractRefreshableConfigApplicationContext {  
    //实现父类抽象的载入Bean定义方法  
    @Override  
    protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) throws BeansException, IOException {  
        // 创建XmlBeanDefinitionReader，即创建Bean读取器，并通过回调设置到容器中去，容器使用该读取器读取Bean定义资源  
        XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);  
        // 为Bean读取器设置Spring资源加载器，AbstractXmlApplicationContext的祖先父类AbstractApplicationContext继承DefaultResourceLoader　　　　 // 因此，容器本身也是一个资源加载器  
        beanDefinitionReader.setResourceLoader(this);  
        // 为Bean读取器设置SAX xml解析器  
        beanDefinitionReader.setEntityResolver(new ResourceEntityResolver(this));  
        // 当Bean读取器读取Bean定义的Xml资源文件时，启用Xml的校验机制  
        initBeanDefinitionReader(beanDefinitionReader);  
        // Bean读取器真正实现加载的方法  
        loadBeanDefinitions(beanDefinitionReader);  
   }  
   // Xml Bean读取器加载Bean定义资源  
   protected void loadBeanDefinitions(XmlBeanDefinitionReader reader) throws BeansException, IOException {  
       // 获取Bean定义资源的定位  
       Resource[] configResources = getConfigResources();  
       if (configResources != null) {  
           // Xml Bean读取器调用其父类AbstractBeanDefinitionReader读取定位的Bean定义资源  
           reader.loadBeanDefinitions(configResources);  
       }  
       // 如果子类中获取的Bean定义资源定位为空，则获取FileSystemXmlApplicationContext构造方法中setConfigLocations方法设置的资源  
       String[] configLocations = getConfigLocations();  
       if (configLocations != null) {  
           // Xml Bean读取器调用其父类AbstractBeanDefinitionReader读取定位的Bean定义资源  
           reader.loadBeanDefinitions(configLocations);  
       }  
   }  
   // 这里又使用了一个委托模式，调用子类的获取Bean定义资源定位的方法，该方法在ClassPathXmlApplicationContext中进行实现
   // 对于我们举例分析源码的FileSystemXmlApplicationContext没有使用该方法  
   protected Resource[] getConfigResources() {  
       return null;  
   }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　Xml Bean读取器(XmlBeanDefinitionReader)调用其父类AbstractBeanDefinitionReader的 reader.loadBeanDefinitions方法读取Bean定义资源。由于我们使用FileSystemXmlApplicationContext作为例子分析，因此getConfigResources的返回值为null，因此程序跳过第一个if，进入第二个if执行reader.loadBeanDefinitions(configLocations)分支。

### 　　6、AbstractBeanDefinitionReader读取Bean定义资源

　　XmlBeanDefinitionReader在其抽象父类AbstractBeanDefinitionReader中定义了载入过程，loadBeanDefinitions方法源码如下(注意方法入参是location)：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//重载方法，调用下面的loadBeanDefinitions(String, Set<Resource>);方法  
public int loadBeanDefinitions(String location) throws BeanDefinitionStoreException {  
   return loadBeanDefinitions(location, null);  
}  
//重载方法，调用loadBeanDefinitions(String);  
public int loadBeanDefinitions(String... locations) throws BeanDefinitionStoreException {  
   Assert.notNull(locations, "Location array must not be null");  
   int counter = 0;  
   for (String location : locations) {  
       counter += loadBeanDefinitions(location);  
   }  
   return counter;  
}

public int loadBeanDefinitions(String location, Set<Resource> actualResources) throws BeanDefinitionStoreException {  
   //获取在IoC容器初始化过程中设置的资源加载器  
   ResourceLoader resourceLoader = getResourceLoader();  
   if (resourceLoader == null) {  
       throw new BeanDefinitionStoreException(  
               "Cannot import bean definitions from location [" + location + "]: no ResourceLoader available");  
   }  
   if (resourceLoader instanceof ResourcePatternResolver) {  
       try {  
           //将指定位置的Bean定义资源文件解析为Spring IoC容器封装的资源  
           //加载多个指定位置的Bean定义资源文件  
           Resource[] resources = ((ResourcePatternResolver) resourceLoader).getResources(location);  
           //委派调用其子类XmlBeanDefinitionReader的方法，实现加载功能  
           int loadCount = loadBeanDefinitions(resources);  
           if (actualResources != null) {  
               for (Resource resource : resources) {  
                   actualResources.add(resource);  
               }  
           }  
           if (logger.isDebugEnabled()) {  
               logger.debug("Loaded " + loadCount + " bean definitions from location pattern [" + location + "]");  
           }  
           return loadCount;  
       }  
       catch (IOException ex) {  
           throw new BeanDefinitionStoreException(  
                   "Could not resolve bean definition resource pattern [" + location + "]", ex);  
       }  
   }  
   else {  
       //将指定位置的Bean定义资源文件解析为Spring IoC容器封装的资源  
       //加载单个指定位置的Bean定义资源文件  
       Resource resource = resourceLoader.getResource(location);  
       //委派调用其子类XmlBeanDefinitionReader的方法，实现加载功能  
       int loadCount = loadBeanDefinitions(resource);  
       if (actualResources != null) {  
           actualResources.add(resource);  
       }  
       if (logger.isDebugEnabled()) {  
           logger.debug("Loaded " + loadCount + " bean definitions from location [" + location + "]");  
       }  
       return loadCount;  
   }  
} 
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　loadBeanDefinitions(Resource...resources)方法和上面分析的3个方法类似，同样也是调用XmlBeanDefinitionReader的loadBeanDefinitions方法。如代码：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public int loadBeanDefinitions(Resource... resources) throws BeanDefinitionStoreException {
    Assert.notNull(resources, "Resource array must not be null");
    int counter = 0;
    Resource[] var3 = resources;
    int var4 = resources.length;

    for(int var5 = 0; var5 < var4; ++var5) {
        Resource resource = var3[var5];
        counter += this.loadBeanDefinitions((Resource)resource);
    }
    
    return counter;
} 
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　从对AbstractBeanDefinitionReader的loadBeanDefinitions方法源码分析可以看出该方法做了以下两件事：

　　首先，调用资源加载器的获取资源方法resourceLoader.getResource(location)，获取到要加载的资源。

　　其次，真正执行加载功能是其子类XmlBeanDefinitionReader的loadBeanDefinitions方法。

　　结合ResourceLoader与ApplicationContext的继承关系图，可以知道此时调用的是DefaultResourceLoader中的getSource()方法定位Resource，因为FileSystemXmlApplicationContext本身就是DefaultResourceLoader的实现类，所以此时又回到了FileSystemXmlApplicationContext中来。

### 　　7、资源加载器获取要读入的资源

　　XmlBeanDefinitionReader通过调用其父类DefaultResourceLoader的getResource方法获取要加载的资源，其源码如下:

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//获取Resource的具体实现方法  
public Resource getResource(String location) {  
   Assert.notNull(location, "Location must not be null");  
   //如果是类路径的方式，那需要使用ClassPathResource 来得到bean 文件的资源对象  
   if (location.startsWith(CLASSPATH_URL_PREFIX)) {  
       return new ClassPathResource(location.substring(CLASSPATH_URL_PREFIX.length()), getClassLoader());  
   }  
    try {  
         // 如果是URL 方式，使用UrlResource 作为bean 文件的资源对象  
        URL url = new URL(location);  
        return new UrlResource(url);  
       }  
       catch (MalformedURLException ex) { 
       } 
       //如果既不是classpath标识，又不是URL标识的Resource定位，则调用  
       //容器本身的getResourceByPath方法获取Resource  
       return getResourceByPath(location);  
} 
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　FileSystemXmlApplicationContext容器提供了getResourceByPath方法的实现，就是为了处理既不是classpath标识，又不是URL标识的Resource定位这种情况。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
protected Resource getResourceByPath(String path) {    
   if (path != null && path.startsWith("/")) {    
        path = path.substring(1);    
    }  
    //这里使用文件系统资源对象来定义bean 文件
    return new FileSystemResource(path);  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　这样代码就回到了 FileSystemXmlApplicationContext 中来，他提供了FileSystemResource 来完成从文件系统得到配置文件的资源定义。

　　这样，就可以从文件系统路径上对IOC 配置文件进行加载 - 当然我们可以按照这个逻辑从任何地方加载，在Spring 中我们看到它提供 的各种资源抽象，比如ClassPathResource, URLResource,FileSystemResource 等来供我们使用。上面我们看到的是定位Resource 的一个过程，而这只是加载过程的一部分。

### 　　8、XmlBeanDefinitionReader加载Bean定义资源

   Bean定义的Resource得到了后，继续回到XmlBeanDefinitionReader的loadBeanDefinitions(Resource …)方法看到代表bean文件的资源定义以后的载入过程。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
// XmlBeanDefinitionReader加载资源的入口方法  
public int loadBeanDefinitions(Resource resource) throws BeanDefinitionStoreException {  
   // 将读入的XML资源进行特殊编码处理  
   return loadBeanDefinitions(new EncodedResource(resource));  
} 
// 这里是载入XML形式Bean定义资源文件方法
public int loadBeanDefinitions(EncodedResource encodedResource) throws BeanDefinitionStoreException {  　　... ...     
   try {    
        // 将资源文件转为InputStream的IO流 
       InputStream inputStream = encodedResource.getResource().getInputStream();    
       try {    
          // 从InputStream中得到XML的解析源    
           InputSource inputSource = new InputSource(inputStream);    
           if (encodedResource.getEncoding() != null) {    
               inputSource.setEncoding(encodedResource.getEncoding());    
           }    
           // 这里是具体的读取过程    
           return doLoadBeanDefinitions(inputSource, encodedResource.getResource());    
       }    
       finally {    
           // 关闭从Resource中得到的IO流    
           inputStream.close();    
       }    　　... ...
   }    
}    
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　进入 loadBeanDefinitions(resource) 方法中，最后进入到 XmlBeanDefinitionReader.loadBeanDefinitions(EncodedResource encodedResource) 方法中，该方法主要调用 doLoadBeanDefinitions(inputSource, encodedResource.getResource()) 方法。其实现为：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
// 从特定XML文件中实际载入Bean定义资源的方法 
protected int doLoadBeanDefinitions(InputSource inputSource, Resource resource) throws BeanDefinitionStoreException {
    try {
        // 将XML文件转换为DOM对象，解析过程由documentLoader实现 
        Document doc = this.doLoadDocument(inputSource, resource);
        // 这里是启动对Bean定义解析的详细过程，该解析过程会用到Spring的Bean配置规则
        return this.registerBeanDefinitions(doc, resource);
    } catch (BeanDefinitionStoreException var4) {
        throw var4;
    } catch (SAXParseException var5) {
        throw new XmlBeanDefinitionStoreException(resource.getDescription(), "Line " + var5.getLineNumber() + " in XML document from " + resource + " is invalid", var5);
    } catch (SAXException var6) {
        throw new XmlBeanDefinitionStoreException(resource.getDescription(), "XML document from " + resource + " is invalid", var6);
    } catch (ParserConfigurationException var7) {
        throw new BeanDefinitionStoreException(resource.getDescription(), "Parser configuration exception parsing XML from " + resource, var7);
    } catch (IOException var8) {
        throw new BeanDefinitionStoreException(resource.getDescription(), "IOException parsing XML document from " + resource, var8);
    } catch (Throwable var9) {
        throw new BeanDefinitionStoreException(resource.getDescription(), "Unexpected exception parsing XML document from " + resource, var9);
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过源码分析，载入Bean定义资源文件的最后一步是将Bean定义资源转换为Document对象，该过程由documentLoader实现。

### 　　9、DocumentLoader将Bean定义资源转换为Document对象

　　DocumentLoader通过其实现类DefaultDocumentLoader将Bean定义资源转换成Document对象，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//使用标准的JAXP将载入的Bean定义资源转换成document对象  
public Document loadDocument(InputSource inputSource, EntityResolver entityResolver,  
       ErrorHandler errorHandler, int validationMode, boolean namespaceAware) throws Exception {  
   //创建文件解析器工厂  
   DocumentBuilderFactory factory = createDocumentBuilderFactory(validationMode, namespaceAware);  
   if (logger.isDebugEnabled()) {  
       logger.debug("Using JAXP provider [" + factory.getClass().getName() + "]");  
   }  
   //创建文档解析器  
   DocumentBuilder builder = createDocumentBuilder(factory, entityResolver, errorHandler);  
   //解析Spring的Bean定义资源  
   return builder.parse(inputSource);  
}  
protected DocumentBuilderFactory createDocumentBuilderFactory(int validationMode, boolean namespaceAware)  
       throws ParserConfigurationException {  
   //创建文档解析工厂  
   DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();  
   factory.setNamespaceAware(namespaceAware);  
   //设置解析XML的校验  
   if (validationMode != XmlValidationModeDetector.VALIDATION_NONE) {  
       factory.setValidating(true);  
       if (validationMode == XmlValidationModeDetector.VALIDATION_XSD) {  
           factory.setNamespaceAware(true);  
           try {  
               factory.setAttribute(SCHEMA_LANGUAGE_ATTRIBUTE, XSD_SCHEMA_LANGUAGE);  
           }  
           catch (IllegalArgumentException ex) {  
               ParserConfigurationException pcex = new ParserConfigurationException(  
                       "Unable to validate using XSD: Your JAXP provider [" + factory +  
                       "] does not support XML Schema. Are you running on Java 1.4 with Apache Crimson? " +  
                       "Upgrade to Apache Xerces (or Java 1.5) for full XSD support.");  
               pcex.initCause(ex);  
               throw pcex;  
           }  
       }  
   }  
   return factory;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　该解析过程调用JavaEE标准的JAXP标准进行处理。至此Spring IOC容器根据定位的Bean定义资源文件，将其加载读入并转换成为Document对象过程完成。接下来我们要继续分析Spring IOC容器将载入的Bean定义资源文件转换为Document对象之后，是如何将其解析为Spring IOC管理的Bean对象并将其注册到容器中的。

### 　　10、XmlBeanDefinitionReader解析载入的Bean定义资源文件

　　XmlBeanDefinitionReader类中的doLoadBeanDefinitions方法是从特定XML文件中实际载入Bean定义资源的方法，该方法在载入Bean定义资源之后将其转换为Document对象，接下来调用registerBeanDefinitions启动Spring IOC容器对Bean定义的解析过程，registerBeanDefinitions方法源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//按照Spring的Bean语义要求将Bean定义资源解析并转换为容器内部数据结构  
public int registerBeanDefinitions(Document doc, Resource resource) throws BeanDefinitionStoreException {  
   //得到BeanDefinitionDocumentReader来对xml格式的BeanDefinition解析  
   BeanDefinitionDocumentReader documentReader = createBeanDefinitionDocumentReader();  
   //获得容器中注册的Bean数量  
   int countBefore = getRegistry().getBeanDefinitionCount();  
   //解析过程入口，这里使用了委派模式，BeanDefinitionDocumentReader只是个接口，//具体的解析实现过程有实现类DefaultBeanDefinitionDocumentReader完成  
   documentReader.registerBeanDefinitions(doc, createReaderContext(resource));  
   //统计解析的Bean数量  
   return getRegistry().getBeanDefinitionCount() - countBefore;  
}  
//创建BeanDefinitionDocumentReader对象，解析Document对象  
protected BeanDefinitionDocumentReader createBeanDefinitionDocumentReader() {  
   return BeanDefinitionDocumentReader.class.cast(BeanUtils.instantiateClass(this.documentReaderClass));  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　Bean定义资源的载入解析分为以下两个过程：

　　首先，通过调用XML解析器将Bean定义资源文件转换得到Document对象，但是这些Document对象并没有按照Spring的Bean规则进行解析。这一步是载入的过程

　　其次，在完成通用的XML解析之后，按照Spring的Bean规则对Document对象进行解析。

　　按照Spring的Bean规则对Document对象解析的过程是在接口BeanDefinitionDocumentReader的实现类DefaultBeanDefinitionDocumentReader中实现的。

### 　　11、DefaultBeanDefinitionDocumentReader对Bean定义的Document对象解析

　　BeanDefinitionDocumentReader接口通过registerBeanDefinitions方法调用其实现类DefaultBeanDefinitionDocumentReader对Document对象进行解析，解析的代码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//根据Spring DTD对Bean的定义规则解析Bean定义Document对象  
public void registerBeanDefinitions(Document doc, XmlReaderContext readerContext) {  
    //获得XML描述符  
    this.readerContext = readerContext;  
    logger.debug("Loading bean definitions");  
    //获得Document的根元素  
    Element root = doc.getDocumentElement();  
    //具体的解析过程由BeanDefinitionParserDelegate实现，  
    //BeanDefinitionParserDelegate中定义了Spring Bean定义XML文件的各种元素  
   BeanDefinitionParserDelegate delegate = createHelper(readerContext, root);  
   //在解析Bean定义之前，进行自定义的解析，增强解析过程的可扩展性  
   preProcessXml(root);  
   //从Document的根元素开始进行Bean定义的Document对象  
   parseBeanDefinitions(root, delegate);  
   //在解析Bean定义之后，进行自定义的解析，增加解析过程的可扩展性  
   postProcessXml(root);  
}  
//创建BeanDefinitionParserDelegate，用于完成真正的解析过程  
protected BeanDefinitionParserDelegate createHelper(XmlReaderContext readerContext, Element root) {  
   BeanDefinitionParserDelegate delegate = new BeanDefinitionParserDelegate(readerContext);  
   //BeanDefinitionParserDelegate初始化Document根元素  
   delegate.initDefaults(root);  
   return delegate;  
}  
//使用Spring的Bean规则从Document的根元素开始进行Bean定义的Document对象  
protected void parseBeanDefinitions(Element root, BeanDefinitionParserDelegate delegate) {  
   //Bean定义的Document对象使用了Spring默认的XML命名空间  
   if (delegate.isDefaultNamespace(root)) {  
       //获取Bean定义的Document对象根元素的所有子节点  
       NodeList nl = root.getChildNodes();  
       for (int i = 0; i < nl.getLength(); i++) {  
           Node node = nl.item(i);  
           //获得Document节点是XML元素节点  
           if (node instanceof Element) {  
               Element ele = (Element) node;  
           //Bean定义的Document的元素节点使用的是Spring默认的XML命名空间  
               if (delegate.isDefaultNamespace(ele)) {  
                   //使用Spring的Bean规则解析元素节点  
                   parseDefaultElement(ele, delegate);  
               }  
               else {  
                   //没有使用Spring默认的XML命名空间，则使用用户自定义的解//析规则解析元素节点  
                   delegate.parseCustomElement(ele);  
               }  
           }  
       }  
   }  
   else {  
       //Document的根节点没有使用Spring默认的命名空间，则使用用户自定义的  
       //解析规则解析Document根节点  
       delegate.parseCustomElement(root);  
   }  
}  
//使用Spring的Bean规则解析Document元素节点  
private void parseDefaultElement(Element ele, BeanDefinitionParserDelegate delegate) {  
   //如果元素节点是<Import>导入元素，进行导入解析  
   if (delegate.nodeNameEquals(ele, IMPORT_ELEMENT)) {  
       importBeanDefinitionResource(ele);  
   }  
   //如果元素节点是<Alias>别名元素，进行别名解析  
   else if (delegate.nodeNameEquals(ele, ALIAS_ELEMENT)) {  
       processAliasRegistration(ele);  
   }  
   //元素节点既不是导入元素，也不是别名元素，即普通的<Bean>元素，  
   //按照Spring的Bean规则解析元素  
   else if (delegate.nodeNameEquals(ele, BEAN_ELEMENT)) {  
       processBeanDefinition(ele, delegate);  
   }  
}  
//解析<Import>导入元素，从给定的导入路径加载Bean定义资源到Spring IoC容器中  
protected void importBeanDefinitionResource(Element ele) {  
   //获取给定的导入元素的location属性  
   String location = ele.getAttribute(RESOURCE_ATTRIBUTE);  
   //如果导入元素的location属性值为空，则没有导入任何资源，直接返回  
   if (!StringUtils.hasText(location)) {  
       getReaderContext().error("Resource location must not be empty", ele);  
       return;  
   }  
   //使用系统变量值解析location属性值  
   location = SystemPropertyUtils.resolvePlaceholders(location);  
   Set<Resource> actualResources = new LinkedHashSet<Resource>(4);  
   //标识给定的导入元素的location是否是绝对路径  
   boolean absoluteLocation = false;  
   try {  
       absoluteLocation = ResourcePatternUtils.isUrl(location) || ResourceUtils.toURI(location).isAbsolute();  
   }  
   catch (URISyntaxException ex) {  
       //给定的导入元素的location不是绝对路径  
   }  
   //给定的导入元素的location是绝对路径  
   if (absoluteLocation) {  
       try {  
           //使用资源读入器加载给定路径的Bean定义资源  
           int importCount = getReaderContext().getReader().loadBeanDefinitions(location, actualResources);  
           if (logger.isDebugEnabled()) {  
               logger.debug("Imported " + importCount + " bean definitions from URL location [" + location + "]");  
           }  
       }  
       catch (BeanDefinitionStoreException ex) {  
           getReaderContext().error(  
                   "Failed to import bean definitions from URL location [" + location + "]", ele, ex);  
       }  
   }  
   else {  
       //给定的导入元素的location是相对路径  
       try {  
           int importCount;  
           //将给定导入元素的location封装为相对路径资源  
           Resource relativeResource = getReaderContext().getResource().createRelative(location);  
           //封装的相对路径资源存在  
           if (relativeResource.exists()) {  
               //使用资源读入器加载Bean定义资源  
               importCount = getReaderContext().getReader().loadBeanDefinitions(relativeResource);  
               actualResources.add(relativeResource);  
           }  
           //封装的相对路径资源不存在  
           else {  
               //获取Spring IoC容器资源读入器的基本路径  
               String baseLocation = getReaderContext().getResource().getURL().toString();  
               //根据Spring IoC容器资源读入器的基本路径加载给定导入  
               //路径的资源  
               importCount = getReaderContext().getReader().loadBeanDefinitions(  
                       StringUtils.applyRelativePath(baseLocation, location), actualResources);  
           }  
           if (logger.isDebugEnabled()) {  
               logger.debug("Imported " + importCount + " bean definitions from relative location [" + location + "]");  
           }  
       }  
       catch (IOException ex) {  
           getReaderContext().error("Failed to resolve current resource location", ele, ex);  
       }  
       catch (BeanDefinitionStoreException ex) {  
           getReaderContext().error("Failed to import bean definitions from relative location [" + location + "]",  
                   ele, ex);  
       }  
   }  
   Resource[] actResArray = actualResources.toArray(new Resource[actualResources.size()]);  
   //在解析完<Import>元素之后，发送容器导入其他资源处理完成事件  
   getReaderContext().fireImportProcessed(location, actResArray, extractSource(ele));  
}  
//解析<Alias>别名元素，为Bean向Spring IoC容器注册别名  
protected void processAliasRegistration(Element ele) {  
   //获取<Alias>别名元素中name的属性值  
   String name = ele.getAttribute(NAME_ATTRIBUTE);  
   //获取<Alias>别名元素中alias的属性值  
   String alias = ele.getAttribute(ALIAS_ATTRIBUTE);  
   boolean valid = true;  
   //<alias>别名元素的name属性值为空  
   if (!StringUtils.hasText(name)) {  
       getReaderContext().error("Name must not be empty", ele);  
       valid = false;  
   }  
   //<alias>别名元素的alias属性值为空  
   if (!StringUtils.hasText(alias)) {  
       getReaderContext().error("Alias must not be empty", ele);  
       valid = false;  
   }  
   if (valid) {  
       try {  
           //向容器的资源读入器注册别名  
           getReaderContext().getRegistry().registerAlias(name, alias);  
       }  
       catch (Exception ex) {  
           getReaderContext().error("Failed to register alias '" + alias +  
                   "' for bean with name '" + name + "'", ele, ex);  
       }  
       //在解析完<Alias>元素之后，发送容器别名处理完成事件  
       getReaderContext().fireAliasRegistered(name, alias, extractSource(ele));  
   }  
}  
//解析Bean定义资源Document对象的普通元素  
protected void processBeanDefinition(Element ele, BeanDefinitionParserDelegate delegate) {  
   // BeanDefinitionHolder是对BeanDefinition的封装，即Bean定义的封装类  
   //对Document对象中<Bean>元素的解析由BeanDefinitionParserDelegate实现  BeanDefinitionHolder bdHolder = delegate.parseBeanDefinitionElement(ele);  
   if (bdHolder != null) {  
       bdHolder = delegate.decorateBeanDefinitionIfRequired(ele, bdHolder);  
       try {  
          //向Spring IoC容器注册解析得到的Bean定义，这是Bean定义向IoC容器注册的入口            
              BeanDefinitionReaderUtils.registerBeanDefinition(bdHolder, getReaderContext().getRegistry());  
       }  
       catch (BeanDefinitionStoreException ex) {  
           getReaderContext().error("Failed to register bean definition with name '" +  
                   bdHolder.getBeanName() + "'", ele, ex);  
       }  
       //在完成向Spring IoC容器注册解析得到的Bean定义之后，发送注册事件  
       getReaderContext().fireComponentRegistered(new BeanComponentDefinition(bdHolder));  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过上述Spring IOC容器对载入的Bean定义Document解析可以看出，我们使用Spring时，在Spring配置文件中可以使用<Import>元素来导入IOC容器所需要的其他资源，Spring IOC容器在解析时会首先将指定导入的资源加载进容器中。使用<Ailas>别名时，Spring IoC容器首先将别名元素所定义的别名注册到容器中。

　　对于既不是<Import>元素，又不是<Alias>元素的元素，即Spring配置文件中普通的<Bean>元素的解析由BeanDefinitionParserDelegate类的parseBeanDefinitionElement方法来实现。

### 　　12、BeanDefinitionParserDelegate解析Bean定义资源文件中的<Bean>元素

　　Bean定义资源文件中的<Import>和<Alias>元素解析在DefaultBeanDefinitionDocumentReader中已经完成，对Bean定义资源文件中使用最多的<Bean>元素交由BeanDefinitionParserDelegate来解析，其解析实现的源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//解析<Bean>元素的入口  
   public BeanDefinitionHolder parseBeanDefinitionElement(Element ele) {  
       return parseBeanDefinitionElement(ele, null);  
   }  
   //解析Bean定义资源文件中的<Bean>元素，这个方法中主要处理<Bean>元素的id，name  
   //和别名属性  
   public BeanDefinitionHolder parseBeanDefinitionElement(Element ele, BeanDefinition containingBean) {  
       //获取<Bean>元素中的id属性值  
       String id = ele.getAttribute(ID_ATTRIBUTE);  
       //获取<Bean>元素中的name属性值  
       String nameAttr = ele.getAttribute(NAME_ATTRIBUTE);  
       ////获取<Bean>元素中的alias属性值  
       List<String> aliases = new ArrayList<String>();  
       //将<Bean>元素中的所有name属性值存放到别名中  
       if (StringUtils.hasLength(nameAttr)) {  
           String[] nameArr = StringUtils.tokenizeToStringArray(nameAttr, BEAN_NAME_DELIMITERS);  
           aliases.addAll(Arrays.asList(nameArr));  
       }  
       String beanName = id;  
       //如果<Bean>元素中没有配置id属性时，将别名中的第一个值赋值给beanName  
       if (!StringUtils.hasText(beanName) && !aliases.isEmpty()) {  
           beanName = aliases.remove(0);  
           if (logger.isDebugEnabled()) {  
               logger.debug("No XML 'id' specified - using '" + beanName +  
                       "' as bean name and " + aliases + " as aliases");  
           }  
       }  
       //检查<Bean>元素所配置的id或者name的唯一性，containingBean标识<Bean>  
       //元素中是否包含子<Bean>元素  
       if (containingBean == null) {  
           //检查<Bean>元素所配置的id、name或者别名是否重复  
           checkNameUniqueness(beanName, aliases, ele);  
       }  
       //详细对<Bean>元素中配置的Bean定义进行解析的地方  
       AbstractBeanDefinition beanDefinition = parseBeanDefinitionElement(ele, beanName, containingBean);  
       if (beanDefinition != null) {  
           if (!StringUtils.hasText(beanName)) {  
               try {  
                   if (containingBean != null) {  
                       //如果<Bean>元素中没有配置id、别名或者name，且没有包含子//<Bean>元素，为解析的Bean生成一个唯一beanName并注册  
                       beanName = BeanDefinitionReaderUtils.generateBeanName(  
                               beanDefinition, this.readerContext.getRegistry(), true);  
                   }  
                   else {  
                       //如果<Bean>元素中没有配置id、别名或者name，且包含了子//<Bean>元素，为解析的Bean使用别名向IoC容器注册  
                       beanName = this.readerContext.generateBeanName(beanDefinition);  
                       //为解析的Bean使用别名注册时，为了向后兼容                                    //Spring1.2/2.0，给别名添加类名后缀  
                       String beanClassName = beanDefinition.getBeanClassName();  
                       if (beanClassName != null &&  
                               beanName.startsWith(beanClassName) && beanName.length() > beanClassName.length() &&  
                               !this.readerContext.getRegistry().isBeanNameInUse(beanClassName)) {  
                           aliases.add(beanClassName);  
                       }  
                   }  
                   if (logger.isDebugEnabled()) {  
                       logger.debug("Neither XML 'id' nor 'name' specified - " +  
                               "using generated bean name [" + beanName + "]");  
                   }  
               }  
               catch (Exception ex) {  
                   error(ex.getMessage(), ele);  
                   return null;  
               }  
           }  
           String[] aliasesArray = StringUtils.toStringArray(aliases);  
           return new BeanDefinitionHolder(beanDefinition, beanName, aliasesArray);  
       }  
       //当解析出错时，返回null  
       return null;  
   }  
   //详细对<Bean>元素中配置的Bean定义其他属性进行解析，由于上面的方法中已经对//Bean的id、name和别名等属性进行了处理，该方法中主要处理除这三个以外的其他属性数据  
   public AbstractBeanDefinition parseBeanDefinitionElement(  
           Element ele, String beanName, BeanDefinition containingBean) {  
       //记录解析的<Bean>  
       this.parseState.push(new BeanEntry(beanName));  
       //这里只读取<Bean>元素中配置的class名字，然后载入到BeanDefinition中去  
       //只是记录配置的class名字，不做实例化，对象的实例化在依赖注入时完成  
       String className = null;  
       if (ele.hasAttribute(CLASS_ATTRIBUTE)) {  
           className = ele.getAttribute(CLASS_ATTRIBUTE).trim();  
       }  
       try {  
           String parent = null;  
           //如果<Bean>元素中配置了parent属性，则获取parent属性的值  
           if (ele.hasAttribute(PARENT_ATTRIBUTE)) {  
               parent = ele.getAttribute(PARENT_ATTRIBUTE);  
           }  
           //根据<Bean>元素配置的class名称和parent属性值创建BeanDefinition  
           //为载入Bean定义信息做准备  
           AbstractBeanDefinition bd = createBeanDefinition(className, parent);  
           //对当前的<Bean>元素中配置的一些属性进行解析和设置，如配置的单态(singleton)属性等  
           parseBeanDefinitionAttributes(ele, beanName, containingBean, bd);  
           //为<Bean>元素解析的Bean设置description信息 bd.setDescription(DomUtils.getChildElementValueByTagName(ele, DESCRIPTION_ELEMENT));  
           //对<Bean>元素的meta(元信息)属性解析  
           parseMetaElements(ele, bd);  
           //对<Bean>元素的lookup-method属性解析  
           parseLookupOverrideSubElements(ele, bd.getMethodOverrides());  
           //对<Bean>元素的replaced-method属性解析  
           parseReplacedMethodSubElements(ele, bd.getMethodOverrides());  
           //解析<Bean>元素的构造方法设置  
           parseConstructorArgElements(ele, bd);  
           //解析<Bean>元素的<property>设置  
           parsePropertyElements(ele, bd);  
           //解析<Bean>元素的qualifier属性  
           parseQualifierElements(ele, bd);  
           //为当前解析的Bean设置所需的资源和依赖对象  
           bd.setResource(this.readerContext.getResource());  
           bd.setSource(extractSource(ele));  
           return bd;  
       }  
       catch (ClassNotFoundException ex) {  
           error("Bean class [" + className + "] not found", ele, ex);  
       }  
       catch (NoClassDefFoundError err) {  
           error("Class that bean class [" + className + "] depends on not found", ele, err);  
       }  
       catch (Throwable ex) {  
           error("Unexpected failure during bean definition parsing", ele, ex);  
       }  
       finally {  
           this.parseState.pop();  
       }  
       //解析<Bean>元素出错时，返回null  
       return null;  
   }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　只要使用过Spring，对Spring配置文件比较熟悉的人，通过对上述源码的分析，就会明白我们在Spring配置文件中<Bean>元素的中配置的属性就是通过该方法解析和设置到Bean中去的。

　　注意：在解析<Bean>元素过程中没有创建和实例化Bean对象，只是创建了Bean对象的定义类BeanDefinition，将<Bean>元素中的配置信息设置到BeanDefinition中作为记录，当依赖注入时才使用这些记录信息创建和实例化具体的Bean对象。

　　上面方法中一些对一些配置如元信息(meta)、qualifier等的解析，我们在Spring中配置时使用的也不多，我们在使用Spring的<Bean>元素时，配置最多的是<property>属性，因此我们下面继续分析源码，了解Bean的属性在解析时是如何设置的。

### 　　13、BeanDefinitionParserDelegate解析<property>元素

　　BeanDefinitionParserDelegate在解析<Bean>调用parsePropertyElements方法解析<Bean>元素中的<property>属性子元素，解析源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//解析<Bean>元素中的<property>子元素  
public void parsePropertyElements(Element beanEle, BeanDefinition bd) {  
   //获取<Bean>元素中所有的子元素  
   NodeList nl = beanEle.getChildNodes();  
   for (int i = 0; i < nl.getLength(); i++) {  
       Node node = nl.item(i);  
       //如果子元素是<property>子元素，则调用解析<property>子元素方法解析  
       if (isCandidateElement(node) && nodeNameEquals(node, PROPERTY_ELEMENT)) {  
           parsePropertyElement((Element) node, bd);  
       }  
   }  
}  
//解析<property>元素  
public void parsePropertyElement(Element ele, BeanDefinition bd) {  
   //获取<property>元素的名字   
   String propertyName = ele.getAttribute(NAME_ATTRIBUTE);  
   if (!StringUtils.hasLength(propertyName)) {  
       error("Tag 'property' must have a 'name' attribute", ele);  
       return;  
   }  
   this.parseState.push(new PropertyEntry(propertyName));  
   try {  
       //如果一个Bean中已经有同名的property存在，则不进行解析，直接返回。  
       //即如果在同一个Bean中配置同名的property，则只有第一个起作用  
       if (bd.getPropertyValues().contains(propertyName)) {  
           error("Multiple 'property' definitions for property '" + propertyName + "'", ele);  
           return;  
       }  
       //解析获取property的值  
       Object val = parsePropertyValue(ele, bd, propertyName);  
       //根据property的名字和值创建property实例  
       PropertyValue pv = new PropertyValue(propertyName, val);  
       //解析<property>元素中的属性  
       parseMetaElements(ele, pv);  
       pv.setSource(extractSource(ele));  
       bd.getPropertyValues().addPropertyValue(pv);  
   }  
   finally {  
       this.parseState.pop();  
   }  
}  
//解析获取property值  
public Object parsePropertyValue(Element ele, BeanDefinition bd, String propertyName) {  
   String elementName = (propertyName != null) ?  
                   "<property> element for property '" + propertyName + "'" :  
                   "<constructor-arg> element";  
   //获取<property>的所有子元素，只能是其中一种类型:ref,value,list等  
   NodeList nl = ele.getChildNodes();  
   Element subElement = null;  
   for (int i = 0; i < nl.getLength(); i++) {  
       Node node = nl.item(i);  
       //子元素不是description和meta属性  
       if (node instanceof Element && !nodeNameEquals(node, DESCRIPTION_ELEMENT) &&  
               !nodeNameEquals(node, META_ELEMENT)) {  
           if (subElement != null) {  
               error(elementName + " must not contain more than one sub-element", ele);  
           }  
           else {//当前<property>元素包含有子元素  
               subElement = (Element) node;  
           }  
       }  
   }  
   //判断property的属性值是ref还是value，不允许既是ref又是value  
   boolean hasRefAttribute = ele.hasAttribute(REF_ATTRIBUTE);  
   boolean hasValueAttribute = ele.hasAttribute(VALUE_ATTRIBUTE);  
   if ((hasRefAttribute && hasValueAttribute) ||  
           ((hasRefAttribute || hasValueAttribute) && subElement != null)) {  
       error(elementName +  
               " is only allowed to contain either 'ref' attribute OR 'value' attribute OR sub-element", ele);  
   }  
   //如果属性是ref，创建一个ref的数据对象RuntimeBeanReference，这个对象  
   //封装了ref信息  
   if (hasRefAttribute) {  
       String refName = ele.getAttribute(REF_ATTRIBUTE);  
       if (!StringUtils.hasText(refName)) {  
           error(elementName + " contains empty 'ref' attribute", ele);  
       }  
       //一个指向运行时所依赖对象的引用  
       RuntimeBeanReference ref = new RuntimeBeanReference(refName);  
       //设置这个ref的数据对象是被当前的property对象所引用  
       ref.setSource(extractSource(ele));  
       return ref;  
   }  
    //如果属性是value，创建一个value的数据对象TypedStringValue，这个对象  
   //封装了value信息  
   else if (hasValueAttribute) {  
       //一个持有String类型值的对象  
       TypedStringValue valueHolder = new TypedStringValue(ele.getAttribute(VALUE_ATTRIBUTE));  
       //设置这个value数据对象是被当前的property对象所引用  
       valueHolder.setSource(extractSource(ele));  
       return valueHolder;  
   }  
   //如果当前<property>元素还有子元素  
   else if (subElement != null) {  
       //解析<property>的子元素  
       return parsePropertySubElement(subElement, bd);  
   }  
   else {  
       //propery属性中既不是ref，也不是value属性，解析出错返回null        error(elementName + " must specify a ref or value", ele);  
       return null;  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过对上述源码的分析，我们可以了解在Spring配置文件中，<Bean>元素中<property>元素的相关配置是如何处理的：

- ref：被封装为指向依赖对象一个引用。
- value：配置都会封装成一个字符串类型的对象。
- ref和value都通过“解析的数据类型属性值.setSource(extractSource(ele));”方法将属性值/引用与所引用的属性关联起来。

　　在方法的最后对于<property>元素的子元素通过parsePropertySubElement 方法解析。

### 　　14、解析<property>元素的子元素

　　在BeanDefinitionParserDelegate类中的parsePropertySubElement方法对<property>中的子元素解析，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//解析<property>元素中ref,value或者集合等子元素  
public Object parsePropertySubElement(Element ele, BeanDefinition bd, String defaultValueType) {  
   //如果<property>没有使用Spring默认的命名空间，则使用用户自定义的规则解析//内嵌元素  
   if (!isDefaultNamespace(ele)) {  
       return parseNestedCustomElement(ele, bd);  
   }  
   //如果子元素是bean，则使用解析<Bean>元素的方法解析  
   else if (nodeNameEquals(ele, BEAN_ELEMENT)) {  
       BeanDefinitionHolder nestedBd = parseBeanDefinitionElement(ele, bd);  
       if (nestedBd != null) {  
           nestedBd = decorateBeanDefinitionIfRequired(ele, nestedBd, bd);  
       }  
       return nestedBd;  
   }  
   //如果子元素是ref，ref中只能有以下3个属性：bean、local、parent  
   else if (nodeNameEquals(ele, REF_ELEMENT)) {  
       //获取<property>元素中的bean属性值，引用其他解析的Bean的名称  
       //可以不再同一个Spring配置文件中，具体请参考Spring对ref的配置规则  
       String refName = ele.getAttribute(BEAN_REF_ATTRIBUTE);  
       boolean toParent = false;  
       if (!StringUtils.hasLength(refName)) {  
            //获取<property>元素中的local属性值，引用同一个Xml文件中配置  
            //的Bean的id，local和ref不同，local只能引用同一个配置文件中的Bean  
           refName = ele.getAttribute(LOCAL_REF_ATTRIBUTE);  
           if (!StringUtils.hasLength(refName)) {  
               //获取<property>元素中parent属性值，引用父级容器中的Bean  
               refName = ele.getAttribute(PARENT_REF_ATTRIBUTE);  
               toParent = true;  
               if (!StringUtils.hasLength(refName)) {  
                   error("'bean', 'local' or 'parent' is required for <ref> element", ele);  
                   return null;  
               }  
           }  
       }  
       //没有配置ref的目标属性值  
       if (!StringUtils.hasText(refName)) {  
           error("<ref> element contains empty target attribute", ele);  
           return null;  
       }  
       //创建ref类型数据，指向被引用的对象  
       RuntimeBeanReference ref = new RuntimeBeanReference(refName, toParent);  
       //设置引用类型值是被当前子元素所引用  
       ref.setSource(extractSource(ele));  
       return ref;  
   }  
   //如果子元素是<idref>，使用解析ref元素的方法解析  
   else if (nodeNameEquals(ele, IDREF_ELEMENT)) {  
       return parseIdRefElement(ele);  
   }  
   //如果子元素是<value>，使用解析value元素的方法解析  
   else if (nodeNameEquals(ele, VALUE_ELEMENT)) {  
       return parseValueElement(ele, defaultValueType);  
   }  
   //如果子元素是null，为<property>设置一个封装null值的字符串数据  
   else if (nodeNameEquals(ele, NULL_ELEMENT)) {  
       TypedStringValue nullHolder = new TypedStringValue(null);  
       nullHolder.setSource(extractSource(ele));  
       return nullHolder;  
   }  
   //如果子元素是<array>，使用解析array集合子元素的方法解析  
   else if (nodeNameEquals(ele, ARRAY_ELEMENT)) {  
       return parseArrayElement(ele, bd);  
   }  
   //如果子元素是<list>，使用解析list集合子元素的方法解析  
   else if (nodeNameEquals(ele, LIST_ELEMENT)) {  
       return parseListElement(ele, bd);  
   }  
   //如果子元素是<set>，使用解析set集合子元素的方法解析  
   else if (nodeNameEquals(ele, SET_ELEMENT)) {  
       return parseSetElement(ele, bd);  
   }  
   //如果子元素是<map>，使用解析map集合子元素的方法解析  
   else if (nodeNameEquals(ele, MAP_ELEMENT)) {  
       return parseMapElement(ele, bd);  
   }  
   //如果子元素是<props>，使用解析props集合子元素的方法解析  
   else if (nodeNameEquals(ele, PROPS_ELEMENT)) {  
       return parsePropsElement(ele);  
   }  
   //既不是ref，又不是value，也不是集合，则子元素配置错误，返回null  
   else {  
       error("Unknown property sub-element: [" + ele.getNodeName() + "]", ele);  
       return null;  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过上述源码分析，我们明白了在Spring配置文件中，对<property>元素中配置的Array、List、Set、Map、Prop等各种集合子元素的都通过上述方法解析，生成对应的数据对象，比如ManagedList、ManagedArray、ManagedSet等，这些Managed类是Spring对象BeanDefiniton的数据封装，对集合数据类型的具体解析有各自的解析方法实现，解析方法的命名非常规范，一目了然，我们对<list>集合元素的解析方法进行源码分析，了解其实现过程。

### 　　15、解析<list>子元素

　　在BeanDefinitionParserDelegate类中的parseListElement方法就是具体实现解析<property>元素中的<list>集合子元素，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//解析<list>集合子元素  
public List parseListElement(Element collectionEle, BeanDefinition bd) {  
   //获取<list>元素中的value-type属性，即获取集合元素的数据类型  
   String defaultElementType = collectionEle.getAttribute(VALUE_TYPE_ATTRIBUTE);  
   //获取<list>集合元素中的所有子节点  
   NodeList nl = collectionEle.getChildNodes();  
   //Spring中将List封装为ManagedList  
   ManagedList<Object> target = new ManagedList<Object>(nl.getLength());  
   target.setSource(extractSource(collectionEle));  
   //设置集合目标数据类型  
   target.setElementTypeName(defaultElementType);  
   target.setMergeEnabled(parseMergeAttribute(collectionEle));  
   //具体的<list>元素解析  
   parseCollectionElements(nl, target, bd, defaultElementType);  
   return target;  
}   
//具体解析<list>集合元素，<array>、<list>和<set>都使用该方法解析  
protected void parseCollectionElements(  
       NodeList elementNodes, Collection<Object> target, BeanDefinition bd, String defaultElementType) {  
   //遍历集合所有节点  
   for (int i = 0; i < elementNodes.getLength(); i++) {  
       Node node = elementNodes.item(i);  
       //节点不是description节点  
       if (node instanceof Element && !nodeNameEquals(node, DESCRIPTION_ELEMENT)) {  
           //将解析的元素加入集合中，递归调用下一个子元素  
           target.add(parsePropertySubElement((Element) node, bd, defaultElementType));  
       }  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　经过对Spring Bean定义资源文件转换的Document对象中的元素层层解析，Spring IOC现在已经将XML形式定义的Bean定义资源文件转换为Spring IOC所识别的数据结构——BeanDefinition，它是Bean定义资源文件中配置的POJO对象在Spring IoC容器中的映射，我们可以通过AbstractBeanDefinition为入口，供IOC容器进行索引、查询和操作。

　　通过Spring IOC容器对Bean定义资源的解析后，IOC容器大致完成了管理Bean对象的准备工作，即初始化过程，但是最为重要的依赖注入还没有发生，现在在IOC容器中BeanDefinition存储的只是一些静态信息，接下来需要向容器注册Bean定义信息才能全部完成IOC容器的初始化过程。

### 　　16、解析过后的BeanDefinition在IOC容器中的注册

　　接下来会到我们第3步中分析DefaultBeanDefinitionDocumentReader对Bean定义转换的Document对象解析的流程中，在其parseDefaultElement方法中完成对Document对象的解析后得到封装BeanDefinition的BeanDefinitionHold对象，然后调用BeanDefinitionReaderUtils的registerBeanDefinition方法向IOC容器注册解析的Bean，BeanDefinitionReaderUtils的注册的源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//将解析的BeanDefinitionHold注册到容器中 
public static void registerBeanDefinition(BeanDefinitionHolder definitionHolder, BeanDefinitionRegistry registry)  
    throws BeanDefinitionStoreException {  
        //获取解析的BeanDefinition的名称
         String beanName = definitionHolder.getBeanName();  
        //向IoC容器注册BeanDefinition 
        registry.registerBeanDefinition(beanName, definitionHolder.getBeanDefinition());  
        //如果解析的BeanDefinition有别名，向容器为其注册别名  
         String[] aliases = definitionHolder.getAliases();  
        if (aliases != null) {  
            for (String aliase : aliases) {  
                registry.registerAlias(beanName, aliase);  
            }  
        }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　当调用BeanDefinitionReaderUtils向IOC容器注册解析的BeanDefinition时，真正完成注册功能的是DefaultListableBeanFactory。

### 　　17、DefaultListableBeanFactory向IOC容器注册解析后的BeanDefinition

　　DefaultListableBeanFactory中使用一个ConcurrentHashMap<String, BeanDefinition>的集合对象存放IoC容器中注册解析的BeanDefinition，向IOC容器注册的主要源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//存储注册的俄BeanDefinition  
private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<String, BeanDefinition>();  
//向IoC容器注册解析的BeanDefiniton  
public void registerBeanDefinition(String beanName, BeanDefinition beanDefinition)  
       throws BeanDefinitionStoreException {  
   Assert.hasText(beanName, "Bean name must not be empty");  
   Assert.notNull(beanDefinition, "BeanDefinition must not be null");  
   //校验解析的BeanDefiniton  
   if (beanDefinition instanceof AbstractBeanDefinition) {  
       try {  
           ((AbstractBeanDefinition) beanDefinition).validate();  
       }  
       catch (BeanDefinitionValidationException ex) {  
           throw new BeanDefinitionStoreException(beanDefinition.getResourceDescription(), beanName,  
                   "Validation of bean definition failed", ex);  
       }  
   }  
   //注册的过程中需要线程同步，以保证数据的一致性  
   synchronized (this.beanDefinitionMap) {  
       Object oldBeanDefinition = this.beanDefinitionMap.get(beanName);  
       //检查是否有同名的BeanDefinition已经在IoC容器中注册，如果已经注册，  
       //并且不允许覆盖已注册的Bean，则抛出注册失败异常  
       if (oldBeanDefinition != null) {  
           if (!this.allowBeanDefinitionOverriding) {  
               throw new BeanDefinitionStoreException(beanDefinition.getResourceDescription(), beanName,  
                       "Cannot register bean definition [" + beanDefinition + "] for bean '" + beanName +  
                       "': There is already [" + oldBeanDefinition + "] bound.");  
           }  
           else {//如果允许覆盖，则同名的Bean，后注册的覆盖先注册的  
               if (this.logger.isInfoEnabled()) {  
                   this.logger.info("Overriding bean definition for bean '" + beanName +  
                           "': replacing [" + oldBeanDefinition + "] with [" + beanDefinition + "]");  
               }  
           }  
       }  
       //IoC容器中没有已经注册同名的Bean，按正常注册流程注册  
       else {  
           this.beanDefinitionNames.add(beanName);  
           this.frozenBeanDefinitionNames = null;  
       }  
       this.beanDefinitionMap.put(beanName, beanDefinition);  
       //重置所有已经注册过的BeanDefinition的缓存  
       resetBeanDefinition(beanName);  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　至此，Bean定义资源文件中配置的Bean被解析过后，已经注册到IOC容器中，被容器管理起来，真正完成了IOC容器初始化所做的全部工作。现 在IOC容器中已经建立了整个Bean的配置信息，这些BeanDefinition信息已经可以使用，并且可以被检索，IOC容器的作用就是对这些注册的Bean定义信息进行处理和维护。这些的注册的Bean定义信息是IOC容器控制反转的基础，正是有了这些注册的数据，容器才可以进行依赖注入。

　　总结

- 初始化的入口在容器实现中的 **refresh**()调用来完成
- 对 bean 定义载入 IOC 容器使用的方法是 **loadBeanDefinition**,其中的大致过程如下：通过 ResourceLoader 来完成资源文件位置的定位，**DefaultResourceLoader** 是默认的实现，同时上下文本身就给出了 ResourceLoader 的实现，可以从**类路径，文件系统, URL** 等方式来定为资源位置。如果是 XmlBeanFactory作为 IOC 容器，那么需要为它指定 bean 定义的资源，也就是说 bean 定义文件时通过抽象成 Resource 来被 IOC 容器处理的，容器通过 **BeanDefinitionReader**来完成定义信息的解析和 Bean 信息的注册,往往使用的是**XmlBeanDefinitionReader** 来解析 bean 的 xml 定义文件 - 实际的处理过程是委托给 **BeanDefinitionParserDelegate** 来完成的，从而得到 bean 的定义信息，这些信息在 Spring 中使用 BeanDefinition 对象来表示 - 这个名字可以让我们想到loadBeanDefinition,RegisterBeanDefinition 这些相关的方法 - 他们都是为处理 BeanDefinitin 服务的， 容器解析得到 BeanDefinition 以后，需要把它在 IOC 容器中注册，这由 IOC 实现 **BeanDefinitionRegistry** 接口来实现。注册过程就是在 IOC 容器内部维护的一个ConcurrentHashMap 来保存得到的 BeanDefinition 的过程。这个 ConcurrentHashMap 是 IOC 容器持有 bean 信息的场所，以后对 bean 的操作都是围绕这个ConcurrentHashMap 来实现的.
- 然后我们就可以通过 BeanFactory 和 ApplicationContext 来享受到 Spring IOC 的服务了,在使用 IOC 容器的时候，我们注意到除了少量粘合代码，绝大多数以正确 IoC 风格编写的应用程序代码完全不用关心如何到达工厂，因为容器将把这些对象与容器管理的其他对象钩在一起。基本的策略是把工厂放到已知的地方，最好是放在对预期使用的上下文有意义的地方，以及代码将实际需要访问工厂的地方。 Spring 本身提供了对声明式载入 web 应用程序用法的应用程序上下文,并将其存储在ServletContext 中的框架实现。具体可以参见以后的文章 

　　在使用 Spring IOC 容器的时候我们还需要区别两个概念：

​    Beanfactory 和 Factory bean，其中 BeanFactory 指的是 IOC 容器的编程抽象，比如 ApplicationContext， XmlBeanFactory 等，这些都是 IOC 容器的具体表现，需要使用什么样的容器由客户决定,但 Spring 为我们提供了丰富的选择。 FactoryBean 只是一个可以在 IOC容器中被管理的一个 bean，是对各种处理过程和资源使用的抽象，Factory bean 在需要时产生另一个对象，而不返回 FactoryBean本身,我们可以把它看成是一个抽象工厂，对它的调用返回的是工厂生产的产品。所有的 Factory bean 都实现特殊的org.springframework.beans.factory.FactoryBean 接口，当使用容器中 factory bean 的时候，该容器不会返回 factory bean 本身,而是返回其生成的对象。Spring 包括了大部分的通用资源和服务访问抽象的 Factory bean 的实现，其中包括：对 JNDI 查询的处理、对代理对象的处理、对事务性代理的处理、对 RMI 代理的处理等，这些我们都可以看成是具体的工厂，看成是Spring 为我们建立好的工厂。也就是说 Spring 通过使用抽象工厂模式为我们准备了一系列工厂来生产一些特定的对象,免除我们手工重复的工作，我们要使用时只需要在 IOC 容器里配置好就能很方便的使用了。

## 五、IOC容器的依赖注入

### 　　1、依赖注入发生的时间

　　当Spring IoC容器完成了Bean定义资源的定位、载入、解析和注册以后，IOC容器中已经管理类Bean定义的相关数据，但是此时IoC容器还没有对所管理的Bean进行依赖注入，依赖注入在以下两种情况发生：

- 用户第一次通过getBean方法向IOC容索要Bean时，IOC容器触发依赖注入。
- 当用户在Bean定义资源中为<Bean>元素配置了lazy-init属性，即让容器在解析注册Bean定义时进行预实例化，触发依赖注入。

　　BeanFactory接口定义了Spring IOC容器的基本功能规范，是Spring IOC容器所应遵守的最底层和最基本的编程规范。BeanFactory接口中定义了几个getBean方法，就是用户向IOC容器索取管理的Bean的方法，我们通过分析其子类的具体实现，理解Spring IOC容器在用户索取Bean时如何完成依赖注入。

　　在BeanFactory中我们看到getBean（String…）函数，它的具体实现在AbstractBeanFactory中

### 　　2、AbstractBeanFactory通过getBean向IOC容器获取被管理的Bean

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//获取IoC容器中指定名称的Bean  
public Object getBean(String name) throws BeansException {  
   //doGetBean才是真正向IoC容器获取被管理Bean的过程  
   return doGetBean(name, null, null, false);  
}  
//获取IoC容器中指定名称和类型的Bean  
public <T> T getBean(String name, Class<T> requiredType) throws BeansException {  
   //doGetBean才是真正向IoC容器获取被管理Bean的过程  
   return doGetBean(name, requiredType, null, false);  
}  
//获取IoC容器中指定名称和参数的Bean  
public Object getBean(String name, Object... args) throws BeansException {  
   //doGetBean才是真正向IoC容器获取被管理Bean的过程  
   return doGetBean(name, null, args, false);  
}  
//获取IoC容器中指定名称、类型和参数的Bean  
public <T> T getBean(String name, Class<T> requiredType, Object... args) throws BeansException {  
//doGetBean才是真正向IoC容器获取被管理Bean的过程  
   return doGetBean(name, requiredType, args, false);  
}  
//真正实现向IoC容器获取Bean的功能，也是触发依赖注入功能的地方  
@SuppressWarnings("unchecked")  
protected <T> T doGetBean(  
       final String name, final Class<T> requiredType, final Object[] args, boolean typeCheckOnly)  
       throws BeansException {  
   //根据指定的名称获取被管理Bean的名称，剥离指定名称中对容器的相关依赖，如果指定的是别名，将别名转换为规范的Bean名称  
   final String beanName = transformedBeanName(name);  
   Object bean;  
   //先从缓存中取是否已经有被创建过的单态类型的Bean，对于单态模式的Bean整个IoC容器中只创建一次，不需要重复创建  
   Object sharedInstance = getSingleton(beanName);  
   //IOC容器创建单态模式Bean实例对象  
   if (sharedInstance != null && args == null) {  
       if (logger.isDebugEnabled()) {  
           //如果指定名称的Bean在容器中已有单态模式的Bean被创建，直接返回已经创建的Bean  
           if (isSingletonCurrentlyInCreation(beanName)) {  
               logger.debug("Returning eagerly cached instance of singleton bean '" + beanName +  
                       "' that is not fully initialized yet - a consequence of a circular reference");  
           }  
           else {  
               logger.debug("Returning cached instance of singleton bean '" + beanName + "'");  
           }  
       }  
       //获取给定Bean的实例对象，主要是完成FactoryBean的相关处理  
       //注意：BeanFactory是管理容器中Bean的工厂，而FactoryBean是创建创建对象的工厂Bean，两者之间有区别  
       bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);  
   }  
   else {　　　　//缓存没有正在创建的单态模式Bean  
       //缓存中已经有已经创建的原型模式Bean，但是由于循环引用的问题导致实例化对象失败  
       if (isPrototypeCurrentlyInCreation(beanName)) {  
           throw new BeanCurrentlyInCreationException(beanName);  
       }  
       //对IoC容器中是否存在指定名称的BeanDefinition进行检查，首先检查是否能在当前的BeanFactory中获取的所需要的Bean　　　　//如果不能则委托当前容器的父级容器去查找，如果还是找不到则沿着容器的继承体系向父级容器查找  
       BeanFactory parentBeanFactory = getParentBeanFactory();  
       //当前容器的父级容器存在，且当前容器中不存在指定名称的Bean  
       if (parentBeanFactory != null && !containsBeanDefinition(beanName)) {  
           //解析指定Bean名称的原始名称  
           String nameToLookup = originalBeanName(name);  
           if (args != null) {  
               //委派父级容器根据指定名称和显式的参数查找  
               return (T) parentBeanFactory.getBean(nameToLookup, args);  
           }  
           else {  
               //委派父级容器根据指定名称和类型查找  
               return parentBeanFactory.getBean(nameToLookup, requiredType);  
           }  
       }  
       //创建的Bean是否需要进行类型验证，一般不需要  
       if (!typeCheckOnly) {  
           //向容器标记指定的Bean已经被创建  
           markBeanAsCreated(beanName);  
       }  
        //根据指定Bean名称获取其父级的Bean定义，主要解决Bean继承时子类合并父类公共属性问题  
       final RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);  
       checkMergedBeanDefinition(mbd, beanName, args);  
       //获取当前Bean所有依赖Bean的名称  
       String[] dependsOn = mbd.getDependsOn();  
       //如果当前Bean有依赖Bean  
       if (dependsOn != null) {  
           for (String dependsOnBean : dependsOn) {  
               //递归调用getBean方法，获取当前Bean的依赖Bean  
               getBean(dependsOnBean);  
               //把被依赖Bean注册给当前依赖的Bean  
               registerDependentBean(dependsOnBean, beanName);  
           }  
       }  
       //创建单态模式Bean的实例对象  
       if (mbd.isSingleton()) {  
       //这里使用了一个匿名内部类，创建Bean实例对象，并且注册给所依赖的对象  
           sharedInstance = getSingleton(beanName, new ObjectFactory() {  
               public Object getObject() throws BeansException {  
                   try {  
                       //创建一个指定Bean实例对象，如果有父级继承，则合并子//类和父类的定义  
                       return createBean(beanName, mbd, args);  
                   }  
                   catch (BeansException ex) {  
                       //显式地从容器单态模式Bean缓存中清除实例对象  
                       destroySingleton(beanName);  
                       throw ex;  
                   }  
               }  
           });  
           //获取给定Bean的实例对象  
           bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);  
       }  
       //IoC容器创建原型模式Bean实例对象  
       else if (mbd.isPrototype()) {  
           //原型模式(Prototype)是每次都会创建一个新的对象  
           Object prototypeInstance = null;  
           try {  
               //回调beforePrototypeCreation方法，默认的功能是注册当前创//建的原型对象  
               beforePrototypeCreation(beanName);  
               //创建指定Bean对象实例  
               prototypeInstance = createBean(beanName, mbd, args);  
           }  
           finally {  
               //回调afterPrototypeCreation方法，默认的功能告诉IoC容器指//定Bean的原型对象不再创建了  
               afterPrototypeCreation(beanName);  
           }  
           //获取给定Bean的实例对象  
           bean = getObjectForBeanInstance(prototypeInstance, name, beanName, mbd);  
       }  
       //要创建的Bean既不是单态模式，也不是原型模式，则根据Bean定义资源中配置的生命周期范围，选择实例化Bean的合适方法，这种在Web应用程序中比较常用
       //如：request、session、application等生命周期  
       else {  
           String scopeName = mbd.getScope();  
           final Scope scope = this.scopes.get(scopeName);  
           //Bean定义资源中没有配置生命周期范围，则Bean定义不合法  
           if (scope == null) {  
               throw new IllegalStateException("No Scope registered for scope '" + scopeName + "'");  
           }  
           try {  
               //这里又使用了一个匿名内部类，获取一个指定生命周期范围的实例  
               Object scopedInstance = scope.get(beanName, new ObjectFactory() {  
                   public Object getObject() throws BeansException {  
                       beforePrototypeCreation(beanName);  
                       try {  
                           return createBean(beanName, mbd, args);  
                       }  
                       finally {  
                           afterPrototypeCreation(beanName);  
                       }  
                   }  
               });  
               //获取给定Bean的实例对象  
               bean = getObjectForBeanInstance(scopedInstance, name, beanName, mbd);  
           }  
           catch (IllegalStateException ex) {  
               throw new BeanCreationException(beanName,  
                       "Scope '" + scopeName + "' is not active for the current thread; " +  
                       "consider defining a scoped proxy for this bean if you intend to refer to it from a singleton",  
                       ex);  
           }  
       }  
   }  
   //对创建的Bean实例对象进行类型检查  
   if (requiredType != null && bean != null && !requiredType.isAssignableFrom(bean.getClass())) {  
       throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());  
   }  
   return (T) bean;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过上面对向IoC容器获取Bean方法的分析，我们可以看到在Spring中，如果Bean定义的单例模式(Singleton)，则容器在创建之前先从缓存中查找，以确保整个容器中只存在一个实例对象。如果Bean定义的是原型模式(Prototype)，则容器每次都会创建一个新的实例对象。除此之外，Bean定义还可以扩展为指定其生命周期范围。

　　上面的源码只是定义了根据Bean定义的模式，采取的不同创建Bean实例对象的策略，具体的Bean实例对象的创建过程由实现了ObejctFactory接口的匿名内部类的createBean方法完成，ObejctFactory使用委派模式，**具体的Bean实例创建过程交由其实现类AbstractAutowireCapableBeanFactory完成**，我们继续分析AbstractAutowireCapableBeanFactory的createBean方法的源码，理解其创建Bean实例的具体实现过程。

### 　　3、AbstractAutowireCapableBeanFactory创建Bean实例对象

　　AbstractAutowireCapableBeanFactory类实现了ObejctFactory接口，创建容器指定的Bean实例对象，同时还对创建的Bean实例对象进行初始化处理。其创建Bean实例对象的方法源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//创建Bean实例对象  
protected Object createBean(final String beanName, final RootBeanDefinition mbd, final Object[] args)  
       throws BeanCreationException {  
   if (logger.isDebugEnabled()) {  
       logger.debug("Creating instance of bean '" + beanName + "'");  
   }  
   //判断需要创建的Bean是否可以实例化，即是否可以通过当前的类加载器加载  
   resolveBeanClass(mbd, beanName);  
   //校验和准备Bean中的方法覆盖  
   try {  
       mbd.prepareMethodOverrides();  
   }  
   catch (BeanDefinitionValidationException ex) {  
       throw new BeanDefinitionStoreException(mbd.getResourceDescription(),  
               beanName, "Validation of method overrides failed", ex);  
   }  
   try {  
       //如果Bean配置了初始化前和初始化后的处理器，则试图返回一个需要创建//Bean的代理对象  
       Object bean = resolveBeforeInstantiation(beanName, mbd);  
       if (bean != null) {  
           return bean;  
       }  
   }  
   catch (Throwable ex) {  
       throw new BeanCreationException(mbd.getResourceDescription(), beanName,  
               "BeanPostProcessor before instantiation of bean failed", ex);  
   }  
   //创建Bean的入口  
   Object beanInstance = doCreateBean(beanName, mbd, args);  
   if (logger.isDebugEnabled()) {  
       logger.debug("Finished creating instance of bean '" + beanName + "'");  
   }  
   return beanInstance;  
}  
//真正创建Bean的方法  
protected Object doCreateBean(final String beanName, final RootBeanDefinition mbd, final Object[] args) {  
   //封装被创建的Bean对象  
   BeanWrapper instanceWrapper = null;  
   if (mbd.isSingleton()){//单态模式的Bean，先从容器中缓存中获取同名Bean  
       instanceWrapper = this.factoryBeanInstanceCache.remove(beanName);  
   }  
   if (instanceWrapper == null) {  
       //创建实例对象  
       instanceWrapper = createBeanInstance(beanName, mbd, args);  
   }  
   final Object bean = (instanceWrapper != null ? instanceWrapper.getWrappedInstance() : null);  
   //获取实例化对象的类型  
   Class beanType = (instanceWrapper != null ? instanceWrapper.getWrappedClass() : null);  
   //调用PostProcessor后置处理器  
   synchronized (mbd.postProcessingLock) {  
       if (!mbd.postProcessed) {  
           applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);  
           mbd.postProcessed = true;  
       }  
   }  
   // Eagerly cache singletons to be able to resolve circular references  
   //向容器中缓存单态模式的Bean对象，以防循环引用  
   boolean earlySingletonExposure = (mbd.isSingleton() && this.allowCircularReferences &&  
           isSingletonCurrentlyInCreation(beanName));  
   if (earlySingletonExposure) {  
       if (logger.isDebugEnabled()) {  
           logger.debug("Eagerly caching bean '" + beanName +  
                   "' to allow for resolving potential circular references");  
       }  
       //这里是一个匿名内部类，为了防止循环引用，尽早持有对象的引用  
       addSingletonFactory(beanName, new ObjectFactory() {  
           public Object getObject() throws BeansException {  
               return getEarlyBeanReference(beanName, mbd, bean);  
           }  
       });  
   }  
   //Bean对象的初始化，依赖注入在此触发  
   //这个exposedObject在初始化完成之后返回作为依赖注入完成后的Bean  
   Object exposedObject = bean;  
   try {  
       //将Bean实例对象封装，并且Bean定义中配置的属性值赋值给实例对象  
       populateBean(beanName, mbd, instanceWrapper);  
       if (exposedObject != null) {  
           //初始化Bean对象  
           exposedObject = initializeBean(beanName, exposedObject, mbd);  
       }  
   }  
   catch (Throwable ex) {  
       if (ex instanceof BeanCreationException && beanName.equals(((BeanCreationException) ex).getBeanName())) {  
           throw (BeanCreationException) ex;  
       }  
       else {  
           throw new BeanCreationException(mbd.getResourceDescription(), beanName, "Initialization of bean failed", ex);  
       }  
   }  
   if (earlySingletonExposure) {  
       //获取指定名称的已注册的单态模式Bean对象  
       Object earlySingletonReference = getSingleton(beanName, false);  
       if (earlySingletonReference != null) {  
           //根据名称获取的以注册的Bean和正在实例化的Bean是同一个  
           if (exposedObject == bean) {  
               //当前实例化的Bean初始化完成  
               exposedObject = earlySingletonReference;  
           }  
           //当前Bean依赖其他Bean，并且当发生循环引用时不允许新创建实例对象  
           else if (!this.allowRawInjectionDespiteWrapping && hasDependentBean(beanName)) {  
               String[] dependentBeans = getDependentBeans(beanName);  
               Set<String> actualDependentBeans = new LinkedHashSet<String>(dependentBeans.length);  
               //获取当前Bean所依赖的其他Bean  
               for (String dependentBean : dependentBeans) {  
                   //对依赖Bean进行类型检查  
                   if (!removeSingletonIfCreatedForTypeCheckOnly(dependentBean)) {  
                       actualDependentBeans.add(dependentBean);  
                   }  
               }  
               if (!actualDependentBeans.isEmpty()) {  
                   throw new BeanCurrentlyInCreationException(beanName,  
                           "Bean with name '" + beanName + "' has been injected into other beans [" +  
                           StringUtils.collectionToCommaDelimitedString(actualDependentBeans) +  
                           "] in its raw version as part of a circular reference, but has eventually been " +  
                           "wrapped. This means that said other beans do not use the final version of the " +  
                           "bean. This is often the result of over-eager type matching - consider using " +  
                           "'getBeanNamesOfType' with the 'allowEagerInit' flag turned off, for example.");  
               }  
           }  
       }  
   }  
   //注册完成依赖注入的Bean  
   try {  
       registerDisposableBeanIfNecessary(beanName, bean, mbd);  
   }  
   catch (BeanDefinitionValidationException ex) {  
       throw new BeanCreationException(mbd.getResourceDescription(), beanName, "Invalid destruction signature", ex);  
   }  
   return exposedObject;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过对方法源码的分析，我们看到具体的依赖注入实现在以下两个方法中：

- createBeanInstance：生成Bean所包含的java对象实例。
- populateBean ：对Bean属性的依赖注入进行处理。

　　下面继续分析这两个方法的代码实现。

### 　　4、createBeanInstance方法创建Bean的java实例对象

　　在createBeanInstance方法中，根据指定的初始化策略，使用静态工厂、工厂方法或者容器的自动装配特性生成java实例对象，创建对象的源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//创建Bean的实例对象  
protected BeanWrapper createBeanInstance(String beanName, RootBeanDefinition mbd, Object[] args) {  
   //检查确认Bean是可实例化的  
   Class beanClass = resolveBeanClass(mbd, beanName);  
   //使用工厂方法对Bean进行实例化  
   if (beanClass != null && !Modifier.isPublic(beanClass.getModifiers()) && !mbd.isNonPublicAccessAllowed()) {  
       throw new BeanCreationException(mbd.getResourceDescription(), beanName,  
               "Bean class isn't public, and non-public access not allowed: " + beanClass.getName());  
   }  
   if (mbd.getFactoryMethodName() != null)  {  
       //调用工厂方法实例化  
       return instantiateUsingFactoryMethod(beanName, mbd, args);  
   }  
   //使用容器的自动装配方法进行实例化  
   boolean resolved = false;  
   boolean autowireNecessary = false;  
   if (args == null) {  
       synchronized (mbd.constructorArgumentLock) {  
           if (mbd.resolvedConstructorOrFactoryMethod != null) {  
               resolved = true;  
               autowireNecessary = mbd.constructorArgumentsResolved;  
           }  
       }  
   }  
   if (resolved) {  
       if (autowireNecessary) {  
           //配置了自动装配属性，使用容器的自动装配实例化，容器的自动装配是根据参数类型匹配Bean的构造方法  
           return autowireConstructor(beanName, mbd, null, null);  
       }  
       else {  
           //使用默认的无参构造方法实例化  
           return instantiateBean(beanName, mbd);  
       }  
   }  
   //使用Bean的构造方法进行实例化  
   Constructor[] ctors = determineConstructorsFromBeanPostProcessors(beanClass, beanName);  
   if (ctors != null ||  
           mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_CONSTRUCTOR ||  
           mbd.hasConstructorArgumentValues() || !ObjectUtils.isEmpty(args))  {  
       //使用容器的自动装配特性，调用匹配的构造方法实例化  
       return autowireConstructor(beanName, mbd, ctors, args);  
   }  
   //使用默认的无参构造方法实例化  
   return instantiateBean(beanName, mbd);  
}   
//使用默认的无参构造方法实例化Bean对象  
protected BeanWrapper instantiateBean(final String beanName, final RootBeanDefinition mbd) {  
   try {  
       Object beanInstance;  
       final BeanFactory parent = this;  
       //获取系统的安全管理接口，JDK标准的安全管理API  
       if (System.getSecurityManager() != null) {  
           //这里是一个匿名内置类，根据实例化策略创建实例对象  
           beanInstance = AccessController.doPrivileged(new PrivilegedAction<Object>() {  
               public Object run() {  
                   return getInstantiationStrategy().instantiate(mbd, beanName, parent);  
               }  
           }, getAccessControlContext());  
       }  
       else {  
           //将实例化的对象封装起来  
           beanInstance = getInstantiationStrategy().instantiate(mbd, beanName, parent);  
       }  
       BeanWrapper bw = new BeanWrapperImpl(beanInstance);  
       initBeanWrapper(bw);  
       return bw;  
   }  
   catch (Throwable ex) {  
       throw new BeanCreationException(mbd.getResourceDescription(), beanName, "Instantiation of bean failed", ex);  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　经过对上面的代码分析，我们可以看出，对使用工厂方法和自动装配特性的Bean的实例化相当比较清楚，调用相应的工厂方法或者参数匹配的构造方法即可完成实例化对象的工作，但是对于我们最常使用的默认无参构造方法就需要使用相应的初始化策略(JDK的反射机制或者CGLIB)来进行初始化了，在方法getInstantiationStrategy().instantiate中就具体实现类使用初始策略实例化对象。

### 　　5、SimpleInstantiationStrategy类使用默认的无参构造方法创建Bean实例化对象

　　在使用默认的无参构造方法创建Bean的实例化对象时，方法getInstantiationStrategy().instantiate调用了SimpleInstantiationStrategy类中的实例化Bean的方法，其源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//使用初始化策略实例化Bean对象  
public Object instantiate(RootBeanDefinition beanDefinition, String beanName, BeanFactory owner) {  
   //如果Bean定义中没有方法覆盖，则就不需要CGLIB父类类的方法  
   if (beanDefinition.getMethodOverrides().isEmpty()) {  
       Constructor<?> constructorToUse;  
       synchronized (beanDefinition.constructorArgumentLock) {  
           //获取对象的构造方法或工厂方法  
           constructorToUse = (Constructor<?>) beanDefinition.resolvedConstructorOrFactoryMethod;  
           //如果没有构造方法且没有工厂方法  
           if (constructorToUse == null) {  
               //使用JDK的反射机制，判断要实例化的Bean是否是接口  
               final Class clazz = beanDefinition.getBeanClass();  
               if (clazz.isInterface()) {  
                   throw new BeanInstantiationException(clazz, "Specified class is an interface");  
               }  
               try {  
                   if (System.getSecurityManager() != null) {  
                   //这里是一个匿名内置类，使用反射机制获取Bean的构造方法  
                       constructorToUse = AccessController.doPrivileged(new PrivilegedExceptionAction<Constructor>() {  
                           public Constructor run() throws Exception {  
                               return clazz.getDeclaredConstructor((Class[]) null);  
                           }  
                       });  
                   }  
                   else {  
                       constructorToUse =  clazz.getDeclaredConstructor((Class[]) null);  
                   }  
                   beanDefinition.resolvedConstructorOrFactoryMethod = constructorToUse;  
               }  
               catch (Exception ex) {  
                   throw new BeanInstantiationException(clazz, "No default constructor found", ex);  
               }  
           }  
       }  
       //使用BeanUtils实例化，通过反射机制调用”构造方法.newInstance(arg)”来进行实例化  
       return BeanUtils.instantiateClass(constructorToUse);  
   }  
   else {  
       //使用CGLIB来实例化对象  
       return instantiateWithMethodInjection(beanDefinition, beanName, owner);  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过上面的代码分析，我们看到了如果Bean有方法被覆盖了，则使用JDK的反射机制进行实例化，否则，使用CGLIB进行实例化。instantiateWithMethodInjection方法调用SimpleInstantiationStrategy的子类CglibSubclassingInstantiationStrategy使用CGLIB来进行初始化，其源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//使用CGLIB进行Bean对象实例化  
public Object instantiate(Constructor ctor, Object[] args) {  
       //CGLIB中的类  
       Enhancer enhancer = new Enhancer();  
       //将Bean本身作为其基类  
       enhancer.setSuperclass(this.beanDefinition.getBeanClass());  
       enhancer.setCallbackFilter(new CallbackFilterImpl());  
       enhancer.setCallbacks(new Callback[] {  
               NoOp.INSTANCE,  
               new LookupOverrideMethodInterceptor(),  
               new ReplaceOverrideMethodInterceptor()  
       });  
       //使用CGLIB的create方法生成实例对象  
       return (ctor == null) ?   
               enhancer.create() :   
               enhancer.create(ctor.getParameterTypes(), args);  
   }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　CGLIB是一个常用的字节码生成器的类库，它提供了一系列API实现java字节码的生成和转换功能。我们在学习JDK的动态代理时都知道，JDK的动态代理只能针对接口，如果一个类没有实现任何接口，要对其进行动态代理只能使用CGLIB。

### 　　6、populateBean方法对Bean属性的依赖注入

　　在第3步的分析中我们已经了解到Bean的依赖注入分为以下两个过程：

- createBeanInstance：生成Bean所包含的java对象实例。
- populateBean ：对Bean属性的依赖注入进行处理。

　　第4、5步中我们已经分析了容器初始化生成Bean所包含的Java实例对象的过程，现在我们继续分析生成对象后，Spring IOC容器是如何将Bean的属性依赖关系注入Bean实例对象中并设置好的，属性依赖注入的代码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//将Bean属性设置到生成的实例对象上  
protected void populateBean(String beanName, AbstractBeanDefinition mbd, BeanWrapper bw) {  
   //获取容器在解析Bean定义资源时为BeanDefiniton中设置的属性值  
   PropertyValues pvs = mbd.getPropertyValues();  
   //实例对象为null  
   if (bw == null) {  
       //属性值不为空  
       if (!pvs.isEmpty()) {  
           throw new BeanCreationException(  
                   mbd.getResourceDescription(), beanName, "Cannot apply property values to null instance");  
       }  
       else {  
           //实例对象为null，属性值也为空，不需要设置属性值，直接返回  
           return;  
       }  
   }  
   //在设置属性之前调用Bean的PostProcessor后置处理器  
   boolean continueWithPropertyPopulation = true;  
   if (!mbd.isSynthetic() && hasInstantiationAwareBeanPostProcessors()) {  
       for (BeanPostProcessor bp : getBeanPostProcessors()) {  
           if (bp instanceof InstantiationAwareBeanPostProcessor) {  
               InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;  
               if (!ibp.postProcessAfterInstantiation(bw.getWrappedInstance(), beanName)) {  
                   continueWithPropertyPopulation = false;  
                   break;  
               }  
           }  
       }  
   }  
   if (!continueWithPropertyPopulation) {  
       return;  
   }  
   //依赖注入开始，首先处理autowire自动装配的注入  
   if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_NAME ||  
           mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_TYPE) {  
       MutablePropertyValues newPvs = new MutablePropertyValues(pvs);  
       //对autowire自动装配的处理，根据Bean名称自动装配注入  
       if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_NAME) {  
           autowireByName(beanName, mbd, bw, newPvs);  
       }  
       //根据Bean类型自动装配注入  
       if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_TYPE) {  
           autowireByType(beanName, mbd, bw, newPvs);  
       }  
       pvs = newPvs;  
   }  
   //检查容器是否持有用于处理单态模式Bean关闭时的后置处理器  
   boolean hasInstAwareBpps = hasInstantiationAwareBeanPostProcessors();  
   //Bean实例对象没有依赖，即没有继承基类  
   boolean needsDepCheck = (mbd.getDependencyCheck() != RootBeanDefinition.DEPENDENCY_CHECK_NONE);  
   if (hasInstAwareBpps || needsDepCheck) {  
       //从实例对象中提取属性描述符  
       PropertyDescriptor[] filteredPds = filterPropertyDescriptorsForDependencyCheck(bw);  
       if (hasInstAwareBpps) {  
           for (BeanPostProcessor bp : getBeanPostProcessors()) {  
               if (bp instanceof InstantiationAwareBeanPostProcessor) {  
                   InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;  
                   //使用BeanPostProcessor处理器处理属性值  
                   pvs = ibp.postProcessPropertyValues(pvs, filteredPds, bw.getWrappedInstance(), beanName);  
                   if (pvs == null) {  
                       return;  
                   }  
               }  
           }  
       }  
       if (needsDepCheck) {  
           //为要设置的属性进行依赖检查  
           checkDependencies(beanName, mbd, filteredPds, pvs);  
       }  
   }  
   //对属性进行注入  
   applyPropertyValues(beanName, mbd, bw, pvs);  
}  
//解析并注入依赖属性的过程  
protected void applyPropertyValues(String beanName, BeanDefinition mbd, BeanWrapper bw, PropertyValues pvs) {  
   if (pvs == null || pvs.isEmpty()) {  
       return;  
   }  
   //封装属性值  
   MutablePropertyValues mpvs = null;  
   List<PropertyValue> original;  
   if (System.getSecurityManager()!= null) {  
       if (bw instanceof BeanWrapperImpl) {  
           //设置安全上下文，JDK安全机制  
           ((BeanWrapperImpl) bw).setSecurityContext(getAccessControlContext());  
       }  
   }  
   if (pvs instanceof MutablePropertyValues) {  
       mpvs = (MutablePropertyValues) pvs;  
       //属性值已经转换  
       if (mpvs.isConverted()) {  
           try {  
               //为实例化对象设置属性值  
               bw.setPropertyValues(mpvs);  
               return;  
           }  
           catch (BeansException ex) {  
               throw new BeanCreationException(  
                       mbd.getResourceDescription(), beanName, "Error setting property values", ex);  
           }  
       }  
       //获取属性值对象的原始类型值  
       original = mpvs.getPropertyValueList();  
   }  
   else {  
       original = Arrays.asList(pvs.getPropertyValues());  
   }  
   //获取用户自定义的类型转换  
   TypeConverter converter = getCustomTypeConverter();  
   if (converter == null) {  
       converter = bw;  
   }  
   //创建一个Bean定义属性值解析器，将Bean定义中的属性值解析为Bean实例对象  
   //的实际值  
   BeanDefinitionValueResolver valueResolver = new BeanDefinitionValueResolver(this, beanName, mbd, converter);  
   //为属性的解析值创建一个拷贝，将拷贝的数据注入到实例对象中  
   List<PropertyValue> deepCopy = new ArrayList<PropertyValue>(original.size());  
   boolean resolveNecessary = false;  
   for (PropertyValue pv : original) {  
       //属性值不需要转换  
       if (pv.isConverted()) {  
           deepCopy.add(pv);  
       }  
       //属性值需要转换  
       else {  
           String propertyName = pv.getName();  
           //原始的属性值，即转换之前的属性值  
           Object originalValue = pv.getValue();  
           //转换属性值，例如将引用转换为IoC容器中实例化对象引用  
           Object resolvedValue = valueResolver.resolveValueIfNecessary(pv, originalValue);  
           //转换之后的属性值  
           Object convertedValue = resolvedValue;  
           //属性值是否可以转换  
           boolean convertible = bw.isWritableProperty(propertyName) &&  
                   !PropertyAccessorUtils.isNestedOrIndexedProperty(propertyName);  
           if (convertible) {  
               //使用用户自定义的类型转换器转换属性值  
               convertedValue = convertForProperty(resolvedValue, propertyName, bw, converter);  
           }  
           //存储转换后的属性值，避免每次属性注入时的转换工作  
           if (resolvedValue == originalValue) {  
               if (convertible) {  
                   //设置属性转换之后的值  
                   pv.setConvertedValue(convertedValue);  
               }  
               deepCopy.add(pv);  
           }  
           //属性是可转换的，且属性原始值是字符串类型，且属性的原始类型值不是  
           //动态生成的字符串，且属性的原始值不是集合或者数组类型  
           else if (convertible && originalValue instanceof TypedStringValue &&  
                   !((TypedStringValue) originalValue).isDynamic() &&  
                   !(convertedValue instanceof Collection || ObjectUtils.isArray(convertedValue))) {  
               pv.setConvertedValue(convertedValue);  
               deepCopy.add(pv);  
           }  
           else {  
               resolveNecessary = true;  
               //重新封装属性的值  
               deepCopy.add(new PropertyValue(pv, convertedValue));  
           }  
       }  
   }  
   if (mpvs != null && !resolveNecessary) {  
       //标记属性值已经转换过  
       mpvs.setConverted();  
   }  
   //进行属性依赖注入  
   try {  
       bw.setPropertyValues(new MutablePropertyValues(deepCopy));  
   }  
   catch (BeansException ex) {  
       throw new BeanCreationException(  
               mbd.getResourceDescription(), beanName, "Error setting property values", ex);  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　分析上述代码，我们可以看出，对属性的注入过程分以下两种情况：

- 属性值类型不需要转换时，不需要解析属性值，直接准备进行依赖注入。
- 属性值需要进行类型转换时，如对其他对象的引用等，首先需要解析属性值，然后对解析后的属性值进行依赖注入。

　　对属性值的解析是在BeanDefinitionValueResolver类中的resolveValueIfNecessary方法中进行的，对属性值的依赖注入是通过bw.setPropertyValues方法实现的，在分析属性值的依赖注入之前，我们先分析一下对属性值的解析过程。

### 　　7、BeanDefinitionValueResolver解析属性值

　　当容器在对属性进行依赖注入时，如果发现属性值需要进行类型转换，如属性值是容器中另一个Bean实例对象的引用，则容器首先需要根据属性值解析出所引用的对象，然后才能将该引用对象注入到目标实例对象的属性上去，对属性进行解析的由resolveValueIfNecessary方法实现，其源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//解析属性值，对注入类型进行转换  
public Object resolveValueIfNecessary(Object argName, Object value) {  
   //对引用类型的属性进行解析  
   if (value instanceof RuntimeBeanReference) {  
       RuntimeBeanReference ref = (RuntimeBeanReference) value;  
       //调用引用类型属性的解析方法  
       return resolveReference(argName, ref);  
   }  
   //对属性值是引用容器中另一个Bean名称的解析  
   else if (value instanceof RuntimeBeanNameReference) {  
       String refName = ((RuntimeBeanNameReference) value).getBeanName();  
       refName = String.valueOf(evaluate(refName));  
       //从容器中获取指定名称的Bean  
       if (!this.beanFactory.containsBean(refName)) {  
           throw new BeanDefinitionStoreException(  
                   "Invalid bean name '" + refName + "' in bean reference for " + argName);  
       }  
       return refName;  
   }  
   //对Bean类型属性的解析，主要是Bean中的内部类  
   else if (value instanceof BeanDefinitionHolder) {  
       BeanDefinitionHolder bdHolder = (BeanDefinitionHolder) value;  
       return resolveInnerBean(argName, bdHolder.getBeanName(), bdHolder.getBeanDefinition());  
   }  
   else if (value instanceof BeanDefinition) {  
       BeanDefinition bd = (BeanDefinition) value;  
       return resolveInnerBean(argName, "(inner bean)", bd);  
   }  
   //对集合数组类型的属性解析  
   else if (value instanceof ManagedArray) {  
       ManagedArray array = (ManagedArray) value;  
       //获取数组的类型  
       Class elementType = array.resolvedElementType;  
       if (elementType == null) {  
           //获取数组元素的类型  
           String elementTypeName = array.getElementTypeName();  
           if (StringUtils.hasText(elementTypeName)) {  
               try {  
                   //使用反射机制创建指定类型的对象  
                   elementType = ClassUtils.forName(elementTypeName, this.beanFactory.getBeanClassLoader());  
                   array.resolvedElementType = elementType;  
               }  
               catch (Throwable ex) {  
                   throw new BeanCreationException(  
                           this.beanDefinition.getResourceDescription(), this.beanName,  
                           "Error resolving array type for " + argName, ex);  
               }  
           }  
           //没有获取到数组的类型，也没有获取到数组元素的类型，则直接设置数  
           //组的类型为Object  
           else {  
               elementType = Object.class;  
           }  
       }  
       //创建指定类型的数组  
       return resolveManagedArray(argName, (List<?>) value, elementType);  
   }  
   //解析list类型的属性值  
   else if (value instanceof ManagedList) {  
       return resolveManagedList(argName, (List<?>) value);  
   }  
   //解析set类型的属性值  
   else if (value instanceof ManagedSet) {  
       return resolveManagedSet(argName, (Set<?>) value);  
   }  
   //解析map类型的属性值  
   else if (value instanceof ManagedMap) {  
       return resolveManagedMap(argName, (Map<?, ?>) value);  
   }  
   //解析props类型的属性值，props其实就是key和value均为字符串的map  
   else if (value instanceof ManagedProperties) {  
       Properties original = (Properties) value;  
       //创建一个拷贝，用于作为解析后的返回值  
       Properties copy = new Properties();  
       for (Map.Entry propEntry : original.entrySet()) {  
           Object propKey = propEntry.getKey();  
           Object propValue = propEntry.getValue();  
           if (propKey instanceof TypedStringValue) {  
               propKey = evaluate((TypedStringValue) propKey);  
           }  
           if (propValue instanceof TypedStringValue) {  
               propValue = evaluate((TypedStringValue) propValue);  
           }  
           copy.put(propKey, propValue);  
       }  
       return copy;  
   }  
   //解析字符串类型的属性值  
   else if (value instanceof TypedStringValue) {  
       TypedStringValue typedStringValue = (TypedStringValue) value;  
       Object valueObject = evaluate(typedStringValue);  
       try {  
           //获取属性的目标类型  
           Class<?> resolvedTargetType = resolveTargetType(typedStringValue);  
           if (resolvedTargetType != null) {  
               //对目标类型的属性进行解析，递归调用  
               return this.typeConverter.convertIfNecessary(valueObject, resolvedTargetType);  
           }  
           //没有获取到属性的目标对象，则按Object类型返回  
           else {  
               return valueObject;  
           }  
       }  
       catch (Throwable ex) {  
           throw new BeanCreationException(  
                   this.beanDefinition.getResourceDescription(), this.beanName,  
                   "Error converting typed String value for " + argName, ex);  
       }  
   }  
   else {  
       return evaluate(value);  
   }  
}  
//解析引用类型的属性值  
private Object resolveReference(Object argName, RuntimeBeanReference ref) {  
   try {  
       //获取引用的Bean名称  
       String refName = ref.getBeanName();  
       refName = String.valueOf(evaluate(refName));  
       //如果引用的对象在父类容器中，则从父类容器中获取指定的引用对象  
       if (ref.isToParent()) {  
           if (this.beanFactory.getParentBeanFactory() == null) {  
               throw new BeanCreationException(  
                       this.beanDefinition.getResourceDescription(), this.beanName,  
                       "Can't resolve reference to bean '" + refName +  
                       "' in parent factory: no parent factory available");  
           }  
           return this.beanFactory.getParentBeanFactory().getBean(refName);  
       }  
       //从当前的容器中获取指定的引用Bean对象，如果指定的Bean没有被实例化，则会递归触发引用Bean的初始化和依赖注入  
       else {  
           Object bean = this.beanFactory.getBean(refName);  
           //将当前实例化对象的依赖引用对象  
           this.beanFactory.registerDependentBean(refName, this.beanName);  
           return bean;  
       }  
   }  
   catch (BeansException ex) {  
       throw new BeanCreationException(  
               this.beanDefinition.getResourceDescription(), this.beanName,  
               "Cannot resolve reference to bean '" + ref.getBeanName() + "' while setting " + argName, ex);  
   }  
}   
//解析array类型的属性  
private Object resolveManagedArray(Object argName, List<?> ml, Class elementType) {  
   //创建一个指定类型的数组，用于存放和返回解析后的数组  
   Object resolved = Array.newInstance(elementType, ml.size());  
   for (int i = 0; i < ml.size(); i++) {  
   //递归解析array的每一个元素，并将解析后的值设置到resolved数组中，索引为i  
       Array.set(resolved, i,  
           resolveValueIfNecessary(new KeyedArgName(argName, i), ml.get(i)));  
   }  
   return resolved;  
}  
//解析list类型的属性  
private List resolveManagedList(Object argName, List<?> ml) {  
   List<Object> resolved = new ArrayList<Object>(ml.size());  
   for (int i = 0; i < ml.size(); i++) {  
       //递归解析list的每一个元素  
       resolved.add(  
           resolveValueIfNecessary(new KeyedArgName(argName, i), ml.get(i)));  
   }  
   return resolved;  
}  
//解析set类型的属性  
private Set resolveManagedSet(Object argName, Set<?> ms) {  
   Set<Object> resolved = new LinkedHashSet<Object>(ms.size());  
   int i = 0;  
   //递归解析set的每一个元素  
   for (Object m : ms) {  
       resolved.add(resolveValueIfNecessary(new KeyedArgName(argName, i), m));  
       i++;  
   }  
   return resolved;  
}  
//解析map类型的属性  
private Map resolveManagedMap(Object argName, Map<?, ?> mm) {  
   Map<Object, Object> resolved = new LinkedHashMap<Object, Object>(mm.size());  
   //递归解析map中每一个元素的key和value  
   for (Map.Entry entry : mm.entrySet()) {  
       Object resolvedKey = resolveValueIfNecessary(argName, entry.getKey());  
       Object resolvedValue = resolveValueIfNecessary(  
               new KeyedArgName(argName, entry.getKey()), entry.getValue());  
       resolved.put(resolvedKey, resolvedValue);  
   }  
   return resolved;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过上面的代码分析，我们明白了Spring是如何将引用类型，内部类以及集合类型等属性进行解析的，属性值解析完成后就可以进行依赖注入了，依赖注入的过程就是Bean对象实例设置到它所依赖的Bean对象属性上去，在第7步中我们已经说过，依赖注入是通过bw.setPropertyValues方法实现的，该方法也使用了委托模式，在BeanWrapper接口中至少定义了方法声明，依赖注入的具体实现交由其实现类BeanWrapperImpl来完成，下面我们就分析依BeanWrapperImpl中赖注入相关的源码。

### 　　8、BeanWrapperImpl对Bean属性的依赖注入

　　BeanWrapperImpl类主要是对容器中完成初始化的Bean实例对象进行属性的依赖注入，即把Bean对象设置到它所依赖的另一个Bean的属性中去，依赖注入的相关源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//实现属性依赖注入功能  
private void setPropertyValue(PropertyTokenHolder tokens, PropertyValue pv) throws BeansException {  
   //PropertyTokenHolder主要保存属性的名称、路径，以及集合的size等信息  
   String propertyName = tokens.canonicalName;  
   String actualName = tokens.actualName;  
   //keys是用来保存集合类型属性的size  
   if (tokens.keys != null) {  
       //将属性信息拷贝  
       PropertyTokenHolder getterTokens = new PropertyTokenHolder();  
       getterTokens.canonicalName = tokens.canonicalName;  
       getterTokens.actualName = tokens.actualName;  
       getterTokens.keys = new String[tokens.keys.length - 1];  
       System.arraycopy(tokens.keys, 0, getterTokens.keys, 0, tokens.keys.length - 1);  
       Object propValue;  
       try {  
           //获取属性值，该方法内部使用JDK的内省( Introspector)机制，调用属性//的getter(readerMethod)方法，获取属性的值  
           propValue = getPropertyValue(getterTokens);  
       }  
       catch (NotReadablePropertyException ex) {  
           throw new NotWritablePropertyException(getRootClass(), this.nestedPath + propertyName,  
                   "Cannot access indexed value in property referenced " +  
                   "in indexed property path '" + propertyName + "'", ex);  
       }  
       //获取集合类型属性的长度  
       String key = tokens.keys[tokens.keys.length - 1];  
       if (propValue == null) {  
           throw new NullValueInNestedPathException(getRootClass(), this.nestedPath + propertyName,  
                   "Cannot access indexed value in property referenced " +  
                   "in indexed property path '" + propertyName + "': returned null");  
       }  
       //注入array类型的属性值  
       else if (propValue.getClass().isArray()) {  
           //获取属性的描述符  
           PropertyDescriptor pd = getCachedIntrospectionResults().getPropertyDescriptor(actualName);  
           //获取数组的类型  
           Class requiredType = propValue.getClass().getComponentType();  
           //获取数组的长度  
           int arrayIndex = Integer.parseInt(key);  
           Object oldValue = null;  
           try {  
               //获取数组以前初始化的值  
               if (isExtractOldValueForEditor()) {  
                   oldValue = Array.get(propValue, arrayIndex);  
               }  
               //将属性的值赋值给数组中的元素  
               Object convertedValue = convertIfNecessary(propertyName, oldValue, pv.getValue(), requiredType,  
                       new PropertyTypeDescriptor(pd, new MethodParameter(pd.getReadMethod(), -1), requiredType));  
               Array.set(propValue, arrayIndex, convertedValue);  
           }  
           catch (IndexOutOfBoundsException ex) {  
               throw new InvalidPropertyException(getRootClass(), this.nestedPath + propertyName,  
                       "Invalid array index in property path '" + propertyName + "'", ex);  
           }  
       }  
       //注入list类型的属性值  
       else if (propValue instanceof List) {  
           PropertyDescriptor pd = getCachedIntrospectionResults().getPropertyDescriptor(actualName);  
           //获取list集合的类型  
           Class requiredType = GenericCollectionTypeResolver.getCollectionReturnType(  
                   pd.getReadMethod(), tokens.keys.length);  
           List list = (List) propValue;  
           //获取list集合的size  
           int index = Integer.parseInt(key);  
           Object oldValue = null;  
           if (isExtractOldValueForEditor() && index < list.size()) {  
               oldValue = list.get(index);  
           }  
           //获取list解析后的属性值  
           Object convertedValue = convertIfNecessary(propertyName, oldValue, pv.getValue(), requiredType,  
                   new PropertyTypeDescriptor(pd, new MethodParameter(pd.getReadMethod(), -1), requiredType));  
           if (index < list.size()) {  
               //为list属性赋值  
               list.set(index, convertedValue);  
           }  
           //如果list的长度大于属性值的长度，则多余的元素赋值为null  
           else if (index >= list.size()) {  
               for (int i = list.size(); i < index; i++) {  
                   try {  
                       list.add(null);  
                   }  
                   catch (NullPointerException ex) {  
                       throw new InvalidPropertyException(getRootClass(), this.nestedPath + propertyName,  
                               "Cannot set element with index " + index + " in List of size " +  
                               list.size() + ", accessed using property path '" + propertyName +  
                               "': List does not support filling up gaps with null elements");  
                   }  
               }  
               list.add(convertedValue);  
           }  
       }  
       //注入map类型的属性值  
       else if (propValue instanceof Map) {  
           PropertyDescriptor pd = getCachedIntrospectionResults().getPropertyDescriptor(actualName);  
           //获取map集合key的类型  
           Class mapKeyType = GenericCollectionTypeResolver.getMapKeyReturnType(  
                   pd.getReadMethod(), tokens.keys.length);  
           //获取map集合value的类型  
           Class mapValueType = GenericCollectionTypeResolver.getMapValueReturnType(  
                   pd.getReadMethod(), tokens.keys.length);  
           Map map = (Map) propValue;  
           //解析map类型属性key值  
           Object convertedMapKey = convertIfNecessary(null, null, key, mapKeyType,  
                   new PropertyTypeDescriptor(pd, new MethodParameter(pd.getReadMethod(), -1), mapKeyType));  
           Object oldValue = null;  
           if (isExtractOldValueForEditor()) {  
               oldValue = map.get(convertedMapKey);  
           }  
           //解析map类型属性value值  
           Object convertedMapValue = convertIfNecessary(  
                   propertyName, oldValue, pv.getValue(), mapValueType,  
                   new TypeDescriptor(new MethodParameter(pd.getReadMethod(), -1, tokens.keys.length + 1)));  
           //将解析后的key和value值赋值给map集合属性  
           map.put(convertedMapKey, convertedMapValue);  
       }  
       else {  
           throw new InvalidPropertyException(getRootClass(), this.nestedPath + propertyName,  
                   "Property referenced in indexed property path '" + propertyName +  
                   "' is neither an array nor a List nor a Map; returned value was [" + pv.getValue() + "]");  
       }  
   }  
   //对非集合类型的属性注入  
   else {  
       PropertyDescriptor pd = pv.resolvedDescriptor;  
       if (pd == null || !pd.getWriteMethod().getDeclaringClass().isInstance(this.object)) {  
           pd = getCachedIntrospectionResults().getPropertyDescriptor(actualName);  
           //无法获取到属性名或者属性没有提供setter(写方法)方法  
           if (pd == null || pd.getWriteMethod() == null) {  
               //如果属性值是可选的，即不是必须的，则忽略该属性值  
               if (pv.isOptional()) {  
                   logger.debug("Ignoring optional value for property '" + actualName +  
                           "' - property not found on bean class [" + getRootClass().getName() + "]");  
                   return;  
               }  
               //如果属性值是必须的，则抛出无法给属性赋值，因为每天提供setter方法异常  
               else {  
                   PropertyMatches matches = PropertyMatches.forProperty(propertyName, getRootClass());  
                   throw new NotWritablePropertyException(  
                           getRootClass(), this.nestedPath + propertyName,  
                           matches.buildErrorMessage(), matches.getPossibleMatches());  
               }  
           }  
           pv.getOriginalPropertyValue().resolvedDescriptor = pd;  
       }  
       Object oldValue = null;  
       try {  
           Object originalValue = pv.getValue();  
           Object valueToApply = originalValue;  
           if (!Boolean.FALSE.equals(pv.conversionNecessary)) {  
               if (pv.isConverted()) {  
                   valueToApply = pv.getConvertedValue();  
               }  
               else {  
                   if (isExtractOldValueForEditor() && pd.getReadMethod() != null) {  
                       //获取属性的getter方法(读方法)，JDK内省机制  
                       final Method readMethod = pd.getReadMethod();  
                       //如果属性的getter方法不是public访问控制权限的，即访问控制权限比较严格，  
                       //则使用JDK的反射机制强行访问非public的方法(暴力读取属性值)  
                       if (!Modifier.isPublic(readMethod.getDeclaringClass().getModifiers()) &&  
                               !readMethod.isAccessible()) {  
                           if (System.getSecurityManager()!= null) {  
                               //匿名内部类，根据权限修改属性的读取控制限制  
                               AccessController.doPrivileged(new PrivilegedAction<Object>() {  
                                   public Object run() {  
                                       readMethod.setAccessible(true);  
                                       return null;  
                                   }  
                               });  
                           }  
                           else {  
                               readMethod.setAccessible(true);  
                           }  
                       }  
                       try {  
                           //属性没有提供getter方法时，调用潜在的读取属性值//的方法，获取属性值  
                           if (System.getSecurityManager() != null) {  
                               oldValue = AccessController.doPrivileged(new PrivilegedExceptionAction<Object>() {  
                                   public Object run() throws Exception {  
                                       return readMethod.invoke(object);  
                                   }  
                               }, acc);  
                           }  
                           else {  
                               oldValue = readMethod.invoke(object);  
                           }  
                       }  
                       catch (Exception ex) {  
                           if (ex instanceof PrivilegedActionException) {  
                               ex = ((PrivilegedActionException) ex).getException();  
                           }  
                           if (logger.isDebugEnabled()) {  
                               logger.debug("Could not read previous value of property '" +  
                                       this.nestedPath + propertyName + "'", ex);  
                           }  
                       }  
                   }  
                   //设置属性的注入值  
                   valueToApply = convertForProperty(propertyName, oldValue, originalValue, pd);  
               }  
               pv.getOriginalPropertyValue().conversionNecessary = (valueToApply != originalValue);  
           }  
           //根据JDK的内省机制，获取属性的setter(写方法)方法  
           final Method writeMethod = (pd instanceof GenericTypeAwarePropertyDescriptor ?  
                   ((GenericTypeAwarePropertyDescriptor) pd).getWriteMethodForActualAccess() :  
                   pd.getWriteMethod());  
           //如果属性的setter方法是非public，即访问控制权限比较严格，则使用JDK的反射机制，  
           //强行设置setter方法可访问(暴力为属性赋值)  
           if (!Modifier.isPublic(writeMethod.getDeclaringClass().getModifiers()) && !writeMethod.isAccessible()) {  
               //如果使用了JDK的安全机制，则需要权限验证  
               if (System.getSecurityManager()!= null) {  
                   AccessController.doPrivileged(new PrivilegedAction<Object>() {  
                       public Object run() {  
                           writeMethod.setAccessible(true);  
                           return null;  
                       }  
                   });  
               }  
               else {  
                   writeMethod.setAccessible(true);  
               }  
           }  
           final Object value = valueToApply;  
           if (System.getSecurityManager() != null) {  
               try {  
                   //将属性值设置到属性上去  
                   AccessController.doPrivileged(new PrivilegedExceptionAction<Object>() {  
                       public Object run() throws Exception {  
                           writeMethod.invoke(object, value);  
                           return null;  
                       }  
                   }, acc);  
               }  
               catch (PrivilegedActionException ex) {  
                   throw ex.getException();  
               }  
           }  
           else {  
               writeMethod.invoke(this.object, value);  
           }  
       }  
       catch (TypeMismatchException ex) {  
           throw ex;  
       }  
       catch (InvocationTargetException ex) {  
           PropertyChangeEvent propertyChangeEvent =  
                   new PropertyChangeEvent(this.rootObject, this.nestedPath + propertyName, oldValue, pv.getValue());  
           if (ex.getTargetException() instanceof ClassCastException) {  
               throw new TypeMismatchException(propertyChangeEvent, pd.getPropertyType(), ex.getTargetException());  
           }  
           else {  
               throw new MethodInvocationException(propertyChangeEvent, ex.getTargetException());  
           }  
       }  
       catch (Exception ex) {  
           PropertyChangeEvent pce =  
                   new PropertyChangeEvent(this.rootObject, this.nestedPath + propertyName, oldValue, pv.getValue());  
           throw new MethodInvocationException(pce, ex);  
       }  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过对上面注入依赖代码的分析，我们已经明白了Spring IOC容器是如何将属性的值注入到Bean实例对象中去的：

- 对于集合类型的属性，将其属性值解析为目标类型的集合后直接赋值给属性。
- 对于非集合类型的属性，大量使用了JDK的反射和内省机制，通过属性的getter方法(reader method)获取指定属性注入以前的值，同时调用属性的setter方法(writer method)为属性设置注入后的值。看到这里相信很多人都明白了Spring的setter注入原理。

　　至此Spring IOC容器对Bean定义资源文件的定位，载入、解析和依赖注入已经全部分析完毕，现在Spring IOC容器中管理了一系列靠依赖关系联系起来的Bean，程序不需要应用自己手动创建所需的对象，Spring IOC容器会在我们使用的时候自动为我们创建，并且为我们注入好相关的依赖，这就是Spring核心功能的控制反转和依赖注入的相关功能。

## 六、IOC容器高级特性

### 　　1、介绍

   通过前面对Spring IoC容器的源码分析，我们已经基本上了解了Spring IOC容器对Bean定义资源的定位、读入和解析过程，同时也清楚了当用户通过getBean方法向IOC容器获取被管理的Bean时，IOC容器对Bean进行的初始化和依赖注入过程，这些是Spring IOC容器的基本功能特性。Spring IOC容器还有一些高级特性，如使用lazy-init属性对Bean预初始化、FactoryBean产生或者修饰Bean对象的生成、IOC容器初始化Bean过程中使用BeanPostProcessor后置处理器对Bean声明周期事件管理和IOC容器的autowiring自动装配功能等。

### 　　2、Spring IoC容器的lazy-init属性实现预实例化

   通过前面我们对IOC容器的实现和工作原理分析，我们知道IOC容器的初始化过程就是对Bean定义资源的定位、载入和注册，此时容器对Bean的依赖注入并没有发生，依赖注入主要是在应用程序第一次向容器索取Bean时，通过getBean方法的调用完成。

　　当Bean定义资源的<Bean>元素中配置了lazy-init属性时，容器将会在初始化的时候对所配置的Bean进行预实例化，Bean的依赖注入在容器初始化的时候就已经完成。这样，当应用程序第一次向容器索取被管理的Bean时，就不用再初始化和对Bean进行依赖注入了，直接从容器中获取已经完成依赖注入的现成Bean，可以提高应用第一次向容器获取Bean的性能。

　　下面我们通过代码分析容器预实例化的实现过程：

#### 　　(1). refresh()

　　先从IOC容器的初始化过程开始，通过前面文章分析，我们知道IOC容器读入已经定位的Bean定义资源是从refresh方法开始的，我们首先从AbstractApplicationContext类的refresh方法入手分析，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//容器初始化的过程，读入Bean定义资源，并解析注册  
public void refresh() throws BeansException, IllegalStateException {  
   synchronized (this.startupShutdownMonitor) {  
        //调用容器准备刷新的方法，获取容器的当时时间，同时给容器设置同步标识  
        prepareRefresh();  
        //告诉子类启动refreshBeanFactory()方法，Bean定义资源文件的载入从  
        //子类的refreshBeanFactory()方法启动  
        ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();  
        //为BeanFactory配置容器特性，例如类加载器、事件处理器等  
       prepareBeanFactory(beanFactory);  
       try {  
           //为容器的某些子类指定特殊的BeanPost事件处理器  
           postProcessBeanFactory(beanFactory);  
           //调用所有注册的BeanFactoryPostProcessor的Bean  
           invokeBeanFactoryPostProcessors(beanFactory);  
           //为BeanFactory注册BeanPost事件处理器.  
           //BeanPostProcessor是Bean后置处理器，用于监听容器触发的事件  
           registerBeanPostProcessors(beanFactory);  
           //初始化信息源，和国际化相关.  
           initMessageSource();  
           //初始化容器事件传播器.  
           initApplicationEventMulticaster();  
           //调用子类的某些特殊Bean初始化方法  
           onRefresh();  
           //为事件传播器注册事件监听器.  
           registerListeners();  
           //这里是对容器lazy-init属性进行处理的入口方法  
           finishBeanFactoryInitialization(beanFactory);  
           //初始化容器的生命周期事件处理器，并发布容器的生命周期事件  
           finishRefresh();  
       }  
       catch (BeansException ex) {  
           //销毁以创建的单态Bean  
           destroyBeans();  
           //取消refresh操作，重置容器的同步标识.  
           cancelRefresh(ex);  
           throw ex;  
       }  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　在refresh方法中ConfigurableListableBeanFactorybeanFactory = obtainFreshBeanFactory()；启动了Bean定义资源的载入、注册过程，而finishBeanFactoryInitialization方法是对注册后的Bean定义中的预实例化(lazy-init=false，Spring默认就是预实例化，即为true)的Bean进行处理的地方。

#### 　　(2). finishBeanFactoryInitialization处理预实例化Bean

　　当Bean定义资源被载入IOC容器之后，容器将Bean定义资源解析为容器内部的数据结构BeanDefinition注册到容器中，AbstractApplicationContext类中的finishBeanFactoryInitialization方法对配置了预实例化属性的Bean进行预初始化过程，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//对配置了lazy-init属性的Bean进行预实例化处理  
protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {  
   //这是Spring3以后新加的代码，为容器指定一个转换服务(ConversionService)  
   //在对某些Bean属性进行转换时使用  
   if (beanFactory.containsBean(CONVERSION_SERVICE_BEAN_NAME) &&  
           beanFactory.isTypeMatch(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class)) {  
       beanFactory.setConversionService(  
               beanFactory.getBean(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class));  
   }  
   //为了类型匹配，停止使用临时的类加载器  
   beanFactory.setTempClassLoader(null);  
   //缓存容器中所有注册的BeanDefinition元数据，以防被修改  
   beanFactory.freezeConfiguration();  
   //对配置了lazy-init属性的单态模式Bean进行预实例化处理  
   beanFactory.preInstantiateSingletons();  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　ConfigurableListableBeanFactory是一个接口，其preInstantiateSingletons方法由其子类DefaultListableBeanFactory提供。 

#### 　　(3)、DefaultListableBeanFactory对配置lazy-init属性单态Bean的预实例化

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//对配置lazy-init属性单态Bean的预实例化  
public void preInstantiateSingletons() throws BeansException {  
   if (this.logger.isInfoEnabled()) {  
       this.logger.info("Pre-instantiating singletons in " + this);  
   }  
   //在对配置lazy-init属性单态Bean的预实例化过程中，必须多线程同步，以确保数据一致性  
   synchronized (this.beanDefinitionMap) {  
       for (String beanName : this.beanDefinitionNames) {  
           //获取指定名称的Bean定义  
           RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);  
           //Bean不是抽象的，是单态模式的，且lazy-init属性配置为false  
           if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit()) {  
               //如果指定名称的bean是创建容器的Bean  
               if (isFactoryBean(beanName)) {  
               //FACTORY_BEAN_PREFIX=”&”，当Bean名称前面加”&”符号  
              //时，获取的是产生容器对象本身，而不是容器产生的Bean.  
              //调用getBean方法，触发容器对Bean实例化和依赖注入过程  
                   final FactoryBean factory = (FactoryBean) getBean(FACTORY_BEAN_PREFIX + beanName);  
                   //标识是否需要预实例化  
                   boolean isEagerInit;  
                   if (System.getSecurityManager() != null && factory instanceof SmartFactoryBean) {  
                       //一个匿名内部类  
                       isEagerInit = AccessController.doPrivileged(new PrivilegedAction<Boolean>() {  
                           public Boolean run() {  
                               return ((SmartFactoryBean) factory).isEagerInit();  
                           }  
                       }, getAccessControlContext());  
                   }  
                   else {  
                       isEagerInit = factory instanceof SmartFactoryBean && ((SmartFactoryBean) factory).isEagerInit();   
                   }  
                   if (isEagerInit) {  
                      //调用getBean方法，触发容器对Bean实例化和依赖注入过程  
                       getBean(beanName);  
                   }  
               }  
               else {  
                    //调用getBean方法，触发容器对Bean实例化和依赖注入过程  
                   getBean(beanName);  
               }  
           }  
       }  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过对lazy-init处理源码的分析，我们可以看出，如果设置了lazy-init属性，则容器在完成Bean定义的注册之后，会通过getBean方法，触发对指定Bean的初始化和依赖注入过程，这样当应用第一次向容器索取所需的Bean时，容器不再需要对Bean进行初始化和依赖注入，直接从已经完成实例化和依赖注入的Bean中取一个线程的Bean，这样就提高了第一次获取Bean的性能。

### 　　3、FactoryBean的实现

　　在Spring中，有两个很容易混淆的类：BeanFactory和FactoryBean。

- BeanFactory：Bean工厂，是一个工厂(Factory)，我们Spring IoC容器的最顶层接口就是这个BeanFactory，它的作用是管理Bean，即实例化、定位、配置应用程序中的对象及建立这些对象间的依赖。
- FactoryBean：工厂Bean，是一个Bean，作用是产生其他bean实例。通常情况下，这种bean没有什么特别的要求，仅需要提供一个工厂方法，该方法用来返回其他bean实例。通常情况下，bean无须自己实现工厂模式，Spring容器担任工厂角色；但少数情况下，容器中的bean本身就是工厂，其作用是产生其它bean实例。

　　**当用户使用容器本身时，可以使用转义字符”&”来得到FactoryBean本身，以区别通过FactoryBean产生的实例对象和FactoryBean对象本身**。在BeanFactory中通过如下代码定义了该转义字符：

```
String FACTORY_BEAN_PREFIX = "&";
```

　　如果myJndiObject是一个FactoryBean，则使用&myJndiObject得到的是myJndiObject对象，而不是myJndiObject产生出来的对象。

#### 　　(1). FactoryBean的源码

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//工厂Bean，用于产生其他对象  
public interface FactoryBean<T> {  
   //获取容器管理的对象实例  
    T getObject() throws Exception;  
    //获取Bean工厂创建的对象的类型  
    Class<?> getObjectType();  
    //Bean工厂创建的对象是否是单态模式，如果是单态模式，则整个容器中只有一个实例  
   //对象，每次请求都返回同一个实例对象  
    boolean isSingleton();  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

#### 　　(2). AbstractBeanFactory的getBean方法调用FactoryBean

　　在前面我们分析Spring IOC 容器实例化Bean并进行依赖注入过程的源码时，提到在getBean方法触发容器实例化Bean的时候会调用AbstractBeanFactory的doGetBean方法来进行实例化的过程，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//真正实现向IoC容器获取Bean的功能，也是触发依赖注入功能的地方    
 @SuppressWarnings("unchecked")    
 protected <T> T doGetBean(    
         final String name, final Class<T> requiredType, final Object[] args, boolean typeCheckOnly)    
         throws BeansException {    
     //根据指定的名称获取被管理Bean的名称，剥离指定名称中对容器的相关依赖，如果指定的是别名，将别名转换为规范的Bean名称    
     final String beanName = transformedBeanName(name);    
     Object bean;    
     //先从缓存中取是否已经有被创建过的单态类型的Bean，对于单态模式的Bean整个IoC容器中只创建一次，不需要重复创建    
     Object sharedInstance = getSingleton(beanName);    
     //IoC容器创建单态模式Bean实例对象    
     if (sharedInstance != null && args == null) {    
         if (logger.isDebugEnabled()) {    
         　　 //如果指定名称的Bean在容器中已有单态模式的Bean被创建，直接返回已经创建的Bean    
             if (isSingletonCurrentlyInCreation(beanName)) {    
                 logger.debug("Returning eagerly cached instance of singleton bean '" + beanName +    
                         "' that is not fully initialized yet - a consequence of a circular reference");    
             }    
             else {    
                 logger.debug("Returning cached instance of singleton bean '" + beanName + "'");    
             }    
         }    
         //获取给定Bean的实例对象，主要是完成FactoryBean的相关处理   
         bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);    
     }    
    ……  
}  
//获取给定Bean的实例对象，主要是完成FactoryBean的相关处理 
protected Object getObjectForBeanInstance(  
       Object beanInstance, String name, String beanName, RootBeanDefinition mbd) {  
   //容器已经得到了Bean实例对象，这个实例对象可能是一个普通的Bean，也可能是一个工厂Bean，如果是一个工厂Bean，则使用它创建一个Bean实例对象
   //如果调用本身就想获得一个容器的引用，则指定返回这个工厂Bean实例对象  
   //如果指定的名称是容器的解引用(dereference，即是对象本身而非内存地址)，且Bean实例也不是创建Bean实例对象的工厂Bean  
   if (BeanFactoryUtils.isFactoryDereference(name) && !(beanInstance instanceof FactoryBean)) {  
       throw new BeanIsNotAFactoryException(transformedBeanName(name), beanInstance.getClass());  
   }  
   //如果Bean实例不是工厂Bean，或者指定名称是容器的解引用，调用者向获取对容器的引用，则直接返回当前的Bean实例  
   if (!(beanInstance instanceof FactoryBean) || BeanFactoryUtils.isFactoryDereference(name)) {  
       return beanInstance;  
   }  
   //处理指定名称不是容器的解引用，或者根据名称获取的Bean实例对象是一个工厂Bean，使用工厂Bean创建一个Bean的实例对象  
   Object object = null;  
   if (mbd == null) {  
       //从Bean工厂缓存中获取给定名称的Bean实例对象  
       object = getCachedObjectForFactoryBean(beanName);  
   }  
   //让Bean工厂生产给定名称的Bean对象实例  
   if (object == null) {  
       FactoryBean factory = (FactoryBean) beanInstance;  
       //如果从Bean工厂生产的Bean是单态模式的，则缓存  
       if (mbd == null && containsBeanDefinition(beanName)) {  
           //从容器中获取指定名称的Bean定义，如果继承基类，则合并基类相关属性  
           mbd = getMergedLocalBeanDefinition(beanName);  
       }  
       //如果从容器得到Bean定义信息，并且Bean定义信息不是虚构的，则让工厂Bean生产Bean实例对象  
       boolean synthetic = (mbd != null && mbd.isSynthetic());  
       //调用FactoryBeanRegistrySupport类的getObjectFromFactoryBean方法，实现工厂Bean生产Bean对象实例的过程  
       object = getObjectFromFactoryBean(factory, beanName, !synthetic);  
   }  
   return object;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　在上面获取给定Bean的实例对象的getObjectForBeanInstance方法中，会调用FactoryBeanRegistrySupport类的getObjectFromFactoryBean方法，该方法实现了Bean工厂生产Bean实例对象。

　　**Dereference(解引用)**：一个在C/C++中应用比较多的术语，在C++中，”*”是解引用符号，而”&”是引用符号，解引用是指变量指向的是所引用对象的本身数据，而不是引用对象的内存地址。

#### 　　(3)、AbstractBeanFactory生产Bean实例对象

　　AbstractBeanFactory类中生产Bean实例对象的主要源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//Bean工厂生产Bean实例对象  
protected Object getObjectFromFactoryBean(FactoryBean factory, String beanName, boolean shouldPostProcess) {  
   //Bean工厂是单态模式，并且Bean工厂缓存中存在指定名称的Bean实例对象  
   if (factory.isSingleton() && containsSingleton(beanName)) {  
       //多线程同步，以防止数据不一致  
       synchronized (getSingletonMutex()) {  
           //直接从Bean工厂缓存中获取指定名称的Bean实例对象  
           Object object = this.factoryBeanObjectCache.get(beanName);  
           //Bean工厂缓存中没有指定名称的实例对象，则生产该实例对象  
           if (object == null) {  
               //调用Bean工厂的getObject方法生产指定Bean的实例对象  
               object = doGetObjectFromFactoryBean(factory, beanName, shouldPostProcess);  
               //将生产的实例对象添加到Bean工厂缓存中  
               this.factoryBeanObjectCache.put(beanName, (object != null ? object : NULL_OBJECT));  
           }  
           return (object != NULL_OBJECT ? object : null);  
       }  
   }  
   //调用Bean工厂的getObject方法生产指定Bean的实例对象  
   else {  
       return doGetObjectFromFactoryBean(factory, beanName, shouldPostProcess);  
   }  
}  
//调用Bean工厂的getObject方法生产指定Bean的实例对象  
private Object doGetObjectFromFactoryBean(  
       final FactoryBean factory, final String beanName, final boolean shouldPostProcess)  
       throws BeanCreationException {  
   Object object;  
   try {  
       if (System.getSecurityManager() != null) {  
           AccessControlContext acc = getAccessControlContext();  
           try {  
               //实现PrivilegedExceptionAction接口的匿名内置类，根据JVM检查权限，然后决定BeanFactory创建实例对象  
               object = AccessController.doPrivileged(new PrivilegedExceptionAction<Object>() {  
                   public Object run() throws Exception {  
                           //调用BeanFactory接口实现类的创建对象方法  
                           return factory.getObject();  
                       }  
                   }, acc);  
           }  
           catch (PrivilegedActionException pae) {  
               throw pae.getException();  
           }  
       }  
       else {  
           //调用BeanFactory接口实现类的创建对象方法  
           object = factory.getObject();  
       }  
   }  
   catch (FactoryBeanNotInitializedException ex) {  
       throw new BeanCurrentlyInCreationException(beanName, ex.toString());  
   }  
   catch (Throwable ex) {  
       throw new BeanCreationException(beanName, "FactoryBean threw exception on object creation", ex);  
   }  
   //创建出来的实例对象为null，或者因为单态对象正在创建而返回null  
   if (object == null && isSingletonCurrentlyInCreation(beanName)) {  
       throw new BeanCurrentlyInCreationException(  
               beanName, "FactoryBean which is currently in creation returned null from getObject");  
   }  
   //为创建出来的Bean实例对象添加BeanPostProcessor后置处理器  
   if (object != null && shouldPostProcess) {  
       try {  
           object = postProcessObjectFromFactoryBean(object, beanName);  
       }  
       catch (Throwable ex) {  
           throw new BeanCreationException(beanName, "Post-processing of the FactoryBean's object failed", ex);  
       }  
   }  
   return object;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　从上面的源码分析中，我们可以看出，**BeanFactory接口调用其实现类的getObject方法来实现创建Bean实例对象的功能**。

#### 　　(4). 工厂Bean的实现类getObject方法创建Bean实例对象

　　FactoryBean的实现类有非常多，比如：Proxy、RMI、JNDI、ServletContextFactoryBean等等，FactoryBean接口为Spring容器提供了一个很好的封装机制，具体的getObject有不同的实现类根据不同的实现策略来具体提供，我们分析一个最简单的AnnotationTestFactoryBean的实现源码：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public class AnnotationTestBeanFactory implements FactoryBean<IJmxTestBean> {  
       private final FactoryCreatedAnnotationTestBean instance = new FactoryCreatedAnnotationTestBean();  
       public AnnotationTestBeanFactory() {  
           this.instance.setName("FACTORY");  
       }  
       //AnnotationTestBeanFactory产生Bean实例对象的实现  
       public IJmxTestBean getObject() throws Exception {  
           return this.instance;  
       }  
       public Class<? extends IJmxTestBean> getObjectType() {  
           return FactoryCreatedAnnotationTestBean.class;  
       }  
       public boolean isSingleton() {  
           return true;  
       }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　其他的Proxy，RMI，JNDI等等，都是根据相应的策略提供getObject的实现。这里不做一一分析，这已经不是Spring的核心功能，有需要的时候再去深入研究。

### 　　4. BeanPostProcessor后置处理器的实现

　　BeanPostProcessor后置处理器是Spring IoC容器经常使用到的一个特性，这个Bean后置处理器是一个监听器，可以监听容器触发的Bean声明周期事件。后置处理器向容器注册以后，容器中管理的Bean就具备了接收IOC容器事件回调的能力。

　　BeanPostProcessor的使用非常简单，只需要提供一个实现接口BeanPostProcessor的实现类，然后在Bean的配置文件中设置即可。

　　(1). BeanPostProcessor源码

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package org.springframework.beans.factory.config;  
import org.springframework.beans.BeansException;  
public interface BeanPostProcessor {  
   //为在Bean的初始化前提供回调入口  
   Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException;  
   //为在Bean的初始化之后提供回调入口  
   Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException;  
 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　这两个回调的入口都是和容器管理的Bean的生命周期事件紧密相关，可以为用户提供在Spring IOC容器初始化Bean过程中自定义的处理操作。

　　(2). AbstractAutowireCapableBeanFactory类对容器生成的Bean添加后置处理器

　　**BeanPostProcessor后置处理器的调用发生在Spring IOC容器完成对Bean实例对象的创建和属性的依赖注入完成之后**，在对Spring依赖注入的源码分析过程中我们知道，当应用程序第一次调用getBean方法(lazy-init预实例化除外)向Spring IoC容器索取指定Bean时触发Spring IoC容器创建Bean实例对象并进行依赖注入的过程，其中真正实现创建Bean对象并进行依赖注入的方法是AbstractAutowireCapableBeanFactory类的doCreateBean方法，主要源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//真正创建Bean的方法  
protected Object doCreateBean(final String beanName, final RootBeanDefinition mbd, final Object[] args) {  
   //创建Bean实例对象  
   ……  
   try {  
       //对Bean属性进行依赖注入  
       populateBean(beanName, mbd, instanceWrapper);  
       if (exposedObject != null) {  
          //在对Bean实例对象生成和依赖注入完成以后，开始对Bean实例对象/进行初始化 ，为Bean实例对象应用BeanPostProcessor后置处理器  
          exposedObject = initializeBean(beanName, exposedObject, mbd);  
       }  
   }  
   catch (Throwable ex) {  
       if (ex instanceof BeanCreationException && beanName.equals(((BeanCreationException) ex).getBeanName())) {  
           throw (BeanCreationException) ex;  
       }  
   ……  
   //为应用返回所需要的实例对象  
   return exposedObject;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　从上面的代码中我们知道，为Bean实例对象添加BeanPostProcessor后置处理器的入口的是initializeBean方法。

　　(3). initializeBean方法为容器产生的Bean实例对象添加BeanPostProcessor后置处理器

　　同样在AbstractAutowireCapableBeanFactory类中，initializeBean方法实现为容器创建的Bean实例对象添加BeanPostProcessor后置处理器，源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//初始容器创建的Bean实例对象，为其添加BeanPostProcessor后置处理器  
protected Object initializeBean(final String beanName, final Object bean, RootBeanDefinition mbd) {  
   //JDK的安全机制验证权限  
   if (System.getSecurityManager() != null) {  
       //实现PrivilegedAction接口的匿名内部类  
       AccessController.doPrivileged(new PrivilegedAction<Object>() {  
           public Object run() {  
               invokeAwareMethods(beanName, bean);  
               return null;  
           }  
       }, getAccessControlContext());  
   }  
   else {  
       //为Bean实例对象包装相关属性，如名称，类加载器，所属容器等信息  
       invokeAwareMethods(beanName, bean);  
   }  
   Object wrappedBean = bean;  
   //对BeanPostProcessor后置处理器的postProcessBeforeInitialization回调方法的调用，为Bean实例初始化前做一些处理  
   if (mbd == null || !mbd.isSynthetic()) {  
       wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);  
   }  
   //调用Bean实例对象初始化的方法，这个初始化方法是在Spring Bean定义配置文件中通过init-method属性指定的  
   try {  
       invokeInitMethods(beanName, wrappedBean, mbd);  
   }  
   catch (Throwable ex) {  
       throw new BeanCreationException(  
               (mbd != null ? mbd.getResourceDescription() : null),  
               beanName, "Invocation of init method failed", ex);  
   }  
   //对BeanPostProcessor后置处理器的postProcessAfterInitialization回调方法的调用，为Bean实例初始化之后做一些处理  
   if (mbd == null || !mbd.isSynthetic()) {  
       wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);  
   }  
   return wrappedBean;  
}  
//调用BeanPostProcessor后置处理器实例对象初始化之前的处理方法  
public Object applyBeanPostProcessorsBeforeInitialization(Object existingBean, String beanName)  
       throws BeansException {  
   Object result = existingBean;  
   //遍历容器为所创建的Bean添加的所有BeanPostProcessor后置处理器  
   for (BeanPostProcessor beanProcessor : getBeanPostProcessors()) {  
       //调用Bean实例所有的后置处理中的初始化前处理方法，为Bean实例对象在初始化之前做一些自定义的处理操作  
       result = beanProcessor.postProcessBeforeInitialization(result, beanName);  
       if (result == null) {  
           return result;  
       }  
   }  
   return result;  
}  
//调用BeanPostProcessor后置处理器实例对象初始化之后的处理方法  
public Object applyBeanPostProcessorsAfterInitialization(Object existingBean, String beanName)  
       throws BeansException {  
   Object result = existingBean;  
   //遍历容器为所创建的Bean添加的所有BeanPostProcessor后置处理器  
   for (BeanPostProcessor beanProcessor : getBeanPostProcessors()) {  
       //调用Bean实例所有的后置处理中的初始化后处理方法，为Bean实例对象在初始化之后做一些自定义的处理操作  
       result = beanProcessor.postProcessAfterInitialization(result, beanName);  
       if (result == null) {  
           return result;  
       }  
   }  
   return result;  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　BeanPostProcessor是一个接口，其初始化前的操作方法和初始化后的操作方法均委托其实现子类来实现，在Spring中，BeanPostProcessor的实现子类非常的多，分别完成不同的操作，如：AOP面向切面编程的注册通知适配器、Bean对象的数据校验、Bean继承属性/方法的合并等等，我们以最简单的AOP切面织入来简单了解其主要的功能。

#### 　　(4). AdvisorAdapterRegistrationManager在Bean对象初始化后注册通知适配器

　　AdvisorAdapterRegistrationManager是BeanPostProcessor的一个实现类，其主要的作用为容器中管理的Bean注册一个面向切面编程的通知适配器，以便在Spring容器为所管理的Bean进行面向切面编程时提供方便，其源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//为容器中管理的Bean注册一个面向切面编程的通知适配器  
public class AdvisorAdapterRegistrationManager implements BeanPostProcessor {  
   //容器中负责管理切面通知适配器注册的对象  
   private AdvisorAdapterRegistry advisorAdapterRegistry = GlobalAdvisorAdapterRegistry.getInstance();  
   public void setAdvisorAdapterRegistry(AdvisorAdapterRegistry advisorAdapterRegistry) {  
       this.advisorAdapterRegistry = advisorAdapterRegistry;  
   }  
   //BeanPostProcessor在Bean对象初始化前的操作  
   public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {  
       //没有做任何操作，直接返回容器创建的Bean对象  
       return bean;  
   }  
   //BeanPostProcessor在Bean对象初始化后的操作  
   public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {  
       if (bean instanceof AdvisorAdapter){  
           //如果容器创建的Bean实例对象是一个切面通知适配器，则向容器的注册
           this.advisorAdapterRegistry.registerAdvisorAdapter((AdvisorAdapter) bean);  
       }  
       return bean;  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　其他的BeanPostProcessor接口实现类的也类似，都是对Bean对象使用到的一些特性进行处理，或者向IOC容器中注册，为创建的Bean实例对象做一些自定义的功能增加，这些操作是容器初始化Bean时自动触发的，不需要人为的干预。

### 　　5. Spring IOC容器autowiring实现原理

　　Spring IOC容器提供了两种管理Bean依赖关系的方式：

- 显式管理：通过BeanDefinition的属性值和构造方法实现Bean依赖关系管理。
- autowiring：Spring IoC容器的依赖自动装配功能，不需要对Bean属性的依赖关系做显式的声明，只需要在配置好autowiring属性，IOC容器会自动使用反射查找属性的类型和名称，然后基于属性的类型或者名称来自动匹配容器中管理的Bean，从而自动地完成依赖注入。

　　通过对autowiring自动装配特性的理解，我们知道容器对Bean的自动装配发生在容器对Bean依赖注入的过程中。在前面对Spring IOC容器的依赖注入过程源码分析中，我们已经知道了容器对Bean实例对象的属性注入的处理发生在AbstractAutoWireCapableBeanFactory类中的populateBean方法中，我们通过程序流程分析autowiring的实现原理。

#### 　　(1). AbstractAutoWireCapableBeanFactory对Bean实例进行属性依赖注入

　　应用第一次通过getBean方法(配置了lazy-init预实例化属性的除外)向IOC容器索取Bean时，容器创建Bean实例对象，并且对Bean实例对象进行属性依赖注入，AbstractAutoWireCapableBeanFactory的性依赖注入的功能，其主要源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
protected void populateBean(String beanName, AbstractBeanDefinition mbd, BeanWrapper bw) {  
   //获取Bean定义的属性值，并对属性值进行处理  
   PropertyValues pvs = mbd.getPropertyValues();  
   ……  
   //对依赖注入处理，首先处理autowiring自动装配的依赖注入  
   if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_NAME ||  
           mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_TYPE) {  
       MutablePropertyValues newPvs = new MutablePropertyValues(pvs);  
       //根据Bean名称进行autowiring自动装配处理  
       if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_NAME) {  
           autowireByName(beanName, mbd, bw, newPvs);  
       }  
       //根据Bean类型进行autowiring自动装配处理  
       if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_TYPE) {  
           autowireByType(beanName, mbd, bw, newPvs);  
       }  
   }  
   //对非autowiring的属性进行依赖注入处理  
    ……  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

#### 　　(2). Spring IOC容器根据Bean名称或者类型进行autowiring自动依赖注入

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//根据名称对属性进行自动依赖注入  
protected void autowireByName(  
       String beanName, AbstractBeanDefinition mbd, BeanWrapper bw, MutablePropertyValues pvs) {  
    //对Bean对象中非简单属性(不是简单继承的对象，如8中原始类型，字符串，URL等//都是简单属性)进行处理  
   String[] propertyNames = unsatisfiedNonSimpleProperties(mbd, bw);  
   for (String propertyName : propertyNames) {  
       //如果Spring IOC容器中包含指定名称的Bean  
       if (containsBean(propertyName)) {  
           //调用getBean方法向IoC容器索取指定名称的Bean实例，迭代触发属性的//初始化和依赖注入  
           Object bean = getBean(propertyName);  
           //为指定名称的属性赋予属性值  
           pvs.add(propertyName, bean);  
           //指定名称属性注册依赖Bean名称，进行属性依赖注入  
           registerDependentBean(propertyName, beanName);  
           if (logger.isDebugEnabled()) {  
               logger.debug("Added autowiring by name from bean name '" + beanName +  
                       "' via property '" + propertyName + "' to bean named '" + propertyName + "'");  
           }  
       }  
       else {  
           if (logger.isTraceEnabled()) {  
               logger.trace("Not autowiring property '" + propertyName + "' of bean '" + beanName +  
                       "' by name: no matching bean found");  
           }  
       }  
   }  
}  
//根据类型对属性进行自动依赖注入  
protected void autowireByType(  
       String beanName, AbstractBeanDefinition mbd, BeanWrapper bw, MutablePropertyValues pvs) {  
   //获取用户定义的类型转换器  
   TypeConverter converter = getCustomTypeConverter();  
   if (converter == null) {  
       converter = bw;  
   }  
   //存放解析的要注入的属性  
   Set<String> autowiredBeanNames = new LinkedHashSet<String>(4);  
   //对Bean对象中非简单属性(不是简单继承的对象，如8中原始类型、字符、URL等都是简单属性)进行处理  
   String[] propertyNames = unsatisfiedNonSimpleProperties(mbd, bw);  
   for (String propertyName : propertyNames) {  
       try {  
           //获取指定属性名称的属性描述器  
           PropertyDescriptor pd = bw.getPropertyDescriptor(propertyName);  
           //不对Object类型的属性进行autowiring自动依赖注入  
           if (!Object.class.equals(pd.getPropertyType())) {  
               //获取属性的setter方法  
               MethodParameter methodParam = BeanUtils.getWriteMethodParameter(pd);  
               //检查指定类型是否可以被转换为目标对象的类型  
               boolean eager = !PriorityOrdered.class.isAssignableFrom(bw.getWrappedClass());  
               //创建一个要被注入的依赖描述  
               DependencyDescriptor desc = new AutowireByTypeDependencyDescriptor(methodParam, eager);  
               //根据容器的Bean定义解析依赖关系，返回所有要被注入的Bean对象  
               Object autowiredArgument = resolveDependency(desc, beanName, autowiredBeanNames, converter);  
               if (autowiredArgument != null) {  
                   //为属性赋值所引用的对象  
                   pvs.add(propertyName, autowiredArgument);  
               }  
               for (String autowiredBeanName : autowiredBeanNames) {  
                   //指定名称属性注册依赖Bean名称，进行属性依赖注入  
                   registerDependentBean(autowiredBeanName, beanName);  
                   if (logger.isDebugEnabled()) {  
                       logger.debug("Autowiring by type from bean name '" + beanName + "' via property '" +  
                               propertyName + "' to bean named '" + autowiredBeanName + "'");  
                   }  
               }  
               //释放已自动注入的属性  
               autowiredBeanNames.clear();  
           }  
       }  
       catch (BeansException ex) {  
           throw new UnsatisfiedDependencyException(mbd.getResourceDescription(), beanName, propertyName, ex);  
       }  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过上面的源码分析，我们可以看出来通过属性名进行自动依赖注入的相对比通过属性类型进行自动依赖注入要稍微简单一些，但是真正实现属性注入的是DefaultSingletonBeanRegistry类的registerDependentBean方法。

#### 　　(3). DefaultSingletonBeanRegistry的registerDependentBean方法对属性注入

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//为指定的Bean注入依赖的Bean  
public void registerDependentBean(String beanName, String dependentBeanName) {  
   //处理Bean名称，将别名转换为规范的Bean名称  
   String canonicalName = canonicalName(beanName);  
   //多线程同步，保证容器内数据的一致性 。先从容器中：bean名称-->全部依赖Bean名称集合找查找给定名称Bean的依赖Bean  
   synchronized (this.dependentBeanMap) {  
       //获取给定名称Bean的所有依赖Bean名称  
       Set<String> dependentBeans = this.dependentBeanMap.get(canonicalName);  
       if (dependentBeans == null) {  
           //为Bean设置依赖Bean信息  
           dependentBeans = new LinkedHashSet<String>(8);  
           this.dependentBeanMap.put(canonicalName, dependentBeans);  
       }  
       //向容器中：bean名称-->全部依赖Bean名称集合添加Bean的依赖信息，即：将Bean所依赖的Bean添加到容器的集合中  
       dependentBeans.add(dependentBeanName);  
   }  
   //从容器中：bean名称-->指定名称Bean的依赖Bean集合找查找给定名称Bean的依赖Bean  
   synchronized (this.dependenciesForBeanMap) {  
       Set<String> dependenciesForBean = this.dependenciesForBeanMap.get(dependentBeanName);  
       if (dependenciesForBean == null) {  
           dependenciesForBean = new LinkedHashSet<String>(8);  
           this.dependenciesForBeanMap.put(dependentBeanName, dependenciesForBean);  
       }  
       //向容器中：bean名称-->指定Bean的依赖Bean名称集合添加Bean的依赖信息。即：将Bean所依赖的Bean添加到容器的集合中  
       dependenciesForBean.add(canonicalName);  
   }  
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　通过对autowiring的源码分析，我们可以看出，autowiring的实现过程：

- a. 对Bean的属性迭代调用getBean方法，完成依赖Bean的初始化和依赖注入。
- b. 将依赖Bean的属性引用设置到被依赖的Bean属性上。
- c. 将依赖Bean的名称和被依赖Bean的名称存储在IOC容器的集合中。

　　Spring IoC容器的autowiring属性自动依赖注入是一个很方便的特性，可以简化开发时的配置，但是凡是都有两面性，自动属性依赖注入也有不足，首先，Bean的依赖关系在配置文件中无法很清楚地看出来，对于维护造成一定困难。其次，由于自动依赖注入是Spring容器自动执行的，容器是不会智能判断的，如果配置不当，将会带来无法预料的后果，所以自动依赖注入特性在使用时还是综合考虑。

### 　　6、多容器/父子容器概念

　　Spring 框架允许在一个应用中创建多个上下文容器。但是建议容器之间有父子关系。可以通过 ConfigurableApplicationContext 接口中定义的 setParent 方法设置父容器。一旦设置父 子关系，则**可以通过子容器获取父容器中除 PropertyPlaceHolder 以外的所有资源**，父容器不能获取子容器中的任意资源(类似 Java 中的类型继承)。

　　典型的父子容器: spring 和 springmvc 同时使用的时候。ContextLoaderListener 创建的容器是父容器，DispatcherServlet 创建的容器是子容器。

　　父子容器的存在是保证一个 JVM 中，只有一个树状结构的容器树,每个容器都有一定的作用范围和访问域，起到一个隔离的作用。可以通过子容器访问父容器资源。

### 　　7、p 域/c 域

　　Spring2.0 之后引入了 p(property 标签)域、Spring3.1 之后引入了 c(constractor-arg 标签) 域。可以简化配置文件中对 property 和 constructor-arg 的配置。

```
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.spring framework.org/schema /c" xmlns:p="http://www.sprin gframework.org/schema/p" xsi:schemaLocation="http://www.springframework.org /schema/beans
http://www.springframework.org /schema /beans/spri ng-bean s.xsd">
　　<bean id="oneBean" class="com.sxt.OneBean" p:a="10" p:o-ref="otherBean" c:a="20" c:o-ref="otherBean"/>
　　<bean id="otherBean" class="com.sxt.OtherBean" />
</beans>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class OneBean{ 
    int a;
    Object o;
    public OneBean(int a, Object o){ 
        this.a = a; 
        this.o = o;
    } // getters and setters for fields.
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 　　8、lookup-method

　　lookup-method 一旦应用，Spring 框架会自动使用 CGLIB 技术为指定类型创建一个动态子类型，并自动实现抽象方法。可以动态的实现依赖注入的数据准备。

　　在效率上，比直接自定义子类型慢。相对来说更加通用。可以只提供 lookup-method 方法的返回值对象即可实现动态的对象返回。

　　在工厂方法难以定制的时候使用，也是模板的一种应用，工厂方法的扩展。如:工厂方法返回对象类型为接口类型，且不同版本应用返回的对象未必相同时使用，可以避免多次开发工厂类。如：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestLookupMethod {
    public static void main(String[] args) {
        ApplicationContext context =
                new ClassPathXmlApplicationContext("classpath:lookupmethod/applicationContext.xml");

        CommandManager manager = context.getBean("manager", CommandManager.class);
    
        System.out.println(manager.getClass().getName());
        manager.process();
    }
}

abstract class CommandManager {
    public void process() {
        MyCommand command = createCommand();
        // do something ...
        System.out.println(command);
    }

    protected abstract MyCommand createCommand();
}

interface MyCommand {
}

class MyCommand1 implements MyCommand {
    public MyCommand1() {
        System.out.println("MyCommand1 instanced");
    }
}

class MyCommand2 implements MyCommand {
    public MyCommand2() {
        System.out.println("MyCommand2 instanced");
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　applicationContext.xml中配置为：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="manager" class="com.sxt.lookupmethod.CommandManager">
        <lookup-method bean="command2" name="createCommand"/>
    </bean>
    
    <bean id="command1" class="com.sxt.lookupmethod.MyCommand1"></bean>
    <bean id="command2" class="com.sxt.lookupmethod.MyCommand2"></bean>

</beans>
>>>>>>> 1bceba3b0817dd12b5184b5608d4687460c549ab

```

```



Spring 最重要的概念就是 IOC 和 AOP，本文章适合于有一定的Spring使用基础的同学查看，阅读本文不会让你成为spring这方面的专家，但是可以增加你对Spring IOC 的工作原理和创建Bean 过程的理解。

本文章Spring 的版本是5.2.5.RELEASE， 原文是4.2.4.RELEASE。其实5 和 4 没有太大区别，不过在源码方面，还是有几处小的区别和优化。

本文只是对SpringIOC 源码解读，了解其工作原理，方面我们以后的工作和学习。

# 目录

# 引言

先看下最基本的启动 Spring 容器的例子：

```javascript
public static void main(String[] args) {
	ApplicationContext context = new ClassPathXmlApplicationContext("classpath:applicationfile.xml");
}
123
```

以上代码就可以利用配置文件来启动一个 Spring 容器了，请使用 maven 的小伙伴直接在 dependencies 5.2.5中加上以下依赖即可，
我比较反对那些不知道要添加什么依赖，然后把 Spring 的所有相关的东西都加进来的方式。

```javascript
 <properties>
	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	<maven.compiler.source>12</maven.compiler.source>
	<maven.compiler.target>12</maven.compiler.target>
	<junit.version>4.12</junit.version>
	<lombok.version>1.18.10</lombok.version>
	<log4j.version>1.2.17</log4j.version>
	<mysql.version>8.0.18</mysql.version>
	<druid.version>1.1.16</druid.version>
	<mybatis.spring.boot.version>2.1.1</mybatis.spring.boot.version>
</properties>
<dependencies>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-beans</artifactId>
		<version>5.2.5.RELEASE</version>
	</dependency>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-core</artifactId>
		<version>5.2.5.RELEASE</version>
	</dependency>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-context</artifactId>
		<version>5.2.5.RELEASE</version>
	</dependency>
	<dependency>
		<groupId>cglib</groupId>
		<artifactId>cglib</artifactId>
		<version>2.2.2</version>
	</dependency>
</dependencies>
123456789101112131415161718192021222324252627282930313233
```

> 

```
spring-context 会自动将 spring-core、spring-beans、spring-aop、spring-expression 这几个基础 jar 包带进来。
1
```

多说一句，很多开发者入门就直接接触的 SpringMVC，对 Spring 其实不是很了解，Spring 是渐进式的工具，并不具有很强的侵入性，它的模块也划分得很合理，即使你的应用不是 web 应用，或者之前完全没有使用到 Spring，而你就想用 Spring 的依赖注入这个功能，其实完全是可以的，它的引入不会对其他的组件产生冲突。
废话说完，我们继续。ApplicationContext context = new ClassPathXmlApplicationContext(…) 其实很好理解，从名字上就可以猜出一二，就是在 ClassPath 中寻找 xml 配置文件，根据 xml 文件内容来构建 ApplicationContext。当然，除了 ClassPathXmlApplicationContext 以外，我们也还有其他构建 ApplicationContext 的方案可供选择，我们先来看看大体的继承结构是怎么样的：

![图片](https://img-blog.csdnimg.cn/20201013202125550.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNzczMDI2,size_16,color_FFFFFF,t_70#pic_center)

> 读者可以大致看一下类名，源码分析的时候不至于找不着看哪个类，因为 Spring 为了适应各种使用场景，提供的各个接口都可能有很多的实现类。对于我们来说，
> 就是揪着一个完整的分支看完。当然，读本文的时候读者也不必太担心，每个代码块分析的时候，我都会告诉读者我们在说哪个类第几行。

我们可以看到，ClassPathXmlApplicationContext 兜兜转转了好久才到 ApplicationContext 接口，同样的，我们也可以使用绿颜色的 FileSystemXmlApplicationContext 和 AnnotationConfigApplicationContext 这两个类。

**FileSystemXmlApplicationContext** 的构造函数需要一个 xml 配置文件在系统中的路径，其他和 ClassPathXmlApplicationContext 基本上一样。

**AnnotationConfigApplicationContext** 是基于注解来使用的，它不需要配置文件，采用 java 配置类和各种注解来配置，是比较简单的方式，也是大势所趋吧。

不过本文旨在帮助大家理解整个构建流程，所以决定使用 ClassPathXmlApplicationContext 进行分析。

我们先来一个简单的例子来看看怎么实例化 ApplicationContext。

首先，定义一个类：

```javascript
public class Video {
	private int id;
	private String title;
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getTitle() {
		return title;
	}
	public void setTitle(String title) {
		this.title = title;
	}
}
12345678910111213141516
```

接下来，我们在 resources 目录新建一个配置文件，文件名随意，通常叫 application.xml 或 application-xxx.xml就可以了：

```javascript
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="video" class="com.xlg.springSource.beans.Video">
        <property name="id" value="9"></property>
        <property name="title" value="Spring5.X课程"></property>
    </bean>
</beans>
12345678910
```

这样，我们就可以跑起来了：

```javascript
public class BeansSource {
    public static void main(String[] args) {
        ApplicationContext con = new ClassPathXmlApplicationContext("applicationContext.xml");
        Video video = (Video) con.getBean("video");
        System.out.println(video.getTitle() + video.getId());
    }
}
1234567
```

以上例子很简单，不过也够引出本文的主题了，就是怎么样通过配置文件来启动 Spring 的 ApplicationContext？也就是我们今天要分析的 IOC 的核心了.
ApplicationContext 启动过程中，会负责创建实例 Bean，往各个 Bean 中注入依赖等。

# BeanFactory简介

初学者可别以为我之前说那么多和 BeanFactory 无关，前面说的 ApplicationContext 其实就是一个 BeanFactory。
我们来看下和 BeanFactory 接口相关的主要的继承结构：

![beanFactory](https://img-blog.csdnimg.cn/20201013203056997.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNzczMDI2,size_16,color_FFFFFF,t_70#pic_center)

我想，大家看完这个图以后，可能就不是很开心了。ApplicationContext 往下的继承结构前面一张图说过了，这里就不重复了。这张图呢，背下来肯定是不需要的，有几个重点和大家说明下就好。

- ApplicationContext 继承了 ListableBeanFactory，这个 Listable 的意思就是，通过这个接口，我们可以获取多个 Bean，最顶层 BeanFactory 接口的方法都是获取单个 Bean 的。
- ApplicationContext 继承了 HierarchicalBeanFactory，Hierarchical 单词本身已经能说明问题了，也就是说我们可以在应用中起多个 BeanFactory，然后可以将各个 BeanFactory 设置为父子关系。
- AutowireCapableBeanFactory 这个名字中的 Autowire 大家都非常熟悉，它就是用来自动装配 Bean 用的，但是仔细看上图，ApplicationContext 并没有继承它，不过不用担心，不使用继承，不代表不可以使用组合，如果你看到 ApplicationContext 接口定义中的最后一个方法 getAutowireCapableBeanFactory() 就知道了。
- ConfigurableListableBeanFactory 也是一个特殊的接口，看图，特殊之处在于它继承了第二层所有的三个接口，而 ApplicationContext 没有。这点之后会用到。
- 请先不用花时间在其他的接口和类上，先理解我说的这几点就可以了。

然后，请读者打开编辑器，翻一下 BeanFactory、ListableBeanFactory、HierarchicalBeanFactory、AutowireCapableBeanFactory、ApplicationContext 这几个接口的代码，大概看一下各个接口中的方法，大家心里要有底，限于篇幅，我就不贴代码介绍了。

# 启动过程分析

下面将会是冗长的代码分析，请读者先喝个水。记住，一定要在电脑中打开源码，不然纯看是很累的。

第一步，我们肯定要从 ClassPathXmlApplicationContext 的构造方法说起。

```javascript
public class ClassPathXmlApplicationContext extends AbstractXmlApplicationContext {
  private Resource[] configResources;

  // 如果已经有 ApplicationContext 并需要配置成父子关系，那么调用这个构造方法
  public ClassPathXmlApplicationContext(ApplicationContext parent) {
	super(parent);
  }
  ...
  public ClassPathXmlApplicationContext(String[] configLocations, boolean refresh, ApplicationContext parent)
	  throws BeansException {

	super(parent);
	// 根据提供的路径，处理成配置文件数组，(以分号. 逗号. 空格. tab. 换行符分割)
	setConfigLocations(configLocations);
	if (refresh) {
	  refresh(); // 核心方法
	}
  }
	...
}

123456789101112131415161718192021
```

接下来，就是refresh, 这里简单说一下为什么是refresh()， 而不是init()这种名字的方法。从大多数博客来看都是这样说明的:
因为ApplicationContext 建立起来以后，其实我们是可以通过调用refresh() 这个方法重建的, refresh() 会将原来的ApplciationContext销毁
然后再重新执行一次初始化操作。

往下看，refresh()方法里面调用了那么多方法，就知道肯定不简单了，请读者先看个大概，细节之后会详细说明。

```javascript
	@Override
public void refresh() throws BeansException, IllegalStateException {

	// 来个同步锁，不然refresh()还么结束，你又来个启动或者销毁容器的操作，那不就乱套了
	synchronized (this.startupShutdownMonitor) {
	
		// 准备工作，记录下容器的启动时间,并标记"已启动" 状态，处理配置文件中的占位符
		prepareRefresh();

		
		//  这步比较关键，这步完成后，配置文件就会解析成一个个Bean定义，注册到BeanFactory中
		// 当然，这里说的Bean还没有初始化，只是配置信息都提取出来了，
		// 注册也只是将这些信息都保存到了注册中心(说到底核心是一个 beadName->beanDefinition的 map)
		ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

		// 设置BeanFactory 的类加载器，添加几个BeanPostProcessor, 手动注册几个特殊的bean
		// 这块待会说
		prepareBeanFactory(beanFactory);

		try {
			// 【这里需要知道BeanFactoryPostProcessor 这个知识点， bean如果实现了这个借口
			//  那么在容器初始化以后，Spring会负责调用里面的postProcessBeanFactory方法。】
		
			//  这里是提供给子类的扩展点，到这里的时候，所有的bean都加载，注册完成了，但是没有进行初始化
			//  具体的子类可以在这步的时候添加一些特殊的BeanFactoryPostProcessor的实现类或者其他什么事情
			postProcessBeanFactory(beanFactory);

			// 直接执行了，，，调用beanFactoryPostProcessor 各个实现类的postProcessBeanFactory(factory)方法
			invokeBeanFactoryPostProcessors(beanFactory);


	
			// 注册BeanPostProcessor的实现类，注意看和BeanFactoryPostProcessor的区别
			// 此接口两个方法： postProcessBeforeInitialization 和postProcessorAfterInitialization
			// 两个方法分别在 Bean初始化之前和初始化之后得到执行。 注意： 到这里，Bean还没有初始化
			registerBeanPostProcessors(beanFactory);

			// 初始化当前ApplicationContext的 MessageSource, 国际化这里就不展开了，，不然是没完没了
			initMessageSource();

			// 初始化ApplicationContext的 事件广播器，这里也不展开了
			initApplicationEventMulticaster();


			// 从方法名就可以知道，典型的模板方法(钩子方法)， 
			// 具体的子类可以在这里初始化一些特殊的 Bean (在初始化 singleton beans之前)
			onRefresh();

			// 注册事件监听器，监听器需要实现 ApplicationListener接口。 这并不是我们的重点
			registerListeners();


			// 重点  重点 重点 
			// 初始化所有的 singleton beans
			// ( lazy-init 的除外)
			finishBeanFactoryInitialization(beanFactory);

			// 最后，广播事件， ApplicationContext 初始化完成 
			finishRefresh();
		}

		catch (BeansException ex) {
			if (logger.isWarnEnabled()) {
				logger.warn("Exception encountered during context initialization - " +
						"cancelling refresh attempt: " + ex);
			}

			// Destroy already created singletons to avoid dangling resources.
			// 销毁已经初始化的singleton 的Beans， 以免有些bean 会一直占用资源 
			destroyBeans();

			// Reset 'active' flag.
			cancelRefresh(ex);

			// 把异常向外抛出
			throw ex;
		}

		finally {
			// Reset common introspection caches in Spring's core, since we
			// might not ever need metadata for singleton beans anymore...
			resetCommonCaches();
		}
	}
	
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667686970717273747576777879808182838485
```

下面，我们开始一步步来肢解这个 refresh() 方法。

## 1-Bean容器前的准备工作

```
这个比较简单，直接看代码中的几个注释即可。
1
protected void prepareRefresh() {
	//记录 启动时间 
	// 将 active 属性设置为 true， closed 属性设置为false，它们都是 AtomicBoolean 类型
	this.startupDate = System.currentTimeMillis();
	this.closed.set(false);
	this.active.set(true);

	if (logger.isDebugEnabled()) {
		if (logger.isTraceEnabled()) {
			logger.trace("Refreshing " + this);
		}
		else {
			logger.debug("Refreshing " + getDisplayName());
		}
	}

	// Initialize any placeholder property sources in the context environment.
	initPropertySources();

	// 校验 xml 配置文件
	getEnvironment().validateRequiredProperties();

	if (this.earlyApplicationListeners == null) {
		this.earlyApplicationListeners = new LinkedHashSet<>(this.applicationListeners);
	}
	else {
		this.applicationListeners.clear();
		this.applicationListeners.addAll(this.earlyApplicationListeners);
	}
	this.earlyApplicationEvents = new LinkedHashSet<>();
}	
12345678910111213141516171819202122232425262728293031
```

## 2-Bean容器,加载并注册Bean

```
我们回到refresh() 方法中的下一行，obtainFreshBeanFactory() 方法
注意：这个方法是全文最重要的部分之一，这里将会初始化 BeanFactory、加载 Bean、注册 Bean 等等。
    
当然，这步结束后，Bean 并没有完成初始化。这里指的是 Bean 实例并未在这一步生成。
1234
```

// AbstractApplicationContext.java

```javascript
protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {
	// 关闭旧的 BeanFactory(如果有)，创建新的BeanFactory， 加载bean定义，注册bean等等
	refreshBeanFactory();
	
	// 返回刚刚创建的 BeanFactory
	return getBeanFactory();
}
// getBeanFactory
synchronized (this.beanFactoryMonitor) {
	if (this.beanFactory == null) {
		throw new IllegalStateException("BeanFactory not initialized or already closed - " +
				"call 'refresh' before accessing beans via the ApplicationContext");
	}
	return this.beanFactory;
}
123456789101112131415
```

// AbstractRefreshableApplicationContext.java 120
refreshBeanFactory

```javascript
@Override
protected final void refreshBeanFactory() throws BeansException {
	// 如果 ApplicationContext 中已经加载过 BeanFactory 了，销毁所有Bean，关闭BeanFactory
	// 注意, 应用中 BeanFactory 本来就是可以多个的，这里可不是说应用全局是否有BeanFactory，而是当前
	// ApplicationContext 是否有 BeanFactory
	if (hasBeanFactory()) {
		destroyBeans();
		closeBeanFactory();
	}
	try {
		// 初始化一个 DefaultListableBeanFactory ， 为什么用这个，一会说。
		DefaultListableBeanFactory beanFactory = createBeanFactory();
		// 用于 BeanFactory的 序列化 ， 我想部分人应该用不到
		beanFactory.setSerializationId(getId());
		
		
		// 下面这两个方法非常重要，别跟丢了，具体细节之后说
		// 设置BeanFactory 的两个配置属性；是否允许 Bean 覆盖，是否允许循环引用
		customizeBeanFactory(beanFactory);
		
		
		// 加载 Bean 到 BeanFactory 中
		loadBeanDefinitions(beanFactory);
		synchronized (this.beanFactoryMonitor) {
			this.beanFactory = beanFactory;
		}
	}
	catch (IOException ex) {
		throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);
	}
}
12345678910111213141516171819202122232425262728293031
```

> 

```
看到这里的时候，我觉得读者就应该站在高处看ApplicationContext 了， ApplicationContext 继承自 BeanFactory，
但是 它不应该被 理解为BeanFactory的实现类，而是说其内部持有一个实例化的BeanFactory (DefaultListableBeanFactory).
以后所有的BeanFactory 相关的操作其实是委托给这个实例来处理的。
123
```

我们说说为什么选择实例化 DefaultListableBeanFactory ？前面我们说了有个很重要的接口 ConfigurableListableBeanFactory，它实现了 BeanFactory 下面一层的所有三个接口，我把之前的继承图再拿过来大家再仔细看一下：

![beanFactory](https://img-blog.csdnimg.cn/20201013203056997.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNzczMDI2,size_16,color_FFFFFF,t_70#pic_center)

我们可以看到 ConfigurableListableBeanFactory 只有一个实现类 DefaultListableBeanFactory，而且实现类 DefaultListableBeanFactory 还通过实现右边的 AbstractAutowireCapableBeanFactory 通吃了右路。所以结论就是，最底下这个家伙 DefaultListableBeanFactory 基本上是最牛的 BeanFactory 了，这也是为什么这边会使用这个类来实例化的原因。

在继续往下之前，我们需要先了解 BeanDefinition。我们说** BeanFactory 是 Bean 容器，那么 Bean 又是什么呢？**

这里的 BeanDefinition 就是我们所说的 Spring 的 Bean，我们自己定义的各个 Bean 其实会转换成一个个 BeanDefinition 存在于 Spring 的 BeanFactory 中。
所以，如果有人问你 Bean 是什么的时候，你要知道 Bean 在代码层面上是 BeanDefinition 的实例

> 

BeanDefinition 中保存了我们的 Bean 信息，比如这个 Bean 指向的是哪个类、是否是单例的、是否懒加载、这个 Bean 依赖了哪些 Bean 等等。

### 2-1-BeanDefinition接口定义

我们来看下 BeanDefinition 的接口定义：

```javascript
public interface BeanDefinition extends AttributeAccessor, BeanMetadataElement {

   // 我们可以看到，默认只提供 sington 和 prototype 两种，
   // 很多读者可能知道还有 request, session, globalSession, application, websocket 这几种，
   // 不过，它们属于基于 web 的扩展。
   String SCOPE_SINGLETON = ConfigurableBeanFactory.SCOPE_SINGLETON;
   String SCOPE_PROTOTYPE = ConfigurableBeanFactory.SCOPE_PROTOTYPE;

   // 比较不重要，直接跳过吧
   int ROLE_APPLICATION = 0;
   int ROLE_SUPPORT = 1;
   int ROLE_INFRASTRUCTURE = 2;

   // 设置父 Bean，这里涉及到 bean 继承，不是 java 继承。请参见附录的详细介绍
   // 一句话就是：继承父 Bean 的配置信息而已
   void setParentName(String parentName);

   // 获取父 Bean
   String getParentName();

   // 设置 Bean 的类名称，将来是要通过反射来生成实例的
   void setBeanClassName(String beanClassName);

   // 获取 Bean 的类名称
   String getBeanClassName();


   // 设置 bean 的 scope
   void setScope(String scope);

   String getScope();

   // 设置是否懒加载
   void setLazyInit(boolean lazyInit);

   boolean isLazyInit();

   // 设置该 Bean 依赖的所有的 Bean，注意，这里的依赖不是指属性依赖(如 @Autowire 标记的)，
   // 是 depends-on="" 属性设置的值。
   void setDependsOn(String... dependsOn);

   // 返回该 Bean 的所有依赖
   String[] getDependsOn();

   // 设置该 Bean 是否可以注入到其他 Bean 中，只对根据类型注入有效，
   // 如果根据名称注入，即使这边设置了 false，也是可以的
   void setAutowireCandidate(boolean autowireCandidate);

   // 该 Bean 是否可以注入到其他 Bean 中
   boolean isAutowireCandidate();

   // 主要的。同一接口的多个实现，如果不指定名字的话，Spring 会优先选择设置 primary 为 true 的 bean
   void setPrimary(boolean primary);

   // 是否是 primary 的
   boolean isPrimary();

   // 如果该 Bean 采用工厂方法生成，指定工厂名称。对工厂不熟悉的读者，请参加附录
   // 一句话就是：有些实例不是用反射生成的，而是用工厂模式生成的
   void setFactoryBeanName(String factoryBeanName);
   // 获取工厂名称
   String getFactoryBeanName();
   // 指定工厂类中的 工厂方法名称
   void setFactoryMethodName(String factoryMethodName);
   // 获取工厂类中的 工厂方法名称
   String getFactoryMethodName();

   // 构造器参数
   ConstructorArgumentValues getConstructorArgumentValues();

   // Bean 中的属性值，后面给 bean 注入属性值的时候会说到
   MutablePropertyValues getPropertyValues();

   // 是否 singleton
   boolean isSingleton();

   // 是否 prototype
   boolean isPrototype();

   // 如果这个 Bean 是被设置为 abstract，那么不能实例化，
   // 常用于作为 父bean 用于继承，其实也很少用......
   boolean isAbstract();

   int getRole();
   String getDescription();
   String getResourceDescription();
   BeanDefinition getOriginatingBeanDefinition();
}
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667686970717273747576777879808182838485868788
```

> 这个 BeanDefinition 其实已经包含很多的信息了，暂时不清楚所有的方法对应什么东西没关系，希望看完本文后读者
> 可以彻底搞清楚里面的所有东西。

> 这里接口虽然那么多，但是没有类似 getInstance() 这种方法来获取我们定义的类的实例，真正的我们定义的类生成的实例到哪里去了呢？别着急，这个要很后面才能讲到。

有了 BeanDefinition 的概念以后，我们再往下看 refreshBeanFactory() 方法中的剩余部分：

```javascript
customizeBeanFactory(beanFactory);
loadBeanDefinitions(beanFactory);
12
```

虽然只有两个方法，但路还很长啊。。。

### 2-2-customizeBeanFactory

customizeBeanFactory

customizeBeanFactory(beanFactory) 比较简单，就是配置是否允许 BeanDefinition 覆盖、是否允许循环引用。

```javascript
protected void customizeBeanFactory(DefaultListableBeanFactory beanFactory) {
	if (this.allowBeanDefinitionOverriding != null) {
		// 是否允许 Bean 定义覆盖
		beanFactory.setAllowBeanDefinitionOverriding(this.allowBeanDefinitionOverriding);
	}
	if (this.allowCircularReferences != null) {
		// 是否允许 Bean 间的循环依赖
		beanFactory.setAllowCircularReferences(this.allowCircularReferences);
	}
}
12345678910
```

BeanDefinition 的覆盖问题大家也许会碰到，就是在配置文件中定义 bean 时使用了相同的 id 或 name，默认情况下，allowBeanDefinitionOverriding 属性为 null，如果在同一配置文件中重复了，会抛错，但是如果不是同一配置文件中，会发生覆盖。

循环引用也很好理解：A 依赖 B，而 B 依赖 A。或 A 依赖 B，B 依赖 C，而 C 依赖 A。

默认情况下，Spring 允许循环依赖，当然如果你在 A 的构造方法中依赖 B，在 B 的构造方法中依赖 A 是不行的。

至于这两个属性怎么配置？我在附录中进行了介绍，尤其对于覆盖问题，很多人都希望禁止出现 Bean 覆盖，可是 Spring 默认是不同文件的时候可以覆盖的。

之后的源码中还会出现这两个属性，读者有个印象就可以了。

### 2-3-loadBeanDefinitions

接下来是最重要的 loadBeanDefinitions(beanFactory) 方法了，这个方法将根据配置，加载各个 Bean，然后放到 BeanFactory 中。

读取配置的操作在 XmlBeanDefinitionReader 中，其负责加载配置、解析。

// AbstractXmlApplicationContext.java 80

```javascript
///**  我们可以看到，此方法将通过一个 XmlBeanDefinitionReader 实例来加载各个Bean 。*/
Override      
protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) throws BeansException, IOException {
	// 给这个 BeanFactory 实例化一个 XmlBeanDefinitionReader
	XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);

	// Configure the bean definition reader with this context's
	// resource loading environment.
	beanDefinitionReader.setEnvironment(this.getEnvironment());
	beanDefinitionReader.setResourceLoader(this);
	beanDefinitionReader.setEntityResolver(new ResourceEntityResolver(this));

	//  初始化 BeanDefinitionReader, 其实这个是提供给子类覆写的，
	//  我看了一下，没有类覆写这个方法，我们姑且当做不重要吧
	initBeanDefinitionReader(beanDefinitionReader);
	// 重点来了，，继续向下
	loadBeanDefinitions(beanDefinitionReader);
}
123456789101112131415161718
```

现在还在这个类中，接下来用刚刚初始化的 Reader 开始来加载 xml 配置，这块代码读者可以选择性跳过，不是很重要。也就是说，下面这个代码块，读者可以很轻松地略过。
loadBeanDefinitions

```javascript
protected void loadBeanDefinitions(XmlBeanDefinitionReader reader) throws BeansException, IOException {
   Resource[] configResources = getConfigResources();
   if (configResources != null) {
	  // 往下看
	  reader.loadBeanDefinitions(configResources);
   }
   String[] configLocations = getConfigLocations();
   if (configLocations != null) {
	  // 2
	  reader.loadBeanDefinitions(configLocations);
   }
}
123456789101112
```

// 上面虽然有两个分支，不过第二个分支很快通过解析路径转换为 Resource 以后也会进到这里

```
@Override
public int loadBeanDefinitions(Resource... resources) throws BeanDefinitionStoreException {
   Assert.notNull(resources, "Resource array must not be null");
   int counter = 0;
   // 注意这里是个 for 循环，也就是每个文件是一个 resource
   for (Resource resource : resources) {
	  // 继续往下看
	  counter += loadBeanDefinitions(resource);
   }
   // 最后返回 counter，表示总共加载了多少的 BeanDefinition
   return counter;
}

// XmlBeanDefinitionReader 303
@Override
public int loadBeanDefinitions(Resource resource) throws BeanDefinitionStoreException {
   return loadBeanDefinitions(new EncodedResource(resource));
}

// XmlBeanDefinitionReader 314
public int loadBeanDefinitions(EncodedResource encodedResource) throws BeanDefinitionStoreException {
   Assert.notNull(encodedResource, "EncodedResource must not be null");
   if (logger.isInfoEnabled()) {
	  logger.info("Loading XML bean definitions from " + encodedResource.getResource());
   }
   // 用一个 ThreadLocal 来存放配置文件资源
   Set<EncodedResource> currentResources = this.resourcesCurrentlyBeingLoaded.get();
   if (currentResources == null) {
	  currentResources = new HashSet<EncodedResource>(4);
	  this.resourcesCurrentlyBeingLoaded.set(currentResources);
   }
   if (!currentResources.add(encodedResource)) {
	  throw new BeanDefinitionStoreException(
			"Detected cyclic loading of " + encodedResource + " - check your import definitions!");
   }
   try {
	  InputStream inputStream = encodedResource.getResource().getInputStream();
	  try {
		 InputSource inputSource = new InputSource(inputStream);
		 if (encodedResource.getEncoding() != null) {
			inputSource.setEncoding(encodedResource.getEncoding());
		 }
		 // 核心部分是这里，往下面看
		 return doLoadBeanDefinitions(inputSource, encodedResource.getResource());
	  }
	  finally {
		 inputStream.close();
	  }
   }
   catch (IOException ex) {
	  throw new BeanDefinitionStoreException(
			"IOException parsing XML document from " + encodedResource.getResource(), ex);
   }
   finally {
	  currentResources.remove(encodedResource);
	  if (currentResources.isEmpty()) {
		 this.resourcesCurrentlyBeingLoaded.remove();
	  }
   }
}

// 还在这个文件中，第 388 行
protected int doLoadBeanDefinitions(InputSource inputSource, Resource resource)
	  throws BeanDefinitionStoreException {
   try {
	  // 这里就不看了，将 xml 文件转换为 Document 对象
	  Document doc = doLoadDocument(inputSource, resource);
	  // 继续
	  return registerBeanDefinitions(doc, resource);
   }
   catch (...
}
// 还在这个文件中，第 505 行
// 返回值：返回从当前配置文件加载了多少数量的 Bean
public int registerBeanDefinitions(Document doc, Resource resource) throws BeanDefinitionStoreException {
   BeanDefinitionDocumentReader documentReader = createBeanDefinitionDocumentReader();
   int countBefore = getRegistry().getBeanDefinitionCount();
   // 这里
   documentReader.registerBeanDefinitions(doc, createReaderContext(resource));
   return getRegistry().getBeanDefinitionCount() - countBefore;
}
// DefaultBeanDefinitionDocumentReader 90
@Override
public void registerBeanDefinitions(Document doc, XmlReaderContext readerContext) {
   this.readerContext = readerContext;
   logger.debug("Loading bean definitions");
   Element root = doc.getDocumentElement();
   // 从 xml 根节点开始解析文件
   doRegisterBeanDefinitions(root);
}         
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990
```

经过漫长的链路，一个配置文件终于转换为一颗 DOM 树了，注意，这里指的是其中一个配置文件，不是所有的，读者可以看到上面有个 for 循环的。下面从根节点开始解析：

#### 2-3-1-doRegisterBeanDefinitions

// DefaultBeanDefinitionDocumentReader 116

```javascript
protected void doRegisterBeanDefinitions(Element root) {
	// 我们看名字就知道，BeanDefinitionParserDelegate 必定是一个重要的类，它负责解析 Bean 定义，
   // 这里为什么要定义一个 parent? 看到后面就知道了，是递归问题，
   // 因为 <beans /> 内部是可以定义 <beans /> 的，所以这个方法的 root 其实不一定就是 xml 的根节点，也可以是嵌套在里面的 <beans /> 节点，从源码分析的角度，我们当做根节点就好了
   BeanDefinitionParserDelegate parent = this.delegate;
   this.delegate = createDelegate(getReaderContext(), root, parent);

   if (this.delegate.isDefaultNamespace(root)) {
	  // 这块说的是根节点 <beans ... profile="dev" /> 中的 profile 是否是当前环境需要的，
	  // 如果当前环境配置的 profile 不包含此 profile，那就直接 return 了，不对此 <beans /> 解析
	  // 不熟悉 profile 为何物，不熟悉怎么配置 profile 读者的请移步附录区
	  String profileSpec = root.getAttribute(PROFILE_ATTRIBUTE);
	  if (StringUtils.hasText(profileSpec)) {
		 String[] specifiedProfiles = StringUtils.tokenizeToStringArray(
			   profileSpec, BeanDefinitionParserDelegate.MULTI_VALUE_ATTRIBUTE_DELIMITERS);
		 if (!getReaderContext().getEnvironment().acceptsProfiles(specifiedProfiles)) {
			if (logger.isInfoEnabled()) {
			   logger.info("Skipped XML bean definition file due to specified profiles [" + profileSpec +
					 "] not matching: " + getReaderContext().getResource());
			}
			return;
		 }
	  }
   }

   preProcessXml(root); // 钩子
   // 往下看
   parseBeanDefinitions(root, this.delegate);
   postProcessXml(root); // 钩子

   this.delegate = parent;
}
1234567891011121314151617181920212223242526272829303132
```

preProcessXml(root) 和 postProcessXml(root) 是给子类用的钩子方法，鉴于没有被使用到，也不是我们的重点，我们直接跳过。

这里涉及到了 profile 的问题，对于不了解的读者，我在附录中对 profile 做了简单的解释，读者可以参考一下。

接下来，看核心解析方法 parseBeanDefinitions(root, this.delegate) :

// default namespace 涉及到的就四个标签 、、 和 ，
// 其他的属于 custom 的

```javascript
protected void parseBeanDefinitions(Element root, BeanDefinitionParserDelegate delegate) {
   if (delegate.isDefaultNamespace(root)) {
	  NodeList nl = root.getChildNodes();
	  for (int i = 0; i < nl.getLength(); i++) {
		 Node node = nl.item(i);
		 if (node instanceof Element) {
			Element ele = (Element) node;
			if (delegate.isDefaultNamespace(ele)) {
			   // 解析 default namespace 下面的几个元素
			   parseDefaultElement(ele, delegate);
			}
			else {
			   // 解析其他 namespace 的元素
			   delegate.parseCustomElement(ele);
			}
		 }
	  }
   }
   else {
	  delegate.parseCustomElement(root);
   }
}
12345678910111213141516171819202122
```

从上面的代码，我们可以看到，对于每个配置来说，分别进入到 parseDefaultElement(ele, delegate); 和 delegate.parseCustomElement(ele); 这两个分支了。

parseDefaultElement(ele, delegate) 代表解析的节点是 、、、 这几个

> 这里的四个标签之所以是 default 的，是因为它们是处于这个 namespace 下定义的：
> http://www.springframework.org/schema/beans

```
又到初学者科普时间，不熟悉 namespace 的读者请看下面贴出来的 xml，这里的第二行 xmlns 就是咯。

<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	   xmlns="http://www.springframework.org/schema/beans"
	   xsi:schemaLocation="
			http://www.springframework.org/schema/beans
		  http://www.springframework.org/schema/beans/spring-beans.xsd"
	   default-autowire="byName">
而对于其他的标签，将进入到 delegate.parseCustomElement(element) 这个分支。如我们经常会使用到的 <mvc />、<task />、<context />、<aop />等。

这些属于扩展，如果需要使用上面这些 ”非 default“ 标签，那么上面的 xml 头部的地方也要引入相应的 namespace 和 .xsd 文件的路径，如下所示。同时代码中需要提供相应的 parser 来解析，如 MvcNamespaceHandler、TaskNamespaceHandler、ContextNamespaceHandler、AopNamespaceHandler 等。

假如读者想分析 <context:property-placeholder location="classpath:xx.properties" /> 的实现原理，就应该到 ContextNamespaceHandler 中找答案。

<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	  xmlns="http://www.springframework.org/schema/beans"
	  xmlns:context="http://www.springframework.org/schema/context"
	  xmlns:mvc="http://www.springframework.org/schema/mvc"
	  xsi:schemaLocation="
		   http://www.springframework.org/schema/beans 
		   http://www.springframework.org/schema/beans/spring-beans.xsd
		   http://www.springframework.org/schema/context
		   http://www.springframework.org/schema/context/spring-context.xsd
		   http://www.springframework.org/schema/mvc   
		   http://www.springframework.org/schema/mvc/spring-mvc.xsd  
	   "
	  default-autowire="byName">
同理，以后你要是碰到 <dubbo /> 这种标签，那么就应该搜一搜是不是有 DubboNamespaceHandler 这个处理类。
12345678910111213141516171819202122232425262728
```

回过神来，看看处理 default 标签的方法：

```javascript
private void parseDefaultElement(Element ele, BeanDefinitionParserDelegate delegate) {
   if (delegate.nodeNameEquals(ele, IMPORT_ELEMENT)) {
	  // 处理 <import /> 标签
	  importBeanDefinitionResource(ele);
   }
   else if (delegate.nodeNameEquals(ele, ALIAS_ELEMENT)) {
	  // 处理 <alias /> 标签定义
	  // <alias name="fromName" alias="toName"/>
	  processAliasRegistration(ele);
   }
   else if (delegate.nodeNameEquals(ele, BEAN_ELEMENT)) {
	  // 处理 <bean /> 标签定义，这也算是我们的重点吧
	  processBeanDefinition(ele, delegate);
   }
   else if (delegate.nodeNameEquals(ele, NESTED_BEANS_ELEMENT)) {
	  // 如果碰到的是嵌套的 <beans /> 标签，需要递归
	  doRegisterBeanDefinitions(ele);
   }
}
12345678910111213141516171819
```

如果每个标签都说，那我不吐血，你们都要吐血了。我们挑我们的重点 标签出来说

#### 2-3-2-processBeanDefinition

processBeanDefinition

下面是 processBeanDefinition 解析 标签：

// DefaultBeanDefinitionDocumentReader 298

```javascript
protected void processBeanDefinition(Element ele, BeanDefinitionParserDelegate delegate) {
   // 将 <bean /> 节点中的信息提取出来，然后封装到一个 BeanDefinitionHolder 中，细节往下看
   // 解析 <bean /> 然后构建 BeanDefinitionHolder
   BeanDefinitionHolder bdHolder = delegate.parseBeanDefinitionElement(ele);

   // 下面的几行先不要看，跳过先，跳过先，跳过先，后面会继续说的

   if (bdHolder != null) {
	  bdHolder = delegate.decorateBeanDefinitionIfRequired(ele, bdHolder);
	  
	  
	  // 注册 bean 
	  try {
		 // Register the final decorated instance.
		 BeanDefinitionReaderUtils.registerBeanDefinition(bdHolder, getReaderContext().getRegistry());
	  }
	   
	  catch (BeanDefinitionStoreException ex) {
		 getReaderContext().error("Failed to register bean definition with name '" +
			   bdHolder.getBeanName() + "'", ele, ex);
	  }
	  // Send registration event.
	  getReaderContext().fireComponentRegistered(new BeanComponentDefinition(bdHolder));
   }
}
12345678910111213141516171819202122232425
```

继续往下看怎么解析之前，我们先看下 标签中可以定义哪些属性：

| **Property**             |
| ------------------------ |
| class                    |
| name                     |
| scope                    |
| constructor arguments    |
| properties               |
| autowiring mode          |
| lazy-initialization mode |
| initialization method    |
| destruction method       |

上面表格中的内容我想大家都非常熟悉吧，如果不熟悉，那就是你不够了解 Spring 的配置了。

简单地说就是像下面这样子：

```javascript
<bean id="exampleBean" name="name1, name2, name3" class="com.javadoop.ExampleBean"
	scope="singleton" lazy-init="true" init-method="init" destroy-method="cleanup">

	<!-- 可以用下面三种形式指定构造参数 -->
	<constructor-arg type="int" value="7500000"/>
	<constructor-arg name="years" value="7500000"/>
	<constructor-arg index="0" value="7500000"/>

	<!-- property 的几种情况 -->
	<property name="beanOne">
		<ref bean="anotherExampleBean"/>
	</property>
	<property name="beanTwo" ref="yetAnotherBean"/>
	<property name="integerProperty" value="1"/>
</bean>
123456789101112131415
```

当然，除了上面举例出来的这些，还有 factory-bean、factory-method、、、、 这几个，大家是不是熟悉呢？

有了以上这些知识以后，我们再继续往里看怎么解析 bean 元素，是怎么转换到 BeanDefinitionHolder 的。
// BeanDefinitionParserDelegate 428

// 解析 bean/> 标签

```javascript
public BeanDefinitionHolder parseBeanDefinitionElement(Element ele) {
	return parseBeanDefinitionElement(ele, null);
}

public BeanDefinitionHolder parseBeanDefinitionElement(Element ele, BeanDefinition containingBean) {
   String id = ele.getAttribute(ID_ATTRIBUTE);
   String nameAttr = ele.getAttribute(NAME_ATTRIBUTE);

   List<String> aliases = new ArrayList<String>();

   // 将 name 属性的定义按照 “逗号、分号、空格” 切分，形成一个 别名列表数组，
   // 当然，如果你不定义 name 属性的话，就是空的了
   // 我在附录中简单介绍了一下 id 和 name 的配置，大家可以看一眼，有个20秒就可以了
   if (StringUtils.hasLength(nameAttr)) {
	  String[] nameArr = StringUtils.tokenizeToStringArray(nameAttr, MULTI_VALUE_ATTRIBUTE_DELIMITERS);
	  aliases.addAll(Arrays.asList(nameArr));
   }

   String beanName = id;
   // 如果没有指定id, 那么用别名列表的第一个名字作为beanName
   if (!StringUtils.hasText(beanName) && !aliases.isEmpty()) {
	  beanName = aliases.remove(0);
	  if (logger.isDebugEnabled()) {
		 logger.debug("No XML 'id' specified - using '" + beanName +
			   "' as bean name and " + aliases + " as aliases");
	  }
   }

   if (containingBean == null) {
	  checkNameUniqueness(beanName, aliases, ele);
   }

   // 根据 <bean ...>...</bean> 中的配置创建 BeanDefinition，然后把配置中的信息都设置到实例中,
   // 细节后面细说，先知道下面这行结束后，一个 BeanDefinition 实例就出来了。
   AbstractBeanDefinition beanDefinition = parseBeanDefinitionElement(ele, beanName, containingBean);

   // 到这里，整个 <bean /> 标签就算解析结束了，一个 BeanDefinition 就形成了。
   if (beanDefinition != null) {
	  // 如果都没有设置 id 和 name，那么此时的 beanName 就会为 null，进入下面这块代码产生
	  // 如果读者不感兴趣的话，我觉得不需要关心这块代码，对本文源码分析来说，这些东西不重要
	  if (!StringUtils.hasText(beanName)) {
		 try {
			if (containingBean != null) {// 按照我们的思路，这里 containingBean 是 null 的
			   beanName = BeanDefinitionReaderUtils.generateBeanName(
					 beanDefinition, this.readerContext.getRegistry(), true);
			}
			else {
			   // 如果我们不定义 id 和 name，那么我们引言里的那个例子：
			   //   1. beanName 为：com.javadoop.example.MessageServiceImpl#0
			   //   2. beanClassName 为：com.javadoop.example.MessageServiceImpl

			   beanName = this.readerContext.generateBeanName(beanDefinition);

			   String beanClassName = beanDefinition.getBeanClassName();
			   if (beanClassName != null &&
					 beanName.startsWith(beanClassName) && beanName.length() > beanClassName.length() &&
					 !this.readerContext.getRegistry().isBeanNameInUse(beanClassName)) {
				  // 把 beanClassName 设置为 Bean 的别名
				  aliases.add(beanClassName);
			   }
			}
			if (logger.isDebugEnabled()) {
			   logger.debug("Neither XML 'id' nor 'name' specified - " +
					 "using generated bean name [" + beanName + "]");
			}
		 }
		 catch (Exception ex) {
			error(ex.getMessage(), ele);
			return null;
		 }
	  }
	  String[] aliasesArray = StringUtils.toStringArray(aliases);
	  // 返回 BeanDefinitionHolder
	  return new BeanDefinitionHolder(beanDefinition, beanName, aliasesArray);
   }

   return null;
}	
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778
```

看看怎么根据配置创建 BeanDefinition：

```javascript
public AbstractBeanDefinition parseBeanDefinitionElement(
	  Element ele, String beanName, BeanDefinition containingBean) {

   this.parseState.push(new BeanEntry(beanName));

   String className = null;
   if (ele.hasAttribute(CLASS_ATTRIBUTE)) {
	  className = ele.getAttribute(CLASS_ATTRIBUTE).trim();
   }

   try {
	  String parent = null;
	  if (ele.hasAttribute(PARENT_ATTRIBUTE)) {
		 parent = ele.getAttribute(PARENT_ATTRIBUTE);
	  }
	  // 创建 BeanDefinition，然后设置类信息而已，很简单，就不贴代码了
	  AbstractBeanDefinition bd = createBeanDefinition(className, parent);

	  // 设置 BeanDefinition 的一堆属性，这些属性定义在 AbstractBeanDefinition 中
	  parseBeanDefinitionAttributes(ele, beanName, containingBean, bd);
	  bd.setDescription(DomUtils.getChildElementValueByTagName(ele, DESCRIPTION_ELEMENT));

	  /**
	   * 下面的一堆是解析 <bean>......</bean> 内部的子元素，
	   * 解析出来以后的信息都放到 bd 的属性中
	   */

	  // 解析 <meta />
	  parseMetaElements(ele, bd);
	  // 解析 <lookup-method />
	  parseLookupOverrideSubElements(ele, bd.getMethodOverrides());
	  // 解析 <replaced-method />
	  parseReplacedMethodSubElements(ele, bd.getMethodOverrides());
	// 解析 <constructor-arg />
	  parseConstructorArgElements(ele, bd);
	  // 解析 <property />
	  parsePropertyElements(ele, bd);
	  // 解析 <qualifier />
	  parseQualifierElements(ele, bd);

	  bd.setResource(this.readerContext.getResource());
	  bd.setSource(extractSource(ele));

	  return bd;
   }
   catch (ClassNotFoundException ex) {
	  error("Bean class [" + className + "] not found", ele, ex);
   }
   catch (NoClassDefFoundError err) {
	  error("Class that bean class [" + className + "] depends on not found", ele, err);
   }
   catch (Throwable ex) {
	  error("Unexpected failure during bean definition parsing", ele, ex);
   }
   finally {
	  this.parseState.pop();
   }

   return null;
}
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960
```

到这里，我们已经完成了根据 配置创建了一个 BeanDefinitionHolder 实例。注意，是一个。

我们回到解析 的入口方法:

```javascript
protected void processBeanDefinition(Element ele, BeanDefinitionParserDelegate delegate) {
   // 将 <bean /> 节点转换为 BeanDefinitionHolder，就是上面说的一堆
   重要 
   BeanDefinitionHolder bdHolder = delegate.parseBeanDefinitionElement(ele);
   if (bdHolder != null) {
	  // 如果有自定义属性的话，进行相应的解析，先忽略
	  bdHolder = delegate.decorateBeanDefinitionIfRequired(ele, bdHolder);  
	  
	  重要 
	  try {
		 // 我们把这步叫做 注册Bean 吧
		 BeanDefinitionReaderUtils.registerBeanDefinition(bdHolder, getReaderContext().getRegistry());
	  }
  
	  catch (BeanDefinitionStoreException ex) {
		 getReaderContext().error("Failed to register bean definition with name '" +
			   bdHolder.getBeanName() + "'", ele, ex);
	  }
	  // 注册完成后，发送事件，本文不展开说这个
	  getReaderContext().fireComponentRegistered(new BeanComponentDefinition(bdHolder));
   }
}
12345678910111213141516171819202122
```

大家再仔细看一下这块吧，我们后面就不回来说这个了。这里已经根据一个 标签产生了一个 BeanDefinitionHolder 的实例，这个实例里面也就是一个 BeanDefinition 的实例和它的 beanName、aliases 这三个信息，注意，我们的关注点始终在 BeanDefinition 上：

```javascript
public class BeanDefinitionHolder implements BeanMetadataElement {

  private final BeanDefinition beanDefinition;

  private final String beanName;

  private final String[] aliases;
...
12345678
```

然后我们准备注册这个 BeanDefinition，最后，把这个注册事件发送出去。

#### 2-3-3-注册Bean

下面，我们开始说说注册 Bean 吧。

// BeanDefinitionReaderUtils 143

```javascript
public static void registerBeanDefinition(
	  BeanDefinitionHolder definitionHolder, BeanDefinitionRegistry registry)
	  throws BeanDefinitionStoreException {

   String beanName = definitionHolder.getBeanName();
   // 注册这个 Bean
   registry.registerBeanDefinition(beanName, definitionHolder.getBeanDefinition());

   // 如果还有别名的话，也要根据别名全部注册一遍，不然根据别名就会找不到 Bean 了
   String[] aliases = definitionHolder.getAliases();
   if (aliases != null) {
	  for (String alias : aliases) {
		 // alias -> beanName 保存它们的别名信息，这个很简单，用一个 map 保存一下就可以了，
		 // 获取的时候，会先将 alias 转换为 beanName，然后再查找
		 registry.registerAlias(beanName, alias);
	  }
   }
}
123456789101112131415161718
别名注册的放一边，毕竟它很简单，我们看看怎么注册 Bean。
1
```

// DefaultListableBeanFactory 793

```javascript
@Override
public void registerBeanDefinition(String beanName, BeanDefinition beanDefinition)
	  throws BeanDefinitionStoreException {

   Assert.hasText(beanName, "Bean name must not be empty");
   Assert.notNull(beanDefinition, "BeanDefinition must not be null");

   if (beanDefinition instanceof AbstractBeanDefinition) {
	  try {
		 ((AbstractBeanDefinition) beanDefinition).validate();
	  }
	  catch (BeanDefinitionValidationException ex) {
		 throw new BeanDefinitionStoreException(...);
	  }
   }

   // old? 还记得 “允许 bean 覆盖” 这个配置吗？allowBeanDefinitionOverriding
   BeanDefinition oldBeanDefinition;

   // 之后会看到，所有的 Bean 注册后会放入这个 beanDefinitionMap 中
   oldBeanDefinition = this.beanDefinitionMap.get(beanName);

   // 处理重复名称的 Bean 定义的情况
   if (oldBeanDefinition != null) {
	  if (!isAllowBeanDefinitionOverriding()) {
		 // 如果不允许覆盖的话，抛异常
		 throw new BeanDefinitionStoreException(beanDefinition.getResourceDescription()...
	  }
	  else if (oldBeanDefinition.getRole() < beanDefinition.getRole()) {
		 // log...用框架定义的 Bean 覆盖用户自定义的 Bean 
	  }
	  else if (!beanDefinition.equals(oldBeanDefinition)) {
		 // log...用新的 Bean 覆盖旧的 Bean
	  }
	  else {
		 // log...用同等的 Bean 覆盖旧的 Bean，这里指的是 equals 方法返回 true 的 Bean
	  }
	  // 覆盖
	  this.beanDefinitionMap.put(beanName, beanDefinition);
   }
   else {
	  // 判断是否已经有其他的 Bean 开始初始化了.
	  // 注意，"注册Bean" 这个动作结束，Bean 依然还没有初始化，我们后面会有大篇幅说初始化过程，
	  // 在 Spring 容器启动的最后，会 预初始化 所有的 singleton beans
	  if (hasBeanCreationStarted()) {
		 // Cannot modify startup-time collection elements anymore (for stable iteration)
		 synchronized (this.beanDefinitionMap) {
			this.beanDefinitionMap.put(beanName, beanDefinition);
			List<String> updatedDefinitions = new ArrayList<String>(this.beanDefinitionNames.size() + 1);
			updatedDefinitions.addAll(this.beanDefinitionNames);
			updatedDefinitions.add(beanName);
			this.beanDefinitionNames = updatedDefinitions;
			if (this.manualSingletonNames.contains(beanName)) {
			   Set<String> updatedSingletons = new LinkedHashSet<String>(this.manualSingletonNames);
			   updatedSingletons.remove(beanName);
			   this.manualSingletonNames = updatedSingletons;
			}
		 }
	  }
	  else {
		 // 最正常的应该是进到这个分支。

		 // 将 BeanDefinition 放到这个 map 中，这个 map 保存了所有的 BeanDefinition
		 this.beanDefinitionMap.put(beanName, beanDefinition);
		 // 这是个 ArrayList，所以会按照 bean 配置的顺序保存每一个注册的 Bean 的名字
		 this.beanDefinitionNames.add(beanName);
		 // 这是个 LinkedHashSet，代表的是手动注册的 singleton bean，
		 // 注意这里是 remove 方法，到这里的 Bean 当然不是手动注册的
		 // 手动指的是通过调用以下方法注册的 bean ：
		 //     registerSingleton(String beanName, Object singletonObject)
		 // 这不是重点，解释只是为了不让大家疑惑。Spring 会在后面"手动"注册一些 Bean，
		 // 如 "environment"、"systemProperties" 等 bean，我们自己也可以在运行时注册 Bean 到容器中的
		 this.manualSingletonNames.remove(beanName);
	  }
	  // 这个不重要，在预初始化的时候会用到，不必管它。
	  this.frozenBeanDefinitionNames = null;
   }

   if (oldBeanDefinition != null || containsSingleton(beanName)) {
	  resetBeanDefinition(beanName);
   }
}
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667686970717273747576777879808182
```

总结一下，到这里已经初始化了 Bean 容器， 配置也相应的转换为了一个个 BeanDefinition，然后注册了各个 BeanDefinition 到注册中心，并且发送了注册事件。

--------- 分割线 ---------

到这里是一个分水岭，前面的内容都还算比较简单，不过应该也比较繁琐，大家要清楚地知道前面都做了哪些事情。

## 3-Bean容器实例化完成后

说到这里，我们回到 refresh() 方法，我重新贴了一遍代码，看看我们说到哪了。是的，我们才说完 obtainFreshBeanFactory() 方法。

考虑到篇幅，这里开始大幅缩减掉没必要详细介绍的部分，大家直接看下面的代码中的注释就好了。

```javascript
@Override
public void refresh() throws BeansException, IllegalStateException {
   // 来个锁，不然 refresh() 还没结束，你又来个启动或销毁容器的操作，那不就乱套了嘛
   synchronized (this.startupShutdownMonitor) {

	  // 准备工作，记录下容器的启动时间、标记“已启动”状态、处理配置文件中的占位符
	  prepareRefresh();

	  // 这步比较关键，这步完成后，配置文件就会解析成一个个 Bean 定义，注册到 BeanFactory 中，
	  // 当然，这里说的 Bean 还没有初始化，只是配置信息都提取出来了，
	  // 注册也只是将这些信息都保存到了注册中心(说到底核心是一个 beanName-> beanDefinition 的 map)
	  ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

	  // 设置 BeanFactory 的类加载器，添加几个 BeanPostProcessor，手动注册几个特殊的 bean
	  // 这块待会会展开说
	  prepareBeanFactory(beanFactory);

	  try {
		 // 【这里需要知道 BeanFactoryPostProcessor 这个知识点，Bean 如果实现了此接口，
		 // 那么在容器初始化以后，Spring 会负责调用里面的 postProcessBeanFactory 方法。】

		 // 这里是提供给子类的扩展点，到这里的时候，所有的 Bean 都加载、注册完成了，但是都还没有初始化
		 // 具体的子类可以在这步的时候添加一些特殊的 BeanFactoryPostProcessor 的实现类或做点什么事
		 postProcessBeanFactory(beanFactory);
		 // 调用 BeanFactoryPostProcessor 各个实现类的 postProcessBeanFactory(factory) 回调方法
		 invokeBeanFactoryPostProcessors(beanFactory);          



		 // 注册 BeanPostProcessor 的实现类，注意看和 BeanFactoryPostProcessor 的区别
		 // 此接口两个方法: postProcessBeforeInitialization 和 postProcessAfterInitialization
		 // 两个方法分别在 Bean 初始化之前和初始化之后得到执行。这里仅仅是注册，之后会看到回调这两方法的时机
		 registerBeanPostProcessors(beanFactory);

		 // 初始化当前 ApplicationContext 的 MessageSource，国际化这里就不展开说了，不然没完没了了
		 initMessageSource();

		 // 初始化当前 ApplicationContext 的事件广播器，这里也不展开了
		 initApplicationEventMulticaster();

		 // 从方法名就可以知道，典型的模板方法(钩子方法)，不展开说
		 // 具体的子类可以在这里初始化一些特殊的 Bean（在初始化 singleton beans 之前）
		 onRefresh();

		 // 注册事件监听器，监听器需要实现 ApplicationListener 接口。这也不是我们的重点，过
		 registerListeners();

		 // 重点，重点，重点
		 // 初始化所有的 singleton beans
		 //（lazy-init 的除外）
		 finishBeanFactoryInitialization(beanFactory);

		 // 最后，广播事件，ApplicationContext 初始化完成，不展开
		 finishRefresh();
	  }

	  catch (BeansException ex) {
		 if (logger.isWarnEnabled()) {
			logger.warn("Exception encountered during context initialization - " +
				  "cancelling refresh attempt: " + ex);
		 }

		 // Destroy already created singletons to avoid dangling resources.
		 // 销毁已经初始化的 singleton 的 Beans，以免有些 bean 会一直占用资源
		 destroyBeans();

		 // Reset 'active' flag.
		 cancelRefresh(ex);

		 // 把异常往外抛
		 throw ex;
	  }

	  finally {
		 // Reset common introspection caches in Spring's core, since we
		 // might not ever need metadata for singleton beans anymore...
		 resetCommonCaches();
	  }
   }
}
1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162636465666768697071727374757677787980
```

## 4-准备Bean容器prepareBeanFactory

之前我们说过，Spring 把我们在 xml 配置的 bean 都注册以后，会"手动"注册一些特殊的 bean。

这里简单介绍下 prepareBeanFactory(factory) 方法：

```javascript
protected void prepareBeanFactory(ConfigurableListableBeanFactory beanFactory) {
	// 设置BeanFactory 的类加载器，我们知道 BeanFactory 需要加载类，也就是需要类加载器，
	// 这里设置成 当前 ApplicationContext 类的类加载器
	beanFactory.setBeanClassLoader(getClassLoader());
	
	
	// 设置 BeanExpressionResolver 
	beanFactory.setBeanExpressionResolver(new StandardBeanExpressionResolver(beanFactory.getBeanClassLoader()));
	beanFactory.addPropertyEditorRegistrar(new ResourceEditorRegistrar(this, getEnvironment()));

	// 添加一个 BeanPostProcessor， 这个processor 比较简单
	// 实现了 Aware 接口的beans 在初始化的时候，这个processor 负责回调， 
	// 这个我们很常用，如我们会为了 获取 ApplicationContext 而 implement AppliationContextAware
	// 注意 ： 它不仅仅回调 AppliationContextAware, 
	//         还会负责 回调 EnvironmentAware， ResourceLoaderAware 等， 看下源码就知道了。
	beanFactory.addBeanPostProcessor(new ApplicationContextAwareProcessor(this));
	
	// 下面几行的意思是， 如果某个 bean 依赖于以下几个接口的实现类， 在自动装配的时候忽略他们，
	// Spring 会通过其他方式来处理这些依赖。
	beanFactory.ignoreDependencyInterface(EnvironmentAware.class);
	beanFactory.ignoreDependencyInterface(EmbeddedValueResolverAware.class);
	beanFactory.ignoreDependencyInterface(ResourceLoaderAware.class);
	beanFactory.ignoreDependencyInterface(ApplicationEventPublisherAware.class);
	beanFactory.ignoreDependencyInterface(MessageSourceAware.class);
	beanFactory.ignoreDependencyInterface(ApplicationContextAware.class);

	/ 下面几行 就是为特殊的几个 bean赋值，如果有bean 依赖了以下几个，会注入这边相应的值，
	* 之前我们说过，"当前 ApplicationContext 持有一个 beanFactory " ，这里解释了第一行，
	* ApplicationContext 还继承了 ResourceLoader, ApplicationEventPublisher, MessageSource
	* 所以对于这几个依赖，可以赋值为 this，注意this是一个 ApplicationContext
	* 那这里为啥没有看到 MessageSource赋值呢， 那是因为 MessageSource 被注册成为了一个普通的bean
	*
	/
	beanFactory.registerResolvableDependency(BeanFactory.class, beanFactory);
	beanFactory.registerResolvableDependency(ResourceLoader.class, this);
	beanFactory.registerResolvableDependency(ApplicationEventPublisher.class, this);
	beanFactory.registerResolvableDependency(ApplicationContext.class, this);

	//  这个 BeanPostProcessor 也很简单，在 bean 实例化后，如果是 ApplicationListener 的子类，
	// 那么将其添加到 Listener 列表中，可以理解成: 注册 事件监听器
	beanFactory.addBeanPostProcessor(new ApplicationListenerDetector(this));

	// 这涉及到 特殊的 bean , 名为： loadTimeWeaver, 这不是我们的重点，忽略它
	// tips:  ltw是 AspectJ的概念，指的是 在运行期间进行织入， 这个和Spring AOP不一样
	//   感兴趣的读者请参考我写的关于 AspectJ 的另一篇文章 https://www.javadoop.com/post/aspectj
	if (beanFactory.containsBean(LOAD_TIME_WEAVER_BEAN_NAME)) {
		beanFactory.addBeanPostProcessor(new LoadTimeWeaverAwareProcessor(beanFactory));
		// Set a temporary ClassLoader for type matching.
		beanFactory.setTempClassLoader(new ContextTypeMatchClassLoader(beanFactory.getBeanClassLoader()));
	}

	 /**
		* 从下面几行代码我们可以知道，Spring 往往很 "智能" 就是因为它会帮我们默认注册一些有用的 bean，
		* 我们也可以选择覆盖
		*/

	// 如果没有定义 "environment" 这个 bean，那么 Spring 会 "手动" 注册一个
	if (!beanFactory.containsLocalBean(ENVIRONMENT_BEAN_NAME)) {
		beanFactory.registerSingleton(ENVIRONMENT_BEAN_NAME, getEnvironment());
	}
	
	// 如果没有定义 "systemProperties" 这个bean, 那么Spring 会“手动” 注册一个
	if (!beanFactory.containsLocalBean(SYSTEM_PROPERTIES_BEAN_NAME)) {
		beanFactory.registerSingleton(SYSTEM_PROPERTIES_BEAN_NAME, getEnvironment().getSystemProperties());
	}
	// 如果没有定义 “systemEnvironment” 这个bean， 那么Spring 会手动 注册一个
	if (!beanFactory.containsLocalBean(SYSTEM_ENVIRONMENT_BEAN_NAME)) {
		beanFactory.registerSingleton(SYSTEM_ENVIRONMENT_BEAN_NAME, getEnvironment().getSystemEnvironment());
	}
}
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667686970
```

在上面这块代码中，Spring 对一些特殊的 bean 进行了处理，读者如果暂时还不能消化它们也没有关系，慢慢往下看。

## 5-初始化所有的singleton-beans

我们的重点当然是 finishBeanFactoryInitialization(beanFactory) 这个巨头了，这里会负责初始化所有的singleton beans;

注意： 后面的描述中，我都会是使用初始化或预初始化来代表这个阶段, Spring 会在这个阶段完成所有的singleton beans 的实例化。

我们来总结一下，到目前为止， 应该说beanFactory 已经创建完成了，并且所有的实现了BeanFactoryPostProcessor接口的bean都已经初始化并且
其中的postProcessBeanFactory(factory) 方法已经得到回调执行了。而且 Spring 已经“手动”注册了一些特殊的 Bean，如 environment、systemProperties 等。

剩下的就是初始化 singleton beans 了， 我们知道他们是单例的，如果没有设置懒加载，那么Spring会在接下来的初始化所有的singleton beans。
// AbstractApplicationContext.java 834

```javascript
// 初始化剩余的 singleton beans
protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {

	// 首先，初始化名字为 conversionService 的 Bean。本着送佛送到西的精神，我在附录中简单介绍了一下 ConversionService，因为这实在太实用了
	// 什么，看代码这里没有初始化 Bean 啊！
	// 注意了，初始化的动作包装在 beanFactory.getBean(...) 中，这里先不说细节，先往下看吧
	if (beanFactory.containsBean(CONVERSION_SERVICE_BEAN_NAME) &&
			beanFactory.isTypeMatch(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class)) {
		beanFactory.setConversionService(
				beanFactory.getBean(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class));
	}

	// Register a default embedded value resolver if no bean post-processor
	// (such as a PropertyPlaceholderConfigurer bean) registered any before:
	// at this point, primarily for resolution in annotation attribute values.
	if (!beanFactory.hasEmbeddedValueResolver()) {
		beanFactory.addEmbeddedValueResolver(strVal -> getEnvironment().resolvePlaceholders(strVal));
	}

	// 先初始化 LoadTimeWeaverAware 类型的 Bean
	// 之前也说过，这是 AspectJ 相关的内容，放心跳过吧
	String[] weaverAwareNames = beanFactory.getBeanNamesForType(LoadTimeWeaverAware.class, false, false);
	for (String weaverAwareName : weaverAwareNames) {
		getBean(weaverAwareName);
	}

	// Stop using the temporary ClassLoader for type matching.
	beanFactory.setTempClassLoader(null);

	// 没什么别的目的，因为到这一步的时候，Spring 已经开始预初始化
	// 肯定不希望 这个时候还出现 bean 定义解析，加载，注册。
	beanFactory.freezeConfiguration();

	// 开始初始化 
	beanFactory.preInstantiateSingletons();
}
123456789101112131415161718192021222324252627282930313233343536
```

从上面最后一行往里看，我们就又回到 DefaultListableBeanFactory 这个类了，这个类大家应该都不陌生了吧。

### 5-1-preInstantiateSingletons

preInstantiateSingletons
// DefaultListableBeanFactory 728

```javascript
@Override
public void preInstantiateSingletons() throws BeansException {
	if (logger.isTraceEnabled()) {
		logger.trace("Pre-instantiating singletons in " + this);
	}

	// this.beanDefinitionNames 保存了 所有的 beanNames
	List<String> beanNames = new ArrayList<>(this.beanDefinitionNames);

	// 下面这个循环，触发所有的 非懒加载的 singleton beans 的初始化操作
	for (String beanName : beanNames) {
	
		
		// 合并父 bean 中的设置，注意 <bean id= "" class="" parent="" />中的parent，用的不多吧，
		// 考虑到这可能会影响大家的理解，我在附录中解释了一下 "Bean继承"， 不了解的请到附录查看
		RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);
		
		// 非抽象，非懒加载的singletons。 如果配置了 'abstract = true '， 那是不需要初始化的
		if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit()) {
			// 处理FactoryBean (读者不熟悉 FactoryBean ，请移步到附录区查看)
			if (isFactoryBean(beanName)) {
				// FactoryBean的话，，在beanName前面 + '&'符号。在调用getBean， getBean方法别急
				Object bean = getBean(FACTORY_BEAN_PREFIX + beanName);
				if (bean instanceof FactoryBean) {
					final FactoryBean<?> factory = (FactoryBean<?>) bean;
					boolean isEagerInit;
					 // 判断当前 FactoryBean 是否是 SmartFactoryBean 的实现，此处忽略，直接跳过
					if (System.getSecurityManager() != null && factory instanceof SmartFactoryBean) {
						isEagerInit = AccessController.doPrivileged((PrivilegedAction<Boolean>)
										((SmartFactoryBean<?>) factory)::isEagerInit,
								getAccessControlContext());
					}
					else {
						isEagerInit = (factory instanceof SmartFactoryBean &&
								((SmartFactoryBean<?>) factory).isEagerInit());
					}
					if (isEagerInit) {
						getBean(beanName);
					}
				}
			}
			else {
				// 对于普通的Bean ， 只要调用 getBean(beanName) 这个方法就可以初始化了
				getBean(beanName);
			}
		}
	}

	// 到这里说明所有的非懒加载的 singleton beans 已经完成了初始化
	// 如果我们定义的bean是实现了 SmartInitializingSingleton 接口的，那么在这里得到回调，忽略
	for (String beanName : beanNames) {
		Object singletonInstance = getSingleton(beanName);
		if (singletonInstance instanceof SmartInitializingSingleton) {
			final SmartInitializingSingleton smartSingleton = (SmartInitializingSingleton) singletonInstance;
			if (System.getSecurityManager() != null) {
				AccessController.doPrivileged((PrivilegedAction<Object>) () -> {
					smartSingleton.afterSingletonsInstantiated();
					return null;
				}, getAccessControlContext());
			}
			else {
				smartSingleton.afterSingletonsInstantiated();
			}
		}
	}
}
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566
```

接下来，我们就进入到 getBean(beanName) 方法了，这个方法我们经常用来从 BeanFactory 中获取一个 Bean，而初始化的过程也封装到了这个方法里。

### 5-2-getBean

在继续前进之前，读者应该具备 FactoryBean 的知识，如果读者还不熟悉，请移步附录部分了解 FactoryBean。

// AbstractBeanFactory 196

```javascript
@Override
public Object getBean(String name) throws BeansException {
	return doGetBean(name, null, null, false);
}

// 我们在剖析初始化 Bean的过程， 但是getBean 方法我们经常是用来从容器中获取bean的， 注意切换思路
// 已经初始化的 就从容器直接获取，否则就先初始化后再返回
@SuppressWarnings("unchecked")
protected <T> T doGetBean(final String name, @Nullable final Class<T> requiredType,
		@Nullable final Object[] args, boolean typeCheckOnly) throws BeansException {
	
	// 获取一个 “正统的” beanName ， 处理两种情况， 一个是前面说的 FactoryBean (前面带 '&')
	// 一个是别名问题，因为这个方法是 getBean , 获取Bean 用的， 你要是传一个别名进来，是完全可以的
	final String beanName = transformedBeanName(name);
	
	// 注意跟着这个， 这个是 返回值
	Object bean;

	// 检查下是不是 已经创建过了 
	Object sharedInstance = getSingleton(beanName);
	
	
	// 这里说下 args呗， 虽然看上去一点不重要。 前面我们一路进来的时候都是 getBean(beanName) ,
	// 所以args传参其实是 null，但是如果 args 不为空的时候， 那么意味着调用方不是希望获取 Bean， 而是创建 Bean
	if (sharedInstance != null && args == null) {
		if (logger.isTraceEnabled()) {
			if (isSingletonCurrentlyInCreation(beanName)) {
				logger.trace("Returning eagerly cached instance of singleton bean '" + beanName +
						"' that is not fully initialized yet - a consequence of a circular reference");
			}
			else {
				logger.trace("Returning cached instance of singleton bean '" + beanName + "'");
			}
		}
		// 下面这个方法： 如果是普通Bean 的话，直接返回 sharedInstance, 
		//  如果是FactoryBean 的话，返回它创建的那个实例对象
		//  (FactoryBean 知识， 读者若不清楚请移步附录)
		bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);
	}

	else {
		// 创建 过了 此 beanName 的prototype 类型的 bean， 那么抛出异常，
		// 往往是因为陷入了 循环引用
		if (isPrototypeCurrentlyInCreation(beanName)) {
			throw new BeanCurrentlyInCreationException(beanName);
		}

		// 检查一下 这个 BeanDefinition 在容器中是否存在
		BeanFactory parentBeanFactory = getParentBeanFactory();
		if (parentBeanFactory != null && !containsBeanDefinition(beanName)) {
			// 如果当前容器 不存在这个 BeanDefinition，， 就看父容器中有吗
			String nameToLookup = originalBeanName(name);
			if (parentBeanFactory instanceof AbstractBeanFactory) {
				
				return ((AbstractBeanFactory) parentBeanFactory).doGetBean(
						nameToLookup, requiredType, args, typeCheckOnly);
			}
			else if (args != null) {
				// 返回父容器 的查询结果
				return (T) parentBeanFactory.getBean(nameToLookup, args);
			}
			else if (requiredType != null) {
				// No args -> delegate to standard getBean method.
				return parentBeanFactory.getBean(nameToLookup, requiredType);
			}
			else {
				return (T) parentBeanFactory.getBean(nameToLookup);
			}
		}

		if (!typeCheckOnly) {
			// typeCheckOnly为false，将当前beanName 放入一个 alreadyCreated的 Set集合中。
			markBeanAsCreated(beanName);
		}
		
		/* 总结一下： 
		* 到这里的话， 要准备 创建 Bean 了， 对于 singleton 的Bean 来说，容器中还没创建过此 Bean；
		*  对于 prototype 的Bean 来说， 本来就是要创建一个新的 Bean。
		*
		*/
		try {
			final RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);
			checkMergedBeanDefinition(mbd, beanName, args);

			// 先初始化依赖所有 Bean ， 这个很好理解 
			// 注意， 这里的依赖指的是 depends-on 中定义的依赖
			String[] dependsOn = mbd.getDependsOn();
			if (dependsOn != null) {
				for (String dep : dependsOn) {
					// 检查是不是有循环依赖，这里的循环依赖和我门前面说的循环依赖又不一样， 这里肯定是不允许出现的，不然乱套了，读者一定要想一下
					if (isDependent(beanName, dep)) {
						throw new BeanCreationException(mbd.getResourceDescription(), beanName,
								"Circular depends-on relationship between '" + beanName + "' and '" + dep + "'");
					}
					// 注册一下依赖关系
					registerDependentBean(dep, beanName);
					// 先初始化被依赖项
					try {
						getBean(dep);
					}
					catch (NoSuchBeanDefinitionException ex) {
						throw new BeanCreationException(mbd.getResourceDescription(), beanName,
								"'" + beanName + "' depends on missing bean '" + dep + "'", ex);
					}
				}
			}

			//  如果是 singleton scope的， 创建singleton 的实例
			if (mbd.isSingleton()) {
				sharedInstance = getSingleton(beanName, () -> {
					try {
						// 执行创建 Bean， 详情后面再说
						return createBean(beanName, mbd, args);
					}
					catch (BeansException ex) {
						// Explicitly remove instance from singleton cache: It might have been put there
						// eagerly by the creation process, to allow for circular reference resolution.
						// Also remove any beans that received a temporary reference to the bean.
						destroySingleton(beanName);
						throw ex;
					}
				});
				bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
			}
			
			// 如果是 prototype scope的， 创建 prototype的实例 
			else if (mbd.isPrototype()) {
				// It's a prototype -> create a new instance.
				Object prototypeInstance = null;
				try {
					beforePrototypeCreation(beanName);
					// 直接执行创建 Bean
					prototypeInstance = createBean(beanName, mbd, args);
				}
				finally {
					afterPrototypeCreation(beanName);
				}
				bean = getObjectForBeanInstance(prototypeInstance, name, beanName, mbd);
			}
			// 如果不是 singleton 和 prototype的话，需要委托给相应实现类来处理
			else {
				String scopeName = mbd.getScope();
				final Scope scope = this.scopes.get(scopeName);
				if (scope == null) {
					throw new IllegalStateException("No Scope registered for scope name '" + scopeName + "'");
				}
				try {
					Object scopedInstance = scope.get(beanName, () -> {
						beforePrototypeCreation(beanName);
						try {
							// 执行 创阿金 bean
							return createBean(beanName, mbd, args);
						}
						finally {
							afterPrototypeCreation(beanName);
						}
					});
					bean = getObjectForBeanInstance(scopedInstance, name, beanName, mbd);
				}
				catch (IllegalStateException ex) {
					throw new BeanCreationException(beanName,
							"Scope '" + scopeName + "' is not active for the current thread; consider " +
							"defining a scoped proxy for this bean if you intend to refer to it from a singleton",
							ex);
				}
			}
		}
		catch (BeansException ex) {
			cleanupAfterBeanCreationFailure(beanName);
			throw ex;
		}
	}

	// 最后， 检查一下类型对不对， 不对的话就抛异常，对的话 就返回了。
	if (requiredType != null && !requiredType.isInstance(bean)) {
		try {
			T convertedBean = getTypeConverter().convertIfNecessary(bean, requiredType);
			if (convertedBean == null) {
				throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
			}
			return convertedBean;
		}
		catch (TypeMismatchException ex) {
			if (logger.isTraceEnabled()) {
				logger.trace("Failed to convert bean '" + name + "' to required type '" +
						ClassUtils.getQualifiedName(requiredType) + "'", ex);
			}
			throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
		}
	}
	return (T) bean;
}
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990919293949596979899100101102103104105106107108109110111112113114115116117118119120121122123124125126127128129130131132133134135136137138139140141142143144145146147148149150151152153154155156157158159160161162163164165166167168169170171172173174175176177178179180181182183184185186187188189190191192
```

大家应该也猜到了，接下来当然是分析 createBean 方法：

```
protected abstract Object createBean(String beanName, RootBeanDefinition mbd, Object[] args) throws BeanCreationException;
1
```

第三个参数 args 数组代表创建实例需要的参数，不就是给构造方法用的参数，或者是工厂 Bean 的参数嘛，不过要注意，在我们的初始化阶段，args 是 null。

这回我们要到一个新的类了 AbstractAutowireCapableBeanFactory，看类名，AutowireCapable？类名是不是也说明了点问题了。
主要是为了以下场景，采用 @Autowired 注解注入属性值：

```
public class Video {
				@Autowired
				private UserService userService;

				public String getVideo() {
					return userService.getVideo();
				}
			}
<bean id="video"  class="com.xlg.springSource.beans.Video" />
123456789
```

以上这种属于混用了 xml 和 注解 两种方式的配置方式，Spring 会处理这种情况。

好了，读者要知道这么回事就可以了，继续向前。

createBean
// AbstractAutowireCapableBeanFactory 447

```javascript
@Override
protected Object createBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
		throws BeanCreationException {

	if (logger.isTraceEnabled()) {
		logger.trace("Creating instance of bean '" + beanName + "'");
	}
	RootBeanDefinition mbdToUse = mbd;

	// 确保 BeanDefinition 中的 Class 被加载
	Class<?> resolvedClass = resolveBeanClass(mbd, beanName);
	if (resolvedClass != null && !mbd.hasBeanClass() && mbd.getBeanClassName() != null) {
		mbdToUse = new RootBeanDefinition(mbd);
		mbdToUse.setBeanClass(resolvedClass);
	}

	// 准备方法覆写，这里又涉及到一个概念： MethodOverrides，它来自于 bean 定义中的<lookup-method />
	// 和 <replaced-method /> ， 如果读者感兴趣，回到bean 解析的地方看看 对这两个标签的解析。
	//  在附录中 也对这两个标签的相关知识进行了介绍，读者可以移步去看看。
	try {
		mbdToUse.prepareMethodOverrides();
	}
	catch (BeanDefinitionValidationException ex) {
		throw new BeanDefinitionStoreException(mbdToUse.getResourceDescription(),
				beanName, "Validation of method overrides failed", ex);
	}

	try {
		//  让InstantiationAwareBeanPostProcessor 在这一步 有机会返回代理，
		// 在 <<Spring AOP 源码分析>> 那篇文章中有解释， 这里先跳过
		Object bean = resolveBeforeInstantiation(beanName, mbdToUse);
		if (bean != null) {
			return bean;
		}
	}
	catch (Throwable ex) {
		throw new BeanCreationException(mbdToUse.getResourceDescription(), beanName,
				"BeanPostProcessor before instantiation of bean failed", ex);
	}

	try {
		// 重要，，，，，，创建 Bean
		Object beanInstance = doCreateBean(beanName, mbdToUse, args);
		if (logger.isTraceEnabled()) {
			logger.trace("Finished creating instance of bean '" + beanName + "'");
		}
		return beanInstance;
	}
	catch (BeanCreationException | ImplicitlyAppearedSingletonException ex) {
		// A previously detected exception with proper bean creation context already,
		// or illegal singleton state to be communicated up to DefaultSingletonBeanRegistry.
		throw ex;
	}
	catch (Throwable ex) {
		throw new BeanCreationException(
				mbdToUse.getResourceDescription(), beanName, "Unexpected exception during bean creation", ex);
	}
}
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758
```

### 5-3-创建Bean

doCreateBean
我们继续往里看 doCreateBean 这个方法：

```javascript
protected Object doCreateBean(final String beanName, final RootBeanDefinition mbd, final @Nullable Object[] args)
		throws BeanCreationException {

	// Instantiate the bean.
	BeanWrapper instanceWrapper = null;
	if (mbd.isSingleton()) {
		instanceWrapper = this.factoryBeanInstanceCache.remove(beanName);
	}
	if (instanceWrapper == null) {
		// 说明不是 FactoryBean， 这里实例化 Bean， 这里非常关键，细节之后再说哦
		instanceWrapper = createBeanInstance(beanName, mbd, args);
	}
	// 这个就是 Bean 里面的 我们定义的类的实例，很多地方我直接描述成 “bean 实例”
	final Object bean = instanceWrapper.getWrappedInstance();
	// 类型
	Class<?> beanType = instanceWrapper.getWrappedClass();
	if (beanType != NullBean.class) {
		mbd.resolvedTargetType = beanType;
	}

	// 简历直接跳过， 涉及接口: MergedBeanDefinitionPostProcessor
	synchronized (mbd.postProcessingLock) {
		if (!mbd.postProcessed) {
			try {
				// MergedBeanDefinitionPostProcessor， 不展开说了，，直接跳过，很少用
				applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);
			}
			catch (Throwable ex) {
				throw new BeanCreationException(mbd.getResourceDescription(), beanName,
						"Post-processing of merged bean definition failed", ex);
			}
			mbd.postProcessed = true;
		}
	}

	// Eagerly cache singletons to be able to resolve circular references
	// even when triggered by lifecycle interfaces like BeanFactoryAware.
	// 下面这块代码是为了 解决循环依赖的问题， 以后有时间，我们在对循环依赖这个问题进行解析八
	boolean earlySingletonExposure = (mbd.isSingleton() && this.allowCircularReferences &&
			isSingletonCurrentlyInCreation(beanName));
	if (earlySingletonExposure) {
		if (logger.isTraceEnabled()) {
			logger.trace("Eagerly caching bean '" + beanName +
					"' to allow for resolving potential circular references");
		}
		addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));
	}

	// Initialize the bean instance.
	Object exposedObject = bean;
	try {
		// 这一步非常关键，这一步负责属性 装配， 因为前面的实例只是实例化了，没有设值
		populateBean(beanName, mbd, instanceWrapper);
		// 该记得 init-method 吗? 还有 InitializationBean 接口？还有 BeanPostProcessor 接口、
		// 这里就是处理 bean 初始化完后 的各种回调
		exposedObject = initializeBean(beanName, exposedObject, mbd);
	}
	catch (Throwable ex) {
		if (ex instanceof BeanCreationException && beanName.equals(((BeanCreationException) ex).getBeanName())) {
			throw (BeanCreationException) ex;
		}
		else {
			throw new BeanCreationException(
					mbd.getResourceDescription(), beanName, "Initialization of bean failed", ex);
		}
	}

	if (earlySingletonExposure) {
		Object earlySingletonReference = getSingleton(beanName, false);
		if (earlySingletonReference != null) {
			if (exposedObject == bean) {
				exposedObject = earlySingletonReference;
			}
			else if (!this.allowRawInjectionDespiteWrapping && hasDependentBean(beanName)) {
				String[] dependentBeans = getDependentBeans(beanName);
				Set<String> actualDependentBeans = new LinkedHashSet<>(dependentBeans.length);
				for (String dependentBean : dependentBeans) {
					if (!removeSingletonIfCreatedForTypeCheckOnly(dependentBean)) {
						actualDependentBeans.add(dependentBean);
					}
				}
				if (!actualDependentBeans.isEmpty()) {
					throw new BeanCurrentlyInCreationException(beanName,
							"Bean with name '" + beanName + "' has been injected into other beans [" +
							StringUtils.collectionToCommaDelimitedString(actualDependentBeans) +
							"] in its raw version as part of a circular reference, but has eventually been " +
							"wrapped. This means that said other beans do not use the final version of the " +
							"bean. This is often the result of over-eager type matching - consider using " +
							"'getBeanNamesOfType' with the 'allowEagerInit' flag turned off, for example.");
				}
			}
		}
	}

	// Register bean as disposable.
	try {
		registerDisposableBeanIfNecessary(beanName, bean, mbd);
	}
	catch (BeanDefinitionValidationException ex) {
		throw new BeanCreationException(
				mbd.getResourceDescription(), beanName, "Invalid destruction signature", ex);
	}

	return exposedObject;
}
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990919293949596979899100101102103104105
```

到这里，我们已经分析完了 doCreateBean 方法，总的来说，我们已经说完了整个初始化流程。

接下来我们挑 doCreateBean 中的三个细节出来说说。一个是创建 Bean 实例的 createBeanInstance 方法，一个是依赖注入的 populateBean 方法，还有就是回调方法 initializeBean。

注意了，接下来的这三个方法要认真说那也是极其复杂的，很多地方我就点到为止了，感兴趣的读者可以自己往里看，最好就是碰到不懂的，自己写代码去调试它。

#### 5-3-1-创建Bean实例

我们先看看 createBeanInstance 方法。需要说明的是，这个方法如果每个分支都分析下去，必然也是极其复杂冗长的，我们挑重点说。此方法的目的就是实例化我们指定的类。

createBeanInstance

```javascript
protected BeanWrapper createBeanInstance(String beanName, RootBeanDefinition mbd, @Nullable Object[] args) {
	// 确保 已经加载了 此 class
	Class<?> beanClass = resolveBeanClass(mbd, beanName);
		
	// 校验一下这个类的访问权限
	if (beanClass != null && !Modifier.isPublic(beanClass.getModifiers()) && !mbd.isNonPublicAccessAllowed()) {
		throw new BeanCreationException(mbd.getResourceDescription(), beanName,
				"Bean class isn't public, and non-public access not allowed: " + beanClass.getName());
	}

	Supplier<?> instanceSupplier = mbd.getInstanceSupplier();
	if (instanceSupplier != null) {
		
		return obtainFromSupplier(instanceSupplier, beanName);
	}

	if (mbd.getFactoryMethodName() != null) {
		// 采用工厂方法实例化，不熟悉这个概念的读者请看附录，注意，不是FactoryBean
		return instantiateUsingFactoryMethod(beanName, mbd, args);
	}

	// 如果不是第一次创建，比如第二次创建 prototype bean。
	// 这种情况下，，我们可以从第一次创建知道，采用无参构造函数，还是构造函数依赖注入 来完成实例化
	boolean resolved = false;
	boolean autowireNecessary = false;
	if (args == null) {
		synchronized (mbd.constructorArgumentLock) {
			if (mbd.resolvedConstructorOrFactoryMethod != null) {
				resolved = true;
				autowireNecessary = mbd.constructorArgumentsResolved;
			}
		}
	}
	if (resolved) {
		if (autowireNecessary) {
			// 构造函数依赖 注入
			return autowireConstructor(beanName, mbd, null, null);
		}
		else {
			// 无参构造函数 
			return instantiateBean(beanName, mbd);
		}
	}

	// 判断 是否采用 有参构造函数
	Constructor<?>[] ctors = determineConstructorsFromBeanPostProcessors(beanClass, beanName);
	if (ctors != null || mbd.getResolvedAutowireMode() == AUTOWIRE_CONSTRUCTOR ||
			mbd.hasConstructorArgumentValues() || !ObjectUtils.isEmpty(args)) {
		// 构造函数依赖注入
		return autowireConstructor(beanName, mbd, ctors, args);
	}

	// Preferred constructors for default construction?
	ctors = mbd.getPreferredConstructors();
	if (ctors != null) {
		return autowireConstructor(beanName, mbd, ctors, null);
	}
	
	// 调用无参构造函数
	return instantiateBean(beanName, mbd);
}	
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061
```

挑个简单的无参构造函数构造实例来看看：

instantiateBean

```javascript
protected BeanWrapper instantiateBean(final String beanName, final RootBeanDefinition mbd) {
	try {
		Object beanInstance;
		final BeanFactory parent = this;
		if (System.getSecurityManager() != null) {
			beanInstance = AccessController.doPrivileged((PrivilegedAction<Object>) () ->
					getInstantiationStrategy().instantiate(mbd, beanName, parent),
					getAccessControlContext());
		}
		else {
			// 实例化 
			beanInstance = getInstantiationStrategy().instantiate(mbd, beanName, parent);
		}
		// 包装一下 ，返回
		BeanWrapper bw = new BeanWrapperImpl(beanInstance);
		initBeanWrapper(bw);
		return bw;
	}
	catch (Throwable ex) {
		throw new BeanCreationException(
				mbd.getResourceDescription(), beanName, "Instantiation of bean failed", ex);
	}
}
1234567891011121314151617181920212223
```

我们可以看到，关键的地方在于：

```javascript
beanInstance = getInstantiationStrategy().instantiate(mbd, beanName, parent);
1
```

这里会进行实际的实例化过程，我们进去看看:
// SimpleInstantiationStrategy 59
instantiate

```javascript
@Override
public Object instantiate(RootBeanDefinition bd, String beanName, BeanFactory owner) {

   // 如果不存在方法覆写，那就使用 java 反射进行实例化，否则使用 CGLIB,
   // 方法覆写 请参见附录"方法注入"中对 lookup-method 和 replaced-method 的介绍
   if (bd.getMethodOverrides().isEmpty()) {
	  Constructor<?> constructorToUse;
	  synchronized (bd.constructorArgumentLock) {
		 constructorToUse = (Constructor<?>) bd.resolvedConstructorOrFactoryMethod;
		 if (constructorToUse == null) {
			final Class<?> clazz = bd.getBeanClass();
			if (clazz.isInterface()) {
			   throw new BeanInstantiationException(clazz, "Specified class is an interface");
			}
			try {
			   if (System.getSecurityManager() != null) {
				  constructorToUse = AccessController.doPrivileged(new PrivilegedExceptionAction<Constructor<?>>() {
					 @Override
					 public Constructor<?> run() throws Exception {
						return clazz.getDeclaredConstructor((Class[]) null);
					 }
				  });
			   }
			   else {
				  constructorToUse = clazz.getDeclaredConstructor((Class[]) null);
			   }
			   bd.resolvedConstructorOrFactoryMethod = constructorToUse;
			}
			catch (Throwable ex) {
			   throw new BeanInstantiationException(clazz, "No default constructor found", ex);
			}
		 }
	  }
	  // 利用构造方法进行实例化
	  return BeanUtils.instantiateClass(constructorToUse);
   }
   else {
	  // 存在方法覆写，利用 CGLIB 来完成实例化，需要依赖于 CGLIB 生成子类，这里就不展开了。
	  // tips: 因为如果不使用 CGLIB 的话，存在 override 的情况 JDK 并没有提供相应的实例化支持
	  return instantiateWithMethodInjection(bd, beanName, owner);
   }
}
123456789101112131415161718192021222324252627282930313233343536373839404142
到这里，我们就算实例化完成了。我们开始说怎么进行属性注入。
1
```

#### 5-3-2-bean属性注入

populateBean
看完了 createBeanInstance(…) 方法，我们来看看 populateBean(…) 方法，该方法负责进行属性设值，处理依赖。

// AbstractAutowireCapableBeanFactory 1203

```
@SuppressWarnings("deprecation")  // for postProcessPropertyValues
protected void populateBean(String beanName, RootBeanDefinition mbd, @Nullable BeanWrapper bw) {
	if (bw == null) {
		if (mbd.hasPropertyValues()) {
			throw new BeanCreationException(
					mbd.getResourceDescription(), beanName, "Cannot apply property values to null instance");
		}
		else {
			// Skip property population phase for null instance.
			return;
		}
	}

	// Give any InstantiationAwareBeanPostProcessors the opportunity to modify the
	// state of the bean before properties are set. This can be used, for example,
	// to support styles of field injection.
	// 到这步的时候，bean 实例化完成 (通过 工厂方法或者构造方法)， 但是还没有开始属性设置， 
	// InstantiationAwareBeanPostProcessor 的实现类可以在这里对 bean 进行状态修改，
	// 我也没找到有实际的使用，，所以我们暂且忽略这块，，我在这写的时候 一片懵逼，
	// 只能先一步步敲中文，然后博客后，，在细看 ioc源码
	if (!mbd.isSynthetic() && hasInstantiationAwareBeanPostProcessors()) {
		for (BeanPostProcessor bp : getBeanPostProcessors()) {
			if (bp instanceof InstantiationAwareBeanPostProcessor) {
				InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
				// 如果 返回false， 代表 不需要进行 后续的属性设值，也不需要再经过其他的 BeanPostProcessor的处理
				if (!ibp.postProcessAfterInstantiation(bw.getWrappedInstance(), beanName)) {
					return;
				}
			}
		}
	}
		
	// bean 实例的所有属性都在这里了 
	PropertyValues pvs = (mbd.hasPropertyValues() ? mbd.getPropertyValues() : null);

	int resolvedAutowireMode = mbd.getResolvedAutowireMode();
	if (resolvedAutowireMode == AUTOWIRE_BY_NAME || resolvedAutowireMode == AUTOWIRE_BY_TYPE) {
		MutablePropertyValues newPvs = new MutablePropertyValues(pvs);
		// 通过名字找到 所有属性，如果是 bean 依赖，先初始化依赖的bean。 记录依赖关系
		if (resolvedAutowireMode == AUTOWIRE_BY_NAME) {
			autowireByName(beanName, mbd, bw, newPvs);
		}
		// 通过类型装配。。 复杂一些
		if (resolvedAutowireMode == AUTOWIRE_BY_TYPE) {
			autowireByType(beanName, mbd, bw, newPvs);
		}
		pvs = newPvs;
	}

	boolean hasInstAwareBpps = hasInstantiationAwareBeanPostProcessors();
	boolean needsDepCheck = (mbd.getDependencyCheck() != AbstractBeanDefinition.DEPENDENCY_CHECK_NONE);

	PropertyDescriptor[] filteredPds = null;
	if (hasInstAwareBpps) {
		if (pvs == null) {
			pvs = mbd.getPropertyValues();
		}
		for (BeanPostProcessor bp : getBeanPostProcessors()) {
			if (bp instanceof InstantiationAwareBeanPostProcessor) {
				InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
				// 这里有个非常有用的 BeanPostProcessor 进到这里： AutowiredAnnotationBeanPostProcessor
				// 对采用 @Autowired , @Value 注解的 依赖进行设值，这里的内容也是非常丰富的，不过本文不再
				// 展开说了，感兴趣的读者可以自己去看看。
				PropertyValues pvsToUse = ibp.postProcessProperties(pvs, bw.getWrappedInstance(), beanName);
				if (pvsToUse == null) {
					if (filteredPds == null) {
						filteredPds = filterPropertyDescriptorsForDependencyCheck(bw, mbd.allowCaching);
					}
					pvsToUse = ibp.postProcessPropertyValues(pvs, filteredPds, bw.getWrappedInstance(), beanName);
					if (pvsToUse == null) {
						return;
					}
				}
				pvs = pvsToUse;
			}
		}
	}
	if (needsDepCheck) {
		if (filteredPds == null) {
			filteredPds = filterPropertyDescriptorsForDependencyCheck(bw, mbd.allowCaching);
		}
		checkDependencies(beanName, mbd, filteredPds, pvs);
	}

	if (pvs != null) {
	//设值 bean 实例的属性值
		applyPropertyValues(beanName, mbd, bw, pvs);
	}
}
1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162636465666768697071727374757677787980818283848586878889
```

#### 5-3-3-initializeBean

属性注入完成后，这一步其实就是处理各种回调了，这块代码比较简单

```javascript
protected Object initializeBean(final String beanName, final Object bean, @Nullable RootBeanDefinition mbd) {
	if (System.getSecurityManager() != null) {
		AccessController.doPrivileged((PrivilegedAction<Object>) () -> {
			invokeAwareMethods(beanName, bean);
			return null;
		}, getAccessControlContext());
	}
	else {
		// 如果bean 实现了 BeanNameAware， BeanClassLoaderAware，或者BeanFactoryAware 接口，回调
		invokeAwareMethods(beanName, bean);
	}

	Object wrappedBean = bean;
	if (mbd == null || !mbd.isSynthetic()) {
	
		// BeanPostProcessor 的postProcessBeforeInitialization 回调
		wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
	}

	try {
		// 处理 bean 中定义的 init-method, 
		// 或者 如果 bean 实现了 InitializationBean 接口， 调用 afterPropertiesSet() 方法
		invokeInitMethods(beanName, wrappedBean, mbd);
	}
	catch (Throwable ex) {
		throw new BeanCreationException(
				(mbd != null ? mbd.getResourceDescription() : null),
				beanName, "Invocation of init method failed", ex);
	}
	if (mbd == null || !mbd.isSynthetic()) {
		// BeanPostProcessor 的 postProcessAfterInitialization 回调
		wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
	}
	return wrappedBean;
}
1234567891011121314151617181920212223242526272829303132333435
大家发现没有，BeanPostProcessor 的两个回调都发生在这边，只不过中间处理了 init-method，是不是和读者原来的认知有点不一样了？
1
```

# 附录

# id 和 name

每个 Bean 在 Spring 容器中都有一个唯一的名字（beanName）和 0 个或多个别名（aliases）。

我们从 Spring 容器中获取 Bean 的时候，可以根据 beanName，也可以通过别名。

```javascript
beanFactory.getBean("beanName or alias");
1
```

在配置 的过程中，我们可以配置 id 和 name，看几个例子就知道是怎么回事了。

```javascript
<bean id="messageService" name="m1, m2, m3" class="com.javadoop.example.MessageServiceImpl">
1
```

以上配置的结果就是：beanName 为 messageService，别名有 3 个，分别为 m1、m2、m3。

```javascript
<bean name="m1, m2, m3" class="com.javadoop.example.MessageServiceImpl" />
以上配置的结果就是：beanName 为 m1，别名有 2 个，分别为 m2、m3。
12
<bean class="com.javadoop.example.MessageServiceImpl">
beanName 为：com.javadoop.example.MessageServiceImpl#0，
12
```

别名 1 个，为： com.javadoop.example.MessageServiceImpl

```javascript
<bean id="messageService" class="com.javadoop.example.MessageServiceImpl">
1
```

以上配置的结果就是：beanName 为 messageService，没有别名。

# 配置是否允许 Bean 覆盖、是否允许循环依赖

我们说过，默认情况下，allowBeanDefinitionOverriding 属性为 null。如果在同一配置文件中 Bean id 或 name 重复了，会抛错，但是如果不是同一配置文件中，会发生覆盖。

可是有些时候我们希望在系统启动的过程中就严格杜绝发生 Bean 覆盖，因为万一出现这种情况，会增加我们排查问题的成本。

循环依赖说的是 A 依赖 B，而 B 又依赖 A。或者是 A 依赖 B，B 依赖 C，而 C 却依赖 A。默认 allowCircularReferences 也是 null。

它们两个属性是一起出现的，必然可以在同一个地方一起进行配置。

添加这两个属性的作者 Juergen Hoeller 在这个 jira 的讨论中说明了怎么配置这两个属性。

```javascript
public class NoBeanOverridingContextLoader extends ContextLoader {

  @Override
  protected void customizeContext(ServletContext servletContext, ConfigurableWebApplicationContext applicationContext) {
	super.customizeContext(servletContext, applicationContext);
	AbstractRefreshableApplicationContext arac = (AbstractRefreshableApplicationContext) applicationContext;
	arac.setAllowBeanDefinitionOverriding(false);
  }
}
public class MyContextLoaderListener extends org.springframework.web.context.ContextLoaderListener {

  @Override
  protected ContextLoader createContextLoader() {
	return new NoBeanOverridingContextLoader();
  }

}
<listener>
	<listener-class>com.javadoop.MyContextLoaderListener</listener-class>  
</listener>
1234567891011121314151617181920
```

如果以上方式不能满足你的需求，请参考这个链接：解决spring中不同配置文件中存在name或者id相同的bean可能引起的问题

# profile

我们可以把不同环境的配置分别配置到单独的文件中，举个例子：

```javascript
<beans profile="development"
	xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:jdbc="http://www.springframework.org/schema/jdbc"
	xsi:schemaLocation="...">

	<jdbc:embedded-database id="dataSource">
		<jdbc:script location="classpath:com/bank/config/sql/schema.sql"/>
		<jdbc:script location="classpath:com/bank/config/sql/test-data.sql"/>
	</jdbc:embedded-database>
</beans>
1234567891011
<beans profile="production"
	xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:jee="http://www.springframework.org/schema/jee"
	xsi:schemaLocation="...">

	<jee:jndi-lookup id="dataSource" jndi-name="java:comp/env/jdbc/datasource"/>
</beans>
12345678
```

应该不必做过多解释了吧，看每个文件第一行的 profile=""。

当然，我们也可以在一个配置文件中使用：

```javascript
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:jdbc="http://www.springframework.org/schema/jdbc"
	xmlns:jee="http://www.springframework.org/schema/jee"
	xsi:schemaLocation="...">

	<beans profile="development">
		<jdbc:embedded-database id="dataSource">
			<jdbc:script location="classpath:com/bank/config/sql/schema.sql"/>
			<jdbc:script location="classpath:com/bank/config/sql/test-data.sql"/>
		</jdbc:embedded-database>
	</beans>

	<beans profile="production">
		<jee:jndi-lookup id="dataSource" jndi-name="java:comp/env/jdbc/datasource"/>
	</beans>
</beans>
1234567891011121314151617
```

理解起来也很简单吧。

接下来的问题是，怎么使用特定的 profile 呢？Spring 在启动的过程中，会去寻找 “spring.profiles.active” 的属性值，根据这个属性值来的。那怎么配置这个值呢？

Spring 会在这几个地方寻找 spring.profiles.active 的属性值：操作系统环境变量、JVM 系统变量、web.xml 中定义的参数、JNDI。

最简单的方式莫过于在程序启动的时候指定：

```javascript
-Dspring.profiles.active="profile1,profile2"
1
```

> profile 可以激活多个

当然，我们也可以通过代码的形式从 Environment 中设置 profile：

```javascript
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
ctx.getEnvironment().setActiveProfiles("development");
ctx.register(SomeConfig.class, StandaloneDataConfig.class, JndiDataConfig.class);
ctx.refresh(); // 重启
1234
```

如果是 Spring Boot 的话更简单，我们一般会创建 application.properties、application-dev.properties、application-prod.properties 等文件，其中 application.properties 配置各个环境通用的配置，application-{profile}.properties 中配置特定环境的配置，然后在启动的时候指定 profile：

```javascript
java -Dspring.profiles.active=prod -jar JavaDoop.jar
1
```

如果是单元测试中使用的话，在测试类中使用 @ActiveProfiles 指定，这里就不展开了。

# 工厂模式生成Bean

请读者注意 factory-bean 和 FactoryBean 的区别。这节说的是前者，是说静态工厂或实例工厂，而后者是 Spring 中的特殊接口，代表一类特殊的 Bean，附录的下面一节会介绍 FactoryBean。

设计模式里，工厂方法模式分静态工厂和实例工厂，我们分别看看 Spring 中怎么配置这两个，来个代码示例就什么都清楚了。

静态工厂：

```javascript
<bean id="clientService"
	class="examples.ClientService"
	factory-method="createInstance"/>
123
public class ClientService {
	private static ClientService clientService = new ClientService();
	private ClientService() {}

	// 静态方法
	public static ClientService createInstance() {
		return clientService;
	}
}
123456789
```

实例工厂：

```javascript
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
	<!-- inject any dependencies required by this locator bean -->
</bean>

<bean id="clientService"
	factory-bean="serviceLocator"
	factory-method="createClientServiceInstance"/>

<bean id="accountService"
	factory-bean="serviceLocator"
	factory-method="createAccountServiceInstance"/>
1234567891011
public class DefaultServiceLocator {

	private static ClientService clientService = new ClientServiceImpl();

	private static AccountService accountService = new AccountServiceImpl();

	public ClientService createClientServiceInstance() {
		return clientService;
	}

	public AccountService createAccountServiceInstance() {
		return accountService;
	}
}
1234567891011121314
```

# FactoryBean

FactoryBean 适用于 Bean 的创建过程比较复杂的场景，比如数据库连接池的创建。

```javascript
public interface FactoryBean<T> {
	T getObject() throws Exception;
	Class<T> getObjectType();
	boolean isSingleton();
}
public class Person { 
	private Car car ;
	private void setCar(Car car){ this.car = car;  }  
}
123456789
```

我们假设现在需要创建一个 Person 的 Bean，首先我们需要一个 Car 的实例，我们这里假设 Car 的实例创建很麻烦，那么我们可以把创建 Car 的复杂过程包装起来：

```javascript
public class MyCarFactoryBean implements FactoryBean<Car>{
	private String make; 
	private int year ;

	public void setMake(String m){ this.make =m ; }

	public void setYear(int y){ this.year = y; }

	public Car getObject(){ 
	  // 这里我们假设 Car 的实例化过程非常复杂，反正就不是几行代码可以写完的那种
	  CarBuilder cb = CarBuilder.car();

	  if(year!=0) cb.setYear(this.year);
	  if(StringUtils.hasText(this.make)) cb.setMake( this.make ); 
	  return cb.factory(); 
	}

	public Class<Car> getObjectType() { return Car.class ; } 

	public boolean isSingleton() { return false; }
}
123456789101112131415161718192021
```

我们看看装配的时候是怎么配置的：

```javascript
<bean class = "com.javadoop.MyCarFactoryBean" id = "car">
  <property name = "make" value ="Honda"/>
  <property name = "year" value ="1984"/>
</bean>
<bean class = "com.javadoop.Person" id = "josh">
  <property name = "car" ref = "car"/>
</bean>
1234567
```

看到不一样了吗？id 为 “car” 的 bean 其实指定的是一个 FactoryBean，不过配置的时候，我们直接让配置 Person 的 Bean 直接依赖于这个 FactoryBean 就可以了。中间的过程 Spring 已经封装好了。

说到这里，我们再来点干货。我们知道，现在还用 xml 配置 Bean 依赖的越来越少了，更多时候，我们可能会采用 java config 的方式来配置，这里有什么不一样呢？

```javascript
@Configuration 
public class CarConfiguration { 

	@Bean 
	public MyCarFactoryBean carFactoryBean(){ 
	  MyCarFactoryBean cfb = new MyCarFactoryBean();
	  cfb.setMake("Honda");
	  cfb.setYear(1984);
	  return cfb;
	}

	@Bean
	public Person aPerson(){ 
	Person person = new Person();
	  // 注意这里的不同
	person.setCar(carFactoryBean().getObject());
	return person; 
	} 
}
12345678910111213141516171819
```

这个时候，其实我们的思路也很简单，把 MyCarFactoryBean 看成是一个简单的 Bean 就可以了，不必理会什么 FactoryBean，它是不是 FactoryBean 和我们没关系。

# 初始化Bean的回调

有以下四种方案：

```javascript
<bean id="exampleInitBean" class="examples.ExampleBean" init-method="init"/>
1
public class AnotherExampleBean implements InitializingBean {

	public void afterPropertiesSet() {
		// do some initialization work
	}
}
@Bean(initMethod = "init")
public Foo foo() {
	return new Foo();
}
@PostConstruct
public void init() {

}
1234567891011121314
```

# 销毁Bean的回调

```javascript
<bean id="exampleInitBean" class="examples.ExampleBean" destroy-method="cleanup"/>
1
public class AnotherExampleBean implements DisposableBean {

	public void destroy() {
		// do some destruction work (like releasing pooled connections)
	}
}
@Bean(destroyMethod = "cleanup")
public Bar bar() {
	return new Bar();
}
@PreDestroy
public void cleanup() {

}
1234567891011121314
```

# ConversionService

既然文中说到了这个，顺便提一下好了。

最有用的场景就是，它用来将前端传过来的参数和后端的 controller 方法上的参数进行绑定的时候用。

像前端传过来的字符串、整数要转换为后端的 String、Integer 很容易，但是如果 controller 方法需要的是一个枚举值，或者是 Date 这些非基础类型（含基础类型包装类）值的时候，我们就可以考虑采用 ConversionService 来进行转换。

```javascript
<bean id="conversionService"
  class="org.springframework.context.support.ConversionServiceFactoryBean">
  <property name="converters">
	<list>
	  <bean class="com.javadoop.learning.utils.StringToEnumConverterFactory"/>
	</list>
  </property>
</bean>
12345678
```

ConversionService 接口很简单，所以要自定义一个 convert 的话也很简单。

下面再说一个实现这种转换很简单的方式，那就是实现 Converter 接口。

来看一个很简单的例子，这样比什么都管用。

```javascript
public class StringToDateConverter implements Converter<String, Date> {

	@Override
	public Date convert(String source) {
		try {
			return DateUtils.parseDate(source, "yyyy-MM-dd", "yyyy-MM-dd HH:mm:ss", "yyyy-MM-dd HH:mm", "HH:mm:ss", "HH:mm");
		} catch (ParseException e) {
			return null;
		}
	}
}
1234567891011
```

只要注册这个 Bean 就可以了。这样，前端往后端传的时间描述字符串就很容易绑定成 Date 类型了，不需要其他任何操作

# Bean继承

在初始化 Bean 的地方，我们说过了这个：

```javascript
RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);
1
```

这里涉及到的就是 中的 parent 属性，我们来看看 Spring 中是用这个来干什么的。

首先，我们要明白，这里的继承和 java 语法中的继承没有任何关系，不过思路是相通的。child bean 会继承 parent bean 的所有配置，也可以覆盖一些配置，当然也可以新增额外的配置。

Spring 中提供了继承自 AbstractBeanDefinition 的 ChildBeanDefinition 来表示 child bean。

看如下一个例子:

```javascript
<bean id="inheritedTestBean" abstract="true" class="org.springframework.beans.TestBean">
	<property name="name" value="parent"/>
	<property name="age" value="1"/>
</bean>

<bean id="inheritsWithDifferentClass" class="org.springframework.beans.DerivedTestBean"
		parent="inheritedTestBean" init-method="initialize">

	<property name="name" value="override"/>
</bean>
1234567891011
```

parent bean 设置了 abstract=“true” 所以它不会被实例化，child bean 继承了 parent bean 的两个属性，但是对 name 属性进行了覆写。

child bean 会继承 scope、构造器参数值、属性值、init-method、destroy-method 等等。

当然，我不是说 parent bean 中的 abstract = true 在这里是必须的，只是说如果加上了以后 Spring 在实例化 singleton beans 的时候会忽略这个 bean。

比如下面这个极端 parent bean，它没有指定 class，所以毫无疑问，这个 bean 的作用就是用来充当模板用的 parent bean，此处就必须加上 abstract = true。

```javascript
	<bean id="inheritedTestBeanWithoutClass" abstract="true">
		<property name="name" value="parent"/>
		<property name="age" value="1"/>
	</bean>
1234
```

# 方法注入

一般来说，我们的应用中大多数的 Bean 都是 singleton 的。singleton 依赖 singleton，或者 prototype 依赖 prototype 都很好解决，直接设置属性依赖就可以了。

但是，如果是 singleton 依赖 prototype 呢？这个时候不能用属性依赖，因为如果用属性依赖的话，我们每次其实拿到的还是第一次初始化时候的 bean。

一种解决方案就是不要用属性依赖，每次获取依赖的 bean 的时候从 BeanFactory 中取。这个也是大家最常用的方式了吧。怎么取，我就不介绍了，大部分 Spring 项目大家都会定义那么个工具类的。

另一种解决方案就是这里要介绍的通过使用 Lookup method

## lookup-method

我们来看一下 Spring Reference 中提供的一个例子：

```javascript
package fiona.apple;

// no more Spring imports!

public abstract class CommandManager {

	public Object process(Object commandState) {
		// grab a new instance of the appropriate Command interface
		Command command = createCommand();
		// set the state on the (hopefully brand new) Command instance
		command.setState(commandState);
		return command.execute();
	}

	// okay... but where is the implementation of this method?
	protected abstract Command createCommand();
}
1234567891011121314151617
```

xml 配置 ：

```javascript
<!-- a stateful bean deployed as a prototype (non-singleton) -->
<bean id="myCommand" class="fiona.apple.AsyncCommand" scope="prototype">
	<!-- inject dependencies here as required -->
</bean>
<!-- commandProcessor uses statefulCommandHelper -->
<bean id="commandManager" class="fiona.apple.CommandManager">
	<lookup-method name="createCommand" bean="myCommand"/>
</bean>
12345678
```

Spring 采用 CGLIB 生成字节码的方式来生成一个子类。我们定义的类不能定义为 final class，抽象方法上也不能加 final。
lookup-method 上的配置也可以采用注解来完成，这样就可以不用配置 了，其他不变：

```javascript
public abstract class CommandManager {

	public Object process(Object commandState) {
		MyCommand command = createCommand();
		command.setState(commandState);
		return command.execute();
	}

	@Lookup("myCommand")
	protected abstract Command createCommand();
}
1234567891011
```

> 注意，既然用了注解，要配置注解扫描：<context:component-scan base-package=“com.javadoop” />’

甚至，我们可以像下面这样：

```javascript
public abstract class CommandManager {

	public Object process(Object commandState) {
		MyCommand command = createCommand();
		command.setState(commandState);
		return command.execute();
	}

	@Lookup
	protected abstract MyCommand createCommand();
}
1234567891011
```

> 上面的返回值用了 MyCommand，当然，如果 Command 只有一个实现类，那返回值也可以写 Command。

## replaced-method

记住它的功能，就是替换掉 bean 中的一些方法。

```javascript
public class MyValueCalculator {

	public String computeValue(String input) {
		// some real code...
	}

	// some other methods...
}
12345678
```

方法覆写，注意要实现 MethodReplacer 接口：

```javascript
public class ReplacementComputeValue implements org.springframework.beans.factory.support.MethodReplacer {

	public Object reimplement(Object o, Method m, Object[] args) throws Throwable {
		// get the input value, work with it, and return a computed result
		String input = (String) args[0];
		...
		return ...;
	}
}
123456789
```

配置也很简单：

```javascript
<bean id="myValueCalculator" class="x.y.z.MyValueCalculator">
	<!-- 定义 computeValue 这个方法要被替换掉 -->
	<replaced-method name="computeValue" replacer="replacementComputeValue">
		<arg-type>String</arg-type>
	</replaced-method>
</bean>

<bean id="replacementComputeValue" class="a.b.c.ReplacementComputeValue"/>
12345678
```

> arg-type 明显不是必须的，除非存在方法重载，这样必须通过参数类型列表来判断这里要覆盖哪个方法。

# BeanPostProcessor

应该说 BeanPostProcessor 概念在 Spring 中也是比较重要的。我们看下接口定义：

```javascript
public interface BeanPostProcessor {
	Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException;
	Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException;
}
1234
```

看这个接口中的两个方法名字我们大体上可以猜测 bean 在初始化之前会执行 postProcessBeforeInitialization 这个方法，初始化完成之后会执行 postProcessAfterInitialization 这个方法。但是，这么理解是非常片面的。

首先，我们要明白，除了我们自己定义的 BeanPostProcessor 实现外，Spring 容器在启动时自动给我们也加了几个。如在获取 BeanFactory 的 obtainFactory() 方法结束后的 prepareBeanFactory(factory)，大家仔细看会发现，Spring 往容器中添加了这两个 BeanPostProcessor：ApplicationContextAwareProcessor、ApplicationListenerDetector。

我们回到这个接口本身，读者请看第一个方法，这个方法接受的第一个参数是 bean 实例，第二个参数是 bean 的名字，重点在返回值将会作为新的 bean 实例，所以，没事的话这里不能随便返回个 null。

那意味着什么呢？我们很容易想到的就是，我们这里可以对一些我们想要修饰的 bean 实例做一些事情。但是对于 Spring 框架来说，它会决定是不是要在这个方法中返回 bean 实例的代理，这样就有更大的想象空间了。

最后，我们说说如果我们自己定义一个 bean 实现 BeanPostProcessor 的话，它的执行时机是什么时候？

如果仔细看了代码分析的话，其实很容易知道了，在 bean 实例化完成、属性注入完成之后，会执行回调方法，具体请参见类 AbstractAutowireCapableBeanFactory#initBean 方法。

首先会回调几个实现了 Aware 接口的 bean，然后就开始回调 BeanPostProcessor 的 postProcessBeforeInitialization 方法，之后是回调 init-method，然后再回调 BeanPostProcessor 的 postProcessAfterInitialization 方法。

# 总结

按理说，总结应该写在附录前面，我就不讲究了。

在花了那么多时间后，这篇文章终于算是基本写完了，大家在惊叹 Spring 给我们做了那么多的事的时候，应该透过现象看本质，去理解 Spring 写得好的地方，去理解它的设计思想。

本文的缺陷在于对 Spring 预初始化 singleton beans 的过程分析不够，主要是代码量真的比较大，分支旁路众多。同时，虽然附录条目不少，但是庞大的 Spring 真的引出了很多的概念，希望日后有精力可以慢慢补充一些。