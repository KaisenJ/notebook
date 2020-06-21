##### Spring支持的常用数据库事务传播属性和事务隔离级别

测试sql

```java
/*
SQLyog Ultimate v11.25 (64 bit)
MySQL - 5.5.28 : Database - spring_transaction
*********************************************************************
*/


/*!40101 SET NAMES utf8 */;

/*!40101 SET SQL_MODE=''*/;

/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
CREATE DATABASE /*!32312 IF NOT EXISTS*/`spring_transaction` /*!40100 DEFAULT CHARACTER SET gb2312 */;

USE `spring_transaction`;

/*Table structure for table `account` */

DROP TABLE IF EXISTS `account`;

CREATE TABLE `account` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(100) DEFAULT NULL,
  `balance` double(11,2) unsigned DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=gb2312;

/*Data for the table `account` */

insert  into `account`(`id`,`username`,`balance`) values (1,'HanZong',100.00);

/*Table structure for table `book` */

DROP TABLE IF EXISTS `book`;

CREATE TABLE `book` (
  `isbn` varchar(100) NOT NULL,
  `name` varchar(100) DEFAULT NULL,
  `price` double(11,2) DEFAULT NULL,
  PRIMARY KEY (`isbn`)
) ENGINE=InnoDB DEFAULT CHARSET=gb2312;

/*Data for the table `book` */

insert  into `book`(`isbn`,`name`,`price`) values ('1001','Spring',60.00),('1002','SpringMVC',50.00);

/*Table structure for table `book_stock` */

DROP TABLE IF EXISTS `book_stock`;

CREATE TABLE `book_stock` (
  `isbn` varchar(100) NOT NULL,
  `stock` int(11) DEFAULT NULL,
  PRIMARY KEY (`isbn`)
) ENGINE=InnoDB DEFAULT CHARSET=gb2312;

/*Data for the table `book_stock` */

insert  into `book_stock`(`isbn`,`stock`) values ('1001',100),('1002',100);

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
```

beans-tx.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd">

	<!-- 设置自动扫描的包 -->
	<context:component-scan base-package="com.learnspring.spring.tx"></context:component-scan>

	<!-- 引入外部的属性文件 -->
	<context:property-placeholder location="classpath:druid_tx.properties" />

	<!-- 配置数据源 -->
	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
		<property name="username" value="${jdbc.username}"></property>
		<property name="password" value="${jdbc.password}"></property>
		<property name="url" value="${jdbc.url}"></property>
		<property name="driverClassName" value="${jdbc.driverClassName}"></property>

		<property name="initialSize" value="${jdbc.initialSize}"></property>
		<property name="minIdle" value="${jdbc.minIdle}"></property>
		<property name="maxActive" value="${jdbc.maxActive}"></property>
		<property name="maxWait" value="${jdbc.maxWait}"></property>
	</bean>

	<!-- 配置JdbcTemplate -->
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<!-- 配置数据源属性值 -->
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	
	<!-- 配置事务管理器 -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<!-- 配置数据源属性值 -->
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	
	<!-- 开启事务注解支持 
		如果事务管理器的id属性值为transactionManager，那么transaction-manager属性可以省略不写
	-->
<!-- 	<tx:annotation-driven transaction-manager="transactionManager"/> -->
	<tx:annotation-driven/>
</beans>

```

druid_tx.properties

```java
jdbc.username=root
jdbc.password=root
jdbc.url=jdbc:mysql://localhost:3306/spring_transaction
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.initialSize=10
jdbc.minIdle=5
jdbc.maxActive=20
jdbc.maxWait=5000
```

TxTest.java

```java
import java.util.ArrayList;
import java.util.List;

import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import com.learnspring.spring.tx.dao.BookShopDao;
import com.learnspring.spring.tx.service.BookShopService;
import com.learnspring.spring.tx.service.Cashier;

class TxTest {
	
	//创建IOC容器对象
	ApplicationContext ioc = new ClassPathXmlApplicationContext("beans-tx.xml");
	
	@Test
	void testBookShopDao() {
		//获取BookDao
		BookShopDao bookShopDao = (BookShopDao) ioc.getBean("bookShopDao");
		double bookPrice = bookShopDao.getBookPriceByIsbn("1001");
		System.out.println(bookPrice);
		//更新图书的库存
		bookShopDao.updateBookStock("1001");
		//更新账户的余额
		bookShopDao.updateAccountBalance(1, bookPrice);
	}
	
	@Test
	void testBookShopService() {
		BookShopService bookShopService = (BookShopService) ioc.getBean("bookShopService");
		bookShopService.purchase(1, "1001");
	}
	
	@Test
	void testCashier() {
		Cashier cashier = (Cashier) ioc.getBean("cashier");
		//创建List
		List<String> isbns = new ArrayList<>();
		isbns.add("1001");
		isbns.add("1002");
		//去结账
		cashier.checkout(1, isbns);
	}

}
```

BookShopDao.java

```java
public interface BookShopDao {

	/**
	 * 根据书号查询图书的价格
	 * 
	 * @param isbn
	 * @return
	 */
	double getBookPriceByIsbn(String isbn);

	/**
	 * 根据书号更新图书的库存，每次只买一本图书
	 * 
	 * @param isbn
	 */
	void updateBookStock(String isbn);

	/**
	 * 根据用户的id和图书的价格更新用户的账户余额
	 * 
	 * @param userId
	 * @param bookPrice
	 */
	void updateAccountBalance(int userId, double bookPrice);
}
```

BookShopDaoImpl.java

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;

import com.learnspring.spring.tx.dao.BookShopDao;

@Repository("bookShopDao")
public class BookShopDaoImpl implements BookShopDao {

	@Autowired
	private JdbcTemplate jdbcTemplate;

	@Override
	public double getBookPriceByIsbn(String isbn) {
		// 写sql语句
		String sql = "select price from book where isbn = ?";
		// 调用JdbcTemplate中的queryForObject方法
		Double bookPrice = jdbcTemplate.queryForObject(sql, Double.class, isbn);
		return bookPrice;
	}

	@Override
	public void updateBookStock(String isbn) {
		// 写sql语句
		String sql = "update book_stock set stock = stock - 1 where isbn = ?";
		// 调用JdbcTemplate中的update方法
		jdbcTemplate.update(sql, isbn);
	}

	@Override
	public void updateAccountBalance(int userId, double bookPrice) {
		// 写sql语句
		String sql = "update account set balance = balance - ? where id = ?";
		// 调用JdbcTemplate中的update方法
		jdbcTemplate.update(sql, bookPrice, userId);
	}

}
```

BookShopService.java

```java
public interface BookShopService {

	/**
	 * 买东西
	 * 
	 * @param userId
	 * @param isbn
	 */
	void purchase(int userId, String isbn);
}

```

Cashier.java

```java
import java.util.List;

public interface Cashier {

	/**
	 * 去结账
	 * 
	 * @param userId
	 * @param isbns
	 */
	void checkout(int userId, List<String> isbns);
}
```

BookShopServiceImpl.java

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Isolation;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;

import com.learnspring.spring.tx.dao.BookShopDao;
import com.learnspring.spring.tx.service.BookShopService;
/**
 * @Transactional注解
 * 	该注解可以添加到类上，也可以添加到方法上
 * 	如果添加到类上，那么类中所有的方法都添加上了事务
 * 	如果添加到方法上，只有添加了该注解的方法才添加了事务
 */
//@Transactional
@Service("bookShopService")
public class BookShopServiceImpl implements BookShopService {

	@Autowired
	private BookShopDao bookShopDao;
	
	//1.请简单介绍Spring支持的常用数据库事务传播属性和事务隔离级别？
	
	/**
	 * 事务的属性：
	 * 	1.★propagation：用来设置事务的传播行为
	 * 		事务的传播行为：一个方法运行在了一个开启了事务的方法中时，当前方法是使用原来的事务还是开启一个新的事务
	 * 		-Propagation.REQUIRED：默认值，使用原来的事务
	 * 		-Propagation.REQUIRES_NEW：将原来的事务挂起，开启一个新的事务
	 * 	2.★isolation：用来设置事务的隔离级别
	 * 		-Isolation.REPEATABLE_READ：可重复读，MySQL默认的隔离级别
	 * 		-Isolation.READ_COMMITTED：读已提交，Oracle默认的隔离级别，开发时通常使用的隔离级别
	 */
	@Transactional(propagation=Propagation.REQUIRED,isolation=Isolation.READ_COMMITTED)
	@Override
	public void purchase(int userId, String isbn) {
		//1.获取要买的图书的价格
		double bookPrice = bookShopDao.getBookPriceByIsbn(isbn);
//		System.out.println(bookPrice);
		//2.更新图书的库存
		bookShopDao.updateBookStock(isbn);
		//3.更新用户的余额
		bookShopDao.updateAccountBalance(userId, bookPrice);
//		double bookPriceByIsbn = bookShopDao.getBookPriceByIsbn(isbn);
//		System.out.println(bookPriceByIsbn);
	}

}
```

CashierImpl.java

```java
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import com.learnspring.spring.tx.service.BookShopService;
import com.learnspring.spring.tx.service.Cashier;

@Service("cashier")
public class CashierImpl implements Cashier {

	@Autowired
	private BookShopService bookShopService;
	
	@Transactional
	@Override
	public void checkout(int userId, List<String> isbns) {
		for (String isbn : isbns) {
			//调用BookShopService中买东西的方法
			bookShopService.purchase(userId, isbn);
		}
	}

}
```

