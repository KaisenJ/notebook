#### 2.spring框架中，可以直接注入实现类吗，为什么？

可以。但是只能在没有使用Spring AOP时，不会报错。

因为Spring AOP的实现基于动态代理。

如果目标对象实现了接口，那么在默认情况下会采用JDK的动态代理实现AOP

```java
如: 接口 a
     实现类 aimpl
     代理类b
 
//通过JDKProxyFactory创建代理对象
JDKProxyFactory factory = new JDKProxyFactory(aimpl);
AImplProxy b= factory.createProxy();
//这个增强类对象b 只能强转为a，而不能转为aimpl，因为JDK代理得到的b类与aimpl是兄弟关系而非父子
```

解决方式：xml配置，强制使用cglib方式（低版本Spring可能需要引入cglib的jar包）

```xml
<aop:aspectj-autoproxy proxy-target-class="true" />
```



**注意：** 没有接口时，也不会报错。

因为Spring框架，会在这时使用cglib动态代理的方式。

cglib使用继承的方式实现代理。又因为是继承，所以被代理的目标最好不要使用final。