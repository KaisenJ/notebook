### Spring Bean的作用域之间有什么区别？

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">
    
    <!-- bean的作用域
		可以通过scope属性来指定bean的作用域
			-singleton:默认值。当IOC容器一创建就会创建bean的实例，而且是单例，每次得到的都是同一个
			-prototype：原型的。当IOC容器一创建不再实例化该bean,每次调用getBean方法时再实例化该bean，而且每调用一次创建一个对象
			-request:每次请求实例化一个bean
			-session:在一次会话中共享一个bean
	-->
    <bean id="book" class="com.learnspring.spring.beans.Book" scope="prototype">
    	<property name="id" value="8"></property>
        <property name="title" value="三国志"></property>
        <property name="author" value="陈寿"></property>
        <property name="price" value="30.90"></property>
        <property name="sales" value="10"></property>
    </bean>

</beans>
```



```java
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import com.learnspring.spring.beans.Book;

public class SpringTest{
    //创建IOC容器对象
    ApplicationContext ioc new ClassPathXmlApplicationContext("beans.xml");
    
    @Test
    public void testBook(){
        Book book = (Book) ioc.getBean("book");
        Book book1 = (Book) ioc.getBean("book");
        System.out.println(book == book1);
    }
}
```

```java
public class Book{
    private Integer id;
    private String title;
    private String author;
    private double price;
    private Integer sales;
    
    public Book(){
        System.out.println("Book对象被创建了 → 无参构造器")
    }
    
    public Book(Integer id,String title,String author,double price,Integer sales){
        super();
        this.id = id;
        this.title = title;
        this.author = author;
        this.price = price;
        this.sales = sales;
        System.out.println("全参构造器创建Book对象");
    }
    
    public Book(Integer id,String title,String author,double price){
        super();
        this.id = id;
        this.title = title;
        this.author = author;
        this.price = price;
        System.out.println("不带销量的构造器被调用");
    }
    
    public Book(Integer id,String title,String author,Integer sales){
        super();
        this.id = id;
        this.title = title;
        this.author = author;
        this.sales = sales;
        System.out.println("不带价格的构造器创建Book对象");
    }
    
    public Integer getId(){
        return id;
    }
    
    public void setId(Integer id){
        this.id = id;
    }
    
    public String getTitle(){
        return title;
    }
    
    public void setTitle(String title){
        this.title = title;
    }
    
    public String getAuthor(){
        return author;
    }
    
    public void setAuthor(){
        this.author = author;
    }
    
    public double getPrice(){
        return price;
    }
    
    public void setPrice(double price){
        this.price = price;
    }
    
    public Integer getSales(){
        return sales;
    }
    
    public void setSales(Integer sales){
        this.sales = sales;
    }
    
    @Override
    public String toString(){
        return "Book [id=" + id + ", title=" + title + ", author=" + author + ", price=" + price + ", sales=" + sales + "]";
    }
}
```