#### MyBatis中当实体类中的属性名和表中的字段名不一样 ，怎么办 ？

##### 实验

```java
import java.io.IOException;
import java.io.InputStream;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.jupiter.api.Test;

import com.learnspring.mybatis.entities.Employee;
import com.learnspring.mybatis.mapper.EmployeeMapper;

class MyBatisTest {

	//1.MyBatis中当实体类中的属性名和表中的字段名不一样 ，怎么办 ？
	/*
	 * 解决方案：
	 * 	1.写sql语句时起别名
	 * 	2.在MyBatis的全局配置文件中开启驼峰命名规则
	 *  3.在Mapper映射文件中使用resultMap来自定义映射规则
	 */
	
	@Test
	void testGetEmployee() throws IOException {
		//1.创建SqlSessionFactory对象
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		//2.获取sqlSession，sqlSession就相当于JDBC中的connection
		SqlSession sqlSession = sqlSessionFactory.openSession();
		try {
		  //3.获取Mapper对象
		  EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
		  //4.调用EmployeeMapper中获取Employee的方法
		  Employee employee = mapper.getEmployeeById(1);
		  System.out.println(employee);
		} finally {
		  //5.关闭sqlSession
		  sqlSession.close();
		}
	}

}
```

mybatis.sql

```sql
/*
SQLyog Ultimate v11.25 (64 bit)
MySQL - 5.5.28 : Database - mybatis
*********************************************************************
*/


/*!40101 SET NAMES utf8 */;

/*!40101 SET SQL_MODE=''*/;

/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
CREATE DATABASE /*!32312 IF NOT EXISTS*/`mybatis` /*!40100 DEFAULT CHARACTER SET gb2312 */;

USE `mybatis`;

/*Table structure for table `departments` */

DROP TABLE IF EXISTS `departments`;

CREATE TABLE `departments` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(100) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=gb2312;

/*Data for the table `departments` */

insert  into `departments`(`id`,`name`) values (1,'教学部'),(2,'教务部'),(3,'运营部'),(4,'咨询部'),(5,'就业部');

/*Table structure for table `employees` */

DROP TABLE IF EXISTS `employees`;

CREATE TABLE `employees` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `last_name` varchar(100) DEFAULT NULL,
  `email` varchar(100) DEFAULT NULL,
  `salary` double(11,2) DEFAULT NULL,
  `dept_id` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `dept_id` (`dept_id`),
  CONSTRAINT `employees_ibfk_1` FOREIGN KEY (`dept_id`) REFERENCES `departments` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=gb2312;

/*Data for the table `employees` */

insert  into `employees`(`id`,`last_name`,`email`,`salary`,`dept_id`) values (1,'HanZong','hybing@atguigu.com',30000.00,1),(2,'Mayun','mayun@alibaba.com',20000.00,2),(3,'Mahuateng','mahuateng@qq.com',10000.00,3);

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
```

Employee.java

```java
public class Employee {

	private Integer id;
	private String lastName;
	private String email;
	private double salary;
	private Integer deptId;

	public Employee(Integer id, String lastName, String email, double salary, Integer deptId) {
		super();
		this.id = id;
		this.lastName = lastName;
		this.email = email;
		this.salary = salary;
		this.deptId = deptId;
	}

	public Employee() {
		super();
	}

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public double getSalary() {
		return salary;
	}

	public void setSalary(double salary) {
		this.salary = salary;
	}

	public Integer getDeptId() {
		return deptId;
	}

	public void setDeptId(Integer deptId) {
		this.deptId = deptId;
	}

	@Override
	public String toString() {
		return "Employee [id=" + id + ", lastName=" + lastName + ", email=" + email + ", salary=" + salary + ", deptId="
				+ deptId + "]";
	}

}
```

EmployeeMapper.java

```
import com.learnspring.mybatis.entities.Employee;

public interface EmployeeMapper {

	Employee getEmployeeById(Integer id);
}

```

EmployeeMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace属性：必须是接口的全类名 -->
<mapper namespace="com.learnspring.mybatis.mapper.EmployeeMapper">
	<!-- 
		id属性：必须是接口中方法的方法名
		resultType属性：必须是方法的返回值的全类名
	 -->
	<select id="getEmployeeById" resultMap="myMap">
		select * from employees where id = #{id}
	</select>
	
	<!-- 自定义高级映射 -->
    <resultMap type="com.learnspring.mybatis.entities.Employee" id="myMap">
    	<!-- 映射主键 -->
    	<id column="id" property="id"/>
    	<!-- 映射其他列 -->
    	<result column="last_name" property="lastName"/>
    	<result column="email" property="email"/>
    	<result column="salary" property="salary"/>
    	<result column="dept_id" property="deptId"/>
    </resultMap>
</mapper>
```

mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<!-- 	<settings> -->
		 <!-- 开启驼峰命名规则 ，可以将数据库中的下划线映射为驼峰命名
		 	例如：last_name可以映射为lastName
		 -->
<!-- 		<setting name="mapUnderscoreToCamelCase" value="true"/> -->
<!-- 	</settings> -->
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver" />
				<property name="url" value="jdbc:mysql://localhost:3306/mybatis" />
				<property name="username" value="root" />
				<property name="password" value="root" />
			</dataSource>
		</environment>
	</environments>
	<!-- 注册映射文件 -->
	<mappers>
		<mapper resource="EmployeeMapper.xml" />
	</mappers>
</configuration>
```

log4j.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
 
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
 
 <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
   <param name="Encoding" value="UTF-8" />
   <layout class="org.apache.log4j.PatternLayout">
    <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m  (%F:%L) \n" />
   </layout>
 </appender>
 <logger name="java.sql">
   <level value="debug" />
 </logger>
 <logger name="org.apache.ibatis">
   <level value="info" />
 </logger>
 <root>
   <level value="debug" />
   <appender-ref ref="STDOUT" />
 </root>
</log4j:configuration>
```

