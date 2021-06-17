# Spring

[Spring 为何需要三级缓存解决循环依赖，而不是二级缓存](https://www.cnblogs.com/semi-sub/p/13548479.html)

[一文告诉你Spring是如何利用"三级缓存"巧妙解决Bean的循环依赖问题的](https://cloud.tencent.com/developer/article/1497692)

---

**Spring循环依赖**

循环依赖场景

> 1、构造器循环依赖
>
> 2、field属性循环依赖

Spring只能解决field属性循环依赖。使用了三级缓存。

> 为什么无法解决构造器循环依赖？
>
> 构造器循环依赖：A依赖B，B依赖A。
>
> 调用A的构造器创建实例a，发现A依赖B，此时去初始化B。调用B的构造器创建实例b，发现B又依赖A，再去初始化A。形成死锁。

Spring bean加载流程：

```java
public class AbstractAutowireCapableBeanFactory {
  protected Object doCreateBean() {
    ...

    // 1.创建Bean实例
    createBeanInstance();

    // 2.填充Bean属性
    populateBean();

    // 3.初始化Bean
    initializeBean();

    ...
  }
}
```



![Spring循环依赖创建bean流程](../images/spring/Spring循环依赖创建bean流程.png)

>文字描述：
>
>1. 使用`context.getBean(A.class)`获取容器内的单例A，若A不存在，就会走A这个Bean的创建流程
>2. 实例化A（此处仅仅是实例化），并将它放进缓存（此时A已经实例化完成，已经可以被引用了）
>3. 初始化A：`@Autowired`依赖注入B（此时需要去容器内获取B）
>4. 会通过`getBean(B)`去容器内找B。但此时B在容器内不存在，就走A这个Bean的创建流程
>5. 实例化B，并将其放入缓存。（此时B也能够被引用了）
>6. 初始化B，`@Autowired`依赖注入A（此时需要去容器内获取A）
>7. 初始化B时会调用`getBean(A)`去容器内找到A，此时A已经实例化并且放进了缓存里，所以这个时候是可以从缓存里取到A
>8. B初始化成功（此时已经成功注入A，已成功持有A的引用），return（注意此处return相当于是返回最上面的`getBean(B)`这句代码，回到了初始化A的流程中）
>9. 因为B实例已经成功返回了，因此最终A也初始化成功

三级缓存：

```java
public class DefaultSingletonBeanRegistry {

  // 一级缓存, 存放完全初始化好的 bean, 从该缓存中取出的 bean 可以直接使用
  /** Cache of singleton objects: bean name to bean instance. */
  private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

  // 三级缓存, 单例对象工厂的cache，存放 bean 工厂对象，用于解决循环依赖
  /** Cache of singleton factories: bean name to ObjectFactory. */
  private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);

  // 二级缓存, 提前曝光的单例对象的cache，存放原始的 bean 对象（尚未填充属性），用于解决循环依赖
  /** Cache of early singleton objects: bean name to bean instance. */
  private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);
}
```

