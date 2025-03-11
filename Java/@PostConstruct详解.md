在 Java 开发中，尤其是在基于 Spring 框架的项目里，我们常常会遇到需要在对象创建并完成依赖注入后，执行一些初始化操作的场景。``@PostConstruct``注解正是为解决此类问题而诞生的，它为我们提供了一种便捷且优雅的方式来处理对象的初始化逻辑。``@PostConstruct``是JSR-250规范定义的注解，用于标记在对象构造完成且依赖注入完成后执行的初始化方法。在Spring框架中的执行顺序为：构造函数 -> @Autowired依赖注入 -> @PostConstruct方法 -> Bean初始化完成

# 一、@PostConstruct 基础概念
@PostConstruct 是 Java EE 提供的JSR-250规范注解，的注解，作用是标记一个方法，该方法会在对象被创建并且依赖注入完成之后，在构造函数执行完毕后自动调用，无需手动调用。这使得我们能够在对象可用之前，完成一些必要的初始化工作，比如加载配置文件、建立数据库连接、初始化缓存等。

![image](https://github.com/user-attachments/assets/72ab8412-b458-425e-96db-bb49888e48ec)

根据官方代码的注释我们可以看出
> 该方法不得具有任何参数，除非是拦截器，在这种情况下，它采用 Interceptors 规范定义的 InvocationContext 对象。
在拦截器类上定义的方法必须具有以下签名之一：
无效 <METHOD>（InvocationContext）
Object <METHOD>（InvocationContext） 引发 Exception
注意：PostConstruct 拦截器方法不得引发应用程序异常，但如果相同的拦截器方法除了生命周期事件之外还插入到业务或超时方法上，则可以声明它抛出检查异常，包括 java.lang.Exception。如果 PostConstruct 拦截器方法返回一个值，则容器将忽略该值。
在非侦听器类上定义的方法必须具有以下签名：
void <方法>（）
应用 PostConstruct 的方法可以是 public、protected、package private 或 private。
该方法不能是静态的，但应用程序客户端除外。
该方法可能是最终方法。
如果该方法引发未经检查的异常，则不得将类投入使用，除非 EJB 可以处理异常，甚至可以从异常中恢复。

# 二、核心原理与执行顺序

@PostConstruct用于标记在对象构造完成且依赖注入完成后执行的初始化方法。在Spring框架中的执行顺序为：
构造函数 -> @Autowired依赖注入 -> @PostConstruct方法 -> Bean初始化完成

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
