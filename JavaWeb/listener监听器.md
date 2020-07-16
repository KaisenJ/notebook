# Listener监听器

1. javaweb三大组件之一。

   JavaWeb三大组件：

   1. Servlet
   2. Filter过滤器
   3. Listener监听器

2. Listener是JavaEE的规范，即接口。

3. 作用：监听某事物的变化，然后通过回调函数，反馈给客户(程序)去做一些响应的处理。

## ServletContextListener监听器

ServletContextListener可以监听ServletContext对象的创建和销毁。

ServletContext对象在web工程启动的时候创建，在web工程停止的时候销毁。

监听到创建和销毁之后会分别调用ServletContextListener监听器的方法反馈。

```java
package javax.servlet;

import java.util.EventListener;

/** 
 * Interface for receiving notification events about ServletContext
 * lifecycle changes.
 *
 * <p>In order to receive these notification events, the implementation
 * class must be either declared in the deployment descriptor of the web
 * application, annotated with {@link javax.servlet.annotation.WebListener},
 * or registered via one of the addListener methods defined on
 * {@link ServletContext}.
 *
 * <p>Implementations of this interface are invoked at their
 * {@link #contextInitialized} method in the order in which they have been
 * declared, and at their {@link #contextDestroyed} method in reverse
 * order.
 *
 * @see ServletContextEvent
 *
 * @since Servlet 2.3
 */
public interface ServletContextListener extends EventListener {

    /**
     * Receives notification that the web application initialization
     * process is starting.
     *
     * <p>All ServletContextListeners are notified of context
     * initialization before any filters or servlets in the web
     * application are initialized.
     *
     * @param sce the ServletContextEvent containing the ServletContext
     * that is being initialized
     *
     * @implSpec
     * The default implementation takes no action.
     */
    default public void contextInitialized(ServletContextEvent sce) {}

    /**
     * Receives notification that the ServletContext is about to be
     * shut down.
     *
     * <p>All servlets and filters will have been destroyed before any
     * ServletContextListeners are notified of context
     * destruction.
     *
     * @param sce the ServletContextEvent containing the ServletContext
     * that is being destroyed
     *
     * @implSpec
     * The default implementation takes no action.
     */
    default public void contextDestroyed(ServletContextEvent sce) {}
}
```

示例：

java

```java
package top.jch.learn;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;

/**
 * @author: Administrator
 * @create: 2020/07/03 14:04
 **/
//@WebListener //注解配置监听器 与 xml配置监听器 二选一
public class TestListener implements ServletContextListener {

    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("ServletContext 创建");
    }

    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("ServletContext 销毁");
    }
}
```

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!-- 配置监听器 -->
    <listener>
        <listener-class>top.jch.learn.TestListener</listener-class>
    </listener>
</web-app>
```

### 其它监听器

| 监听器                          | 作用                               |
| ------------------------------- | ---------------------------------- |
| ServletContextListener          | 监听ServletContext                 |
| ServletRequestListener          | 监听ServletRequest                 |
| ServletRequestAttributeListener | 监听ServletRequest的属性增删改操作 |
| ServletContextAttributeListener | 监听ServletContext的属性增删改操作 |

