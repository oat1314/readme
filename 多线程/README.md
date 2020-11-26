转载:https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484296&idx=1&sn=6bc82072500dda2798f567f1442f25ab&chksm=ebd74289dca0cb9fece89bedeede895b6058c46289b05b918ef5115b3204fbef2e38ac47d7b3&scene=21###wechat_redirect



Java为我们提供了**三个同步工具类**：

- CountDownLatch(闭锁)
- CyclicBarrier(栅栏)
- Semaphore(信号量)

这几个工具类其实说白了就是为了能够**更好控制线程之间的通讯问题**~

# 一、CountDownLatch

## 1.1CountDownLatch简介

> - A synchronization aid that allows one or more threads to wait until a set of operations being performed in other threads completes.

简单来说：CountDownLatch是一个同步的辅助类，**允许一个或多个线程一直等待**，**直到**其它线程**完成**它们的操作。

它常用的API其实就两个:`await()`和`countDown()`

![img](https://mmbiz.qpic.cn/mmbiz_png/2BGWl1qPxib2oibibWqYgqfWXOwAL0uSWmoeA4EibjPKvhQsE7YQk22k4nDrIVEPPfLkajtCeicmgsVYHRYUSdkKYibA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

使用说明：

- count初始化CountDownLatch，然后需要等待的线程调用await方法。await方法会一直受阻塞直到count=0。而其它线程完成自己的操作后，调用`countDown()`使计数器count减1。**当count减到0时，所有在等待的线程均会被释放**
- 说白了就是通过**count变量来控制等待**，如果**count值为0了**(其他线程的任务都完成了)，那就可以继续执行。

## 1.2CountDownLatch例子

例子：3y现在去做实习生了，其他的员工还没下班，3y不好意思先走，等其他的员工都走光了，3y再走。

```
import java.util.concurrent.CountDownLatch;

public class Test {

    public static void main(String[] args) {

        final CountDownLatch countDownLatch = new CountDownLatch(5);

        System.out.println("现在6点下班了.....");

        // 3y线程启动
        new Thread(new Runnable() {
            @Override
            public void run() {

                try {
                    // 这里调用的是await()不是wait()
                    countDownLatch.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("...其他的5个员工走光了，3y终于可以走了");
            }
        }).start();

        // 其他员工线程启动
        for (int i = 0; i < 5; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    System.out.println("员工xxxx下班了");
                    countDownLatch.countDown();
                }
            }).start();
        }
    }
}
```

输出结果：

![img](https://mmbiz.qpic.cn/mmbiz_png/2BGWl1qPxib2oibibWqYgqfWXOwAL0uSWmoC5tLUO50hb7bgXLAuNBYKdPovGeV90KbY2zbDXbZFibfumD8FbFuQpw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

再写个例子：3y现在负责仓库模块功能，但是能力太差了，写得很慢，**别的员工都需要等3y写好了才能继续往下写。**

```
import java.util.concurrent.CountDownLatch;

public class Test {

    public static void main(String[] args) {

        final CountDownLatch countDownLatch = new CountDownLatch(1);

        // 3y线程启动
        new Thread(new Runnable() {
            @Override
            public void run() {

                try {
                    Thread.sleep(5);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("3y终于写完了");
                countDownLatch.countDown();

            }
        }).start();

        // 其他员工线程启动
        for (int i = 0; i < 5; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    System.out.println("其他员工需要等待3y");
                    try {
                        countDownLatch.await();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println("3y终于写完了，其他员工可以开始了！");
                }
            }).start();
        }
    }
}
```

输出结果：

![img](https://mmbiz.qpic.cn/mmbiz_png/2BGWl1qPxib2oibibWqYgqfWXOwAL0uSWmol81sD1ZjEWH6LvLxUA8mI3jxzZ9bsSvibM7y3laAAhEh61qfzwic3Q7w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

参考资料：

- https://blog.csdn.net/qq_19431333/article/details/68940987
- https://blog.csdn.net/panweiwei1994/article/details/78826072
- http://www.importnew.com/15731.html

# 二、CyclicBarrier

## 2.1CyclicBarrier简介

> - A synchronization aid that allows a set of threads to all wait for each other to reach a common barrier point.  CyclicBarriers are useful in programs involving a fixed sized party of threads that must occasionally wait for each other. The barrier is called *cyclic* because it can be re-used after the waiting threads are released.

简单来说：CyclicBarrier允许一组线程互相等待，直到**到达某个公共屏障点**。叫做cyclic是因为当所有等待线程都被释放以后，CyclicBarrier可以**被重用**(对比于CountDownLatch是不能重用的)

使用说明：

- CountDownLatch注重的是**等待其他线程完成**，CyclicBarrier注重的是：当线程**到达某个状态**后，暂停下来等待其他线程，**所有线程均到达以后**，继续执行。

## 2.2CyclicBarrier例子

例子：3y和女朋友约了去广州夜上海吃东西，由于3y和3y女朋友住的地方不同，自然去的路径也就不一样了。于是他俩约定在体育西路地铁站**集合**，约定等到**相互见面的时候**就发一条朋友圈。

```
import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

public class Test {

    public static void main(String[] args) {

        final CyclicBarrier CyclicBarrier = new CyclicBarrier(2);
        for (int i = 0; i < 2; i++) {

            new Thread(() -> {

                String name = Thread.currentThread().getName();
                if (name.equals("Thread-0")) {
                    name = "3y";
                } else {
                    name = "女朋友";
                }
                System.out.println(name + "到了体育西");
                try {

                    // 两个人都要到体育西才能发朋友圈
                    CyclicBarrier.await();
                    // 他俩到达了体育西，看见了对方发了一条朋友圈：
                    System.out.println("跟" + name + "去夜上海吃东西~");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```

测试结果：

![img](https://mmbiz.qpic.cn/mmbiz_png/2BGWl1qPxib2oibibWqYgqfWXOwAL0uSWmocN0GUyGBflKGLOXszPIpROA1IUdoe1UBHialjiaRiaGezR2YkUh2nSdYw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

玩了一天以后，**各自回到家里**，3y和女朋友约定**各自洗澡完之后再聊天**

```
import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

public class Test {

    public static void main(String[] args) {

        final CyclicBarrier CyclicBarrier = new CyclicBarrier(2);
        for (int i = 0; i < 2; i++) {

            new Thread(() -> {

                String name = Thread.currentThread().getName();
                if (name.equals("Thread-0")) {
                    name = "3y";
                } else {
                    name = "女朋友";
                }
                System.out.println(name + "到了体育西");
                try {

                    // 两个人都要到体育西才能发朋友圈
                    CyclicBarrier.await();
                    // 他俩到达了体育西，看见了对方发了一条朋友圈：
                    System.out.println("跟" + name + "去夜上海吃东西~");

                    // 回家
                    CyclicBarrier.await();
                    System.out.println(name + "洗澡");

                    // 洗澡完之后一起聊天
                    CyclicBarrier.await();

                    System.out.println("一起聊天");

                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```

测试结果：

![img](https://mmbiz.qpic.cn/mmbiz_png/2BGWl1qPxib2oibibWqYgqfWXOwAL0uSWmozGm1Mnn9Kqeq34NZ2J79FZXv95SOhBnK01uOMkYpYkbAD4CNQICe5A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

参考资料：

- https://blog.csdn.net/panweiwei1994/article/details/78827000

# 三、Semaphore

## 3.1Semaphore简介

> Semaphores are often used to **restrict the number of threads than can access some (physical or logical) resource**.

------

> - A counting semaphore.  Conceptually, a semaphore maintains a set of permits.  Each {@link #acquire} blocks if necessary until a permit is available, and then takes it.  Each {@link #release} adds a permit,potentially releasing a blocking acquirer.However, no actual permit objects are used; the {@code Semaphore} just
>   keeps a count of the number available and acts accordingly.

Semaphore(信号量)实际上就是可以控制**同时访问的线程个数**，它维护了一组**"许可证"**。

- 当调用`acquire()`方法时，会消费一个许可证。如果没有许可证了，会阻塞起来
- 当调用`release()`方法时，会添加一个许可证。
- 这些"许可证"的个数其实就是一个count变量罢了~

## 3.2Semaphore例子

3y女朋友开了一间卖酸奶的小店，小店一次只能容纳5个顾客挑选购买，超过5个就需要排队啦~~~

```
import java.util.concurrent.Semaphore;

public class Test {

    public static void main(String[] args) {

        // 假设有50个同时来到酸奶店门口
        int nums = 50;

        // 酸奶店只能容纳10个人同时挑选酸奶
        Semaphore semaphore = new Semaphore(10);

        for (int i = 0; i < nums; i++) {
            int finalI = i;
            new Thread(() -> {
                try {
                    // 有"号"的才能进酸奶店挑选购买
                    semaphore.acquire();

                    System.out.println("顾客" + finalI + "在挑选商品，购买...");

                    // 假设挑选了xx长时间，购买了
                    Thread.sleep(1000);

                    // 归还一个许可，后边的就可以进来购买了
                    System.out.println("顾客" + finalI + "购买完毕了...");
                    semaphore.release();



                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }).start();

        }

    }
}
```

输出结果：

![img](https://mmbiz.qpic.cn/mmbiz_png/2BGWl1qPxib2oibibWqYgqfWXOwAL0uSWmo3WCCIARy0U79ySDBlJaArzD4pz8zrH19oEmkh1DichCOte9BibuuVjkg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

反正每次只能**5个客户同时**进酸奶小店购买挑选。

参考资料：

- https://blog.csdn.net/qq_19431333/article/details/70212663
- https://blog.csdn.net/panweiwei1994/article/details/78827248

# 四、总结

Java为我们提供了**三个同步工具类**：

- CountDownLatch(闭锁)

- - 某个线程等待其他线程**执行完毕后**，它才执行(其他线程等待某个线程**执行完毕后**，它才执行)

- CyclicBarrier(栅栏)

- - 一组线程**互相等待至某个状态**，这组线程再同时执行。

- Semaphore(信号量)

- - **控制一组线程同时执行**。

本文简单的介绍了一下这三个同步工具类是干嘛用的，要**深入还得看源码**或者借鉴其他的资料。

最后补充一下之前的思维导图知识点：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/2BGWl1qPxib2oibibWqYgqfWXOwAL0uSWmo4erk9D0bl7lBNt9XV7YhYUQficFJ3ahKe4fCibY2kBcEKibUGbchlTCRA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

参考资料：

- 《Java并发编程实战》
- http://www.cnblogs.com/dolphin0520/p/3920397.html
- https://zhuanlan.zhihu.com/p/27829595





# 前言

今天要研究的是ThreadLocal，这个我在一年前学习JavaWeb基础的时候接触过一次，当时在baidu搜出来的第一篇博文ThreadLocal，在评论下很多开发者认为那博主理解错误，给出了很多有关的链接来指正(可原博主可能没上博客了，一直没做修改)。我也去学习了一番，可惜的是当时还没有记录的习惯，直到现在仅仅记住了一些当时学过的皮毛。

因此，做一些技术的记录是很重要的～同时，ThreadLocal也是面试非常常见的面试题，对Java开发者而言也是一个必要掌握的知识点～

当然了，如果我有写错的地方请大家多多包涵，欢迎在评论下留言指正～

# 一、什么是ThreadLocal

> 声明：本文使用的是JDK 1.8

首先我们来看一下JDK的文档介绍：

```
/**
 * This class provides thread-local variables.  These variables differ from
 * their normal counterparts in that each thread that accesses one (via its
 * {@code get} or {@code set} method) has its own, independently initialized
 * copy of the variable.  {@code ThreadLocal} instances are typically private
 * static fields in classes that wish to associate state with a thread (e.g.,
 * a user ID or Transaction ID).
 * 
 * <p>For example, the class below generates unique identifiers local to each
 * thread.
 * A thread's id is assigned the first time it invokes {@code ThreadId.get()}
 * and remains unchanged on subsequent calls.
 */      
```

结合我的总结可以这样理解：ThreadLocal提供了线程的局部变量，每个线程都可以通过`set()`和`get()`来对这个局部变量进行操作，但不会和其他线程的局部变量进行冲突，**实现了线程的数据隔离**～。

简要言之：往ThreadLocal中填充的变量属于**当前**线程，该变量对其他线程而言是隔离的。

# 二、为什么要学习ThreadLocal？

从上面可以得出：ThreadLocal可以让我们拥有当前线程的变量，那这个作用有什么用呢？？？

## 2.1管理Connection

**最典型的是管理数据库的Connection：**当时在学JDBC的时候，为了方便操作写了一个简单数据库连接池，需要数据库连接池的理由也很简单，频繁创建和关闭Connection是一件非常耗费资源的操作，因此需要创建数据库连接池～

那么，数据库连接池的连接怎么管理呢？？我们交由ThreadLocal来进行管理。为什么交给它来管理呢？？ThreadLocal能够实现**当前线程的操作都是用同一个Connection，保证了事务！**

当时候写的代码：

```
public class DBUtil {
    //数据库连接池
    private static BasicDataSource source;

    //为不同的线程管理连接
    private static ThreadLocal<Connection> local;


    static {
        try {
            //加载配置文件
            Properties properties = new Properties();

            //获取读取流
            InputStream stream = DBUtil.class.getClassLoader().getResourceAsStream("连接池/config.properties");

            //从配置文件中读取数据
            properties.load(stream);

            //关闭流
            stream.close();

            //初始化连接池
            source = new BasicDataSource();

            //设置驱动
            source.setDriverClassName(properties.getProperty("driver"));

            //设置url
            source.setUrl(properties.getProperty("url"));

            //设置用户名
            source.setUsername(properties.getProperty("user"));

            //设置密码
            source.setPassword(properties.getProperty("pwd"));

            //设置初始连接数量
            source.setInitialSize(Integer.parseInt(properties.getProperty("initsize")));

            //设置最大的连接数量
            source.setMaxActive(Integer.parseInt(properties.getProperty("maxactive")));

            //设置最长的等待时间
            source.setMaxWait(Integer.parseInt(properties.getProperty("maxwait")));

            //设置最小空闲数
            source.setMinIdle(Integer.parseInt(properties.getProperty("minidle")));

            //初始化线程本地
            local = new ThreadLocal<>();


        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static Connection getConnection() throws SQLException {
        //获取Connection对象
        Connection connection = source.getConnection();

        //把Connection放进ThreadLocal里面
        local.set(connection);

        //返回Connection对象
        return connection;
    }

    //关闭数据库连接
    public static void closeConnection() {
        //从线程中拿到Connection对象
        Connection connection = local.get();

        try {
            if (connection != null) {
                //恢复连接为自动提交
                connection.setAutoCommit(true);

                //这里不是真的把连接关了,只是将该连接归还给连接池
                connection.close();

                //既然连接已经归还给连接池了,ThreadLocal保存的Connction对象也已经没用了
                local.remove();

            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }


}
```

同样的，Hibernate对Connection的管理也是采用了相同的手法(使用ThreadLocal，当然了Hibernate的实现是更强大的)～

## 2.2避免一些参数传递

**避免一些参数的传递的理解**可以参考一下Cookie和Session：

- 每当我访问一个页面的时候，浏览器都会帮我们从硬盘中找到对应的Cookie发送过去。
- 浏览器是十分聪明的，不会发送别的网站的Cookie过去，只带当前网站发布过来的Cookie过去

浏览器就相当于我们的ThreadLocal，它仅仅会发送我们当前浏览器存在的Cookie(ThreadLocal的局部变量)，不同的浏览器对Cookie是隔离的(Chrome,Opera,IE的Cookie是隔离的【在Chrome登陆了，在IE你也得重新登陆】)，同样地：线程之间ThreadLocal变量也是隔离的….

那上面避免了参数的传递了吗？？其实是避免了。Cookie并不是我们手动传递过去的，并不需要写`<input name= cookie/>`来进行传递参数…

在编写程序中也是一样的：日常中我们要去办理业务可能会有很多地方用到身份证，各类证件，每次我们都要掏出来很麻烦

```
    // 咨询时要用身份证，学生证，房产证等等....
    public void consult(IdCard idCard,StudentCard studentCard,HourseCard hourseCard){

    }

    // 办理时还要用身份证，学生证，房产证等等....
    public void manage(IdCard idCard,StudentCard studentCard,HourseCard hourseCard) {

    }

    //......
```

而如果用了ThreadLocal的话，ThreadLocal就相当于一个机构，ThreadLocal机构做了记录你有那么多张证件。用到的时候就不用自己掏了，问机构拿就可以了。

在咨询时的时候就告诉机构：来，把我的身份证、房产证、学生证通通给他。在办理时又告诉机构：来，把我的身份证、房产证、学生证通通给他。…

```
    // 咨询时要用身份证，学生证，房产证等等....
    public void consult(){

        threadLocal.get();
    }

    // 办理时还要用身份证，学生证，房产证等等....
    public void takePlane() {
        threadLocal.get();
    }
```

这样是不是比自己掏方便多了。

当然了，ThreadLocal可能还会有其他更好的作用，如果知道的同学可在评论留言哦～～～

# 三、ThreadLocal实现的原理

想要更好地去理解ThreadLocal，那就得翻翻它是怎么实现的了～～～

> 声明：本文使用的是JDK 1.8

首先，我们来看一下ThreadLocal的set()方法，因为我们一般使用都是new完对象，就往里边set对象了

```
    public void set(T value) {

        // 得到当前线程对象
        Thread t = Thread.currentThread();

        // 这里获取ThreadLocalMap
        ThreadLocalMap map = getMap(t);

        // 如果map存在，则将当前线程对象t作为key，要存储的对象作为value存到map里面去
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
    }
```

上面有个ThreadLocalMap，我们去看看这是什么？

```
static class ThreadLocalMap {

        /**
         * The entries in this hash map extend WeakReference, using
         * its main ref field as the key (which is always a
         * ThreadLocal object).  Note that null keys (i.e. entry.get()
         * == null) mean that the key is no longer referenced, so the
         * entry can be expunged from table.  Such entries are referred to
         * as "stale entries" in the code that follows.
         */
        static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }
        //....很长
}
```

通过上面我们可以发现的是**ThreadLocalMap是ThreadLocal的一个内部类。用Entry类来进行存储**

我们的**值都是存储到这个Map上的，key是当前ThreadLocal对象**！

如果该Map不存在，则初始化一个：

```
    void createMap(Thread t, T firstValue) {
        t.threadLocals = new ThreadLocalMap(this, firstValue);
    }
```

如果该Map存在，则**从Thread中获取**！

```
    /**
     * Get the map associated with a ThreadLocal. Overridden in
     * InheritableThreadLocal.
     *
     * @param  t the current thread
     * @return the map
     */
    ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
    }
```

Thread维护了ThreadLocalMap变量

```
    /* ThreadLocal values pertaining to this thread. This map is maintained
     * by the ThreadLocal class. */
    ThreadLocal.ThreadLocalMap threadLocals = null
```

从上面又可以看出，**ThreadLocalMap是在ThreadLocal中使用内部类来编写的，但对象的引用是在Thread中**！

于是我们可以总结出：**Thread为每个线程维护了ThreadLocalMap这么一个Map，而ThreadLocalMap的key是LocalThread对象本身，value则是要存储的对象**

有了上面的基础，我们看get()方法就一点都不难理解了：

```
    public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
    }
```

## 3.1ThreadLocal原理总结

1. 每个Thread维护着一个ThreadLocalMap的引用
2. ThreadLocalMap是ThreadLocal的内部类，用Entry来进行存储
3. 调用ThreadLocal的set()方法时，实际上就是往ThreadLocalMap设置值，key是ThreadLocal对象，值是传递进来的对象
4. 调用ThreadLocal的get()方法时，实际上就是往ThreadLocalMap获取值，key是ThreadLocal对象
5. **ThreadLocal本身并不存储值**，它只是**作为一个key来让线程从ThreadLocalMap获取value**。

正因为这个原理，所以ThreadLocal能够实现“数据隔离”，获取当前线程的局部变量值，不受其他线程影响～

# 四、避免内存泄露

我们来看一下ThreadLocal的对象关系引用图：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/2BGWl1qPxib1a3zqXxNAm6O584NJmiar2AVN56p77CkAyxjvhyaZ27HORCZbFeOc1zHcXI1e7CqJqaYiahbW5LBEw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

ThreadLocal内存泄漏的根源是：**由于ThreadLocalMap的生命周期跟Thread一样长，如果没有手动删除对应key就会导致内存泄漏，而不是因为弱引用**。

想要避免内存泄露就要**手动remove()掉**！

# 五、总结

ThreadLocal这方面的博文真的是数不胜数，随便一搜就很多很多～站在前人的肩膀上总结了这篇博文～

最后要记住的是:**ThreadLocal设计的目的就是为了能够在当前线程中有属于自己的变量，并不是为了解决并发或者共享变量的问题**

如果看得不够过瘾，觉得不够深入的同学可参考下面的链接，很多的博主还开展了一些扩展知识，我就不一一展开了～

参考博文：

- http://blog.xiaohansong.com/2016/08/06/ThreadLocal-memory-leak/
- https://www.cnblogs.com/zhangjk1993/archive/2017/03/29/6641745.html#_label2
- http://www.cnblogs.com/dolphin0520/p/3920407.html
- http://www.cnblogs.com/dolphin0520/p/3920407.html
- http://www.iteye.com/topic/103804
- https://www.cnblogs.com/xzwblog/p/7227509.html
- https://blog.csdn.net/u012834750/article/details/71646700
- https://blog.csdn.net/winwill2012/article/details/71625570
- https://juejin.im/post/5a64a581f265da3e3b7aa02d