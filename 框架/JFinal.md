# JFinal

## 创建项目

使用maven创建详情见官网：https://jfinal.com/doc/1-1

简单示例

引入包

```xml
<dependency>
    <groupId>com.jfinal</groupId>
    <artifactId>jfinal-undertow</artifactId>
    <version>2.1</version>
</dependency>
 
<dependency>
    <groupId>com.jfinal</groupId>
    <artifactId>jfinal</artifactId>
    <version>4.9</version>
</dependency>
```

```java
import com.jfinal.core.Controller;

public class HelloController extends Controller{
    
    public void index(){
        renderText("Hello");
    }
}
```

配置项

```java
import com.jfinal.config.*;
import com.jfinal.template.Engine;

public class HelloConfig extends JFinalConfig {
    
    //常量配置
    public void configConstant(Constants me){
        
    }
    
    //路由配置
    public void configRoute(Routes me){
        me.add("/hello",HelloController.class);
    }
    
    public void configEngine(Engine me){
        
    }
    
    //插件配置
    public void configPlugin(Plugins me){
        
    }
    
    public void configInterceptor(Interceptors me){
        
    }
    
    public void configHandler(Handler me){
        
    }
}
```

启动类

```java
import com.jfinal.server.undertow.UndertowServer;
import com.learn.HelloConfig;

public class Starter {
    
    public static void main(String[] args){
        UndertowServer.start(HelloConfig.class,8080,true);
    }
}
```

部署：

1. jfinal-undertow部署 查看官网文档
2. 以rt.jar包部署
3. war包部署

