在 Java 开发中，尤其是在基于 Spring 框架的项目里，我们常常会遇到需要在对象创建并完成依赖注入后，执行一些初始化操作的场景。``@PostConstruct``注解正是为解决此类问题而诞生的，它为我们提供了一种便捷且优雅的方式来处理对象的初始化逻辑。``@PostConstruct``是JSR-250规范定义的注解，用于标记在对象构造完成且依赖注入完成后执行的初始化方法。在Spring框架中的执行顺序为：构造函数 -> @Autowired依赖注入 -> @PostConstruct方法 -> Bean初始化完成

# 一、@PostConstruct 基础概念
@PostConstruct 是 Java EE 提供的JSR-250规范注解，的注解，作用是标记一个方法，该方法会在对象被创建并且依赖注入完成之后，在构造函数执行完毕后自动调用，无需手动调用。这使得我们能够在对象可用之前，完成一些必要的初始化工作，比如加载配置文件、建立数据库连接、初始化缓存等。

![image](https://github.com/user-attachments/assets/dcd3fae4-9a50-4e38-a7af-0fa369bc7f8d)

根据官方代码的注释我们可以看出
1. ``@PostConstruct`` 注解用于需要在完成依赖注入后执行以执行任何初始化的方法。
2. 所有支持依赖关系注入的类都必须支持此注解。就算注解所在的类不请求注入任何资源，也必须调用带有 ``@PostConstruct ``注释的方法。

**使用条件：**
- 只有一个方法可以被``@PostConstruct`` 标注注解（经测试，在Springboot环境中不生效，可以多个方法标记）
- ``@PostConstruct`` 注解的方法不能有参数，除非是在拦截器类中，在这种情况下，它采用 Interceptors 规范定义的 InvocationContext 对象。
      如果是拦截器类上定义的话，方法必须具有以下签名：
  1. ``void <METHOD>（InvocationContext）``
  2. ``Object <METHOD>（InvocationContext） throws Exception``

     注意：PostConstruct 拦截器方法不得引发应用程序异常，但如果相同的拦截器方法除了生命周期事件之外还插入到业务或超时方法上，则可以声明它抛出检查异常，包括 ``java.lang.Exception``。如果 PostConstruct 拦截器方法返回一个值，则容器将忽略该值。
- 在非拦截器的类上``@PostConstruct`` 注解定义的方法返回值必须是void     
- ``@PostConstruct`` 注解的方法访问修饰符可以是 public、protected、package private 或 private，即所有访问级别都可以。
- ``@PostConstruct`` 注解的方法不能用``static``修饰，除了在Application客户端中。
- ``@PostConstruct`` 注解的方法可以``final``修饰
- ``@PostConstruct`` 注解的方法不能抛出未经检查的异常(unchecked exception),除非

# 二、核心原理与执行顺序

@PostConstruct用于标记在对象构造完成且依赖注入完成后执行的初始化方法。在Spring框架中的执行顺序为：
构造函数 -> @Autowired依赖注入 -> @PostConstruct方法 -> Bean初始化完成
进入``@PostConstruct``的源码中，发现只有``CommonAnnotationBeanPostProcessor``这个类用到
![image](https://github.com/user-attachments/assets/f721d7fa-3e12-4360-890c-d53cc04736da)

# 三、使用示例
以下通过一个简单的 Spring Boot 项目示例来展示``@PostConstruct``的用法。
首先，创建一个普通的 Java 类，并在其中定义一个带有``@PostConstruct``注解的方法：
```java
import org.springframework.stereotype.Component;

@Component
public class ExampleService {

    @Autowired 
    private DataSource dataSource;

    public ExampleService() {
        System.out.println("ExampleService的构造函数被调用");
    }

    @PostConstruct
    public void init() {
        if(dataSource != null) {
            System.out.println("ExampleService的@PostConstruct注解的init方法被调用");
        }
        System.out.println("ExampleService的@PostConstruct注解的init方法被调用");
    }
}
```
然后，启动 Spring Boot 应用程序，观察控制台输出：

从输出结果可以清晰地看到，构造函数先被调用，随后``@PostConstruct``注解的方法被调用。这表明``@PostConstruct``注解的方法确实是在对象创建和依赖注入完成之后执行的。

# 四、失效场景


# 
