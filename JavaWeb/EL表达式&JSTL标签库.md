# EL表达式&JSTL标签库

## EL表达式

EL（Expression Language）表达式，表达式语言。

### 作用

EL表达式主要是代替jsp页面中的表达式脚本在jsp页面中进行数据的输出。

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>el-jstl</title>
</head>
<body>
<%
    request.setAttribute("name","阿三");
%>
jsp表达式脚本输出：<%=request.getAttribute("name") ==null ? "" : request.getAttribute("name")%><br>
EL表达式输出：${name}
</body>
</html>
```

EL表达式的格式：${表达式}

EL表达式在输出null值时，输出的是空串。jsp表达式脚本输出的null值时，输出的null字符串。

### EL表达式搜索域数据的顺序

EL表达式主要是在jsp页面中输出域对象的数据。

当四个域中都有相同的key时，EL表达式会按照四个域的从小到大的顺序去进行搜索，找到就输出。

```jsp
<%--EL搜索域数据的顺序--%>
<%
    //设置四个域的值
    request.setAttribute("k","request");
    session.setAttribute("k","session");
    application.setAttribute("k","application");
    pageContext.setAttribute("k","pageContext");
%>
<br>
k的值： ${k}
```

### EL表达式输出Bean的普通属性、数组属性、List集合属性、Map集合属性

Conner.java

```java
package top.jch.learn;

import java.util.*;

public class Conner {

    //普通属性
    private String str;
    //数组属性
    private String[] strs;
    //List集合属性
    private List<String> list;
    //Map集合属性
    private Map<String,Object> map;

    public Conner(String str, String[] strs, List<String> list, Map<String, Object> map) {
        this.str = str;
        this.strs = strs;
        this.list = list;
        this.map = map;
    }

    public Conner() {
        this.str = "str";
        String[] s =  {"一","三","四"};
        this.strs = s;
        this.list = new ArrayList<String>();
        this.list.addAll(Arrays.asList(s));
        this.list.addAll(Arrays.asList(s));
        this.map = new HashMap<String, Object>();
        this.map.put("name","三");
    }

    public String getStr() {
        return str;
    }

    public void setStr(String str) {
        this.str = str;
    }

    public String[] getStrs() {
        return strs;
    }

    public void setStrs(String[] strs) {
        this.strs = strs;
    }

    public List<String> getList() {
        return list;
    }

    public void setList(List<String> list) {
        this.list = list;
    }

    public Map<String, Object> getMap() {
        return map;
    }

    public void setMap(Map<String, Object> map) {
        this.map = map;
    }

    @Override
    public String toString() {
        return "Conner{" +
                "str='" + str + '\'' +
                ", strs=" + Arrays.toString(strs) +
                ", list=" + list +
                ", map=" + map +
                '}';
    }
}
```

jsp信息

```jsp
<%--EL输出JavaBean中的属性--%>
<%
    Conner conner = new Conner();
    pageContext.setAttribute("c",conner);
%><br>
输出Conner：${c}<br>
输出Conner的str属性：${c.str}<br>
输出Conner的strs属性：${c.strs}<br>
输出Conner的list属性：${c.list}<br>
输出Conner的map属性：${c.map}
```

### EL表达式--运算

#### 关系运算

| 关系运算符 | 说明     | 范例                           | 结果  |
| ---------- | -------- | ------------------------------ | ----- |
| == 或 eq   | 等于     | \${ 5 == 5 }  或  $ { 5 eq 5 } | true  |
| != 或 ne   | 不等于   | \${ 5 != 5 } 或 \${ 5 ne 5 }   | false |
| < 或 lt    | 小于     | \${ 3 < 5 } 或 \${ 3 lt 5 }    | true  |
| \> 或 gt   | 大于     | \${  2 > 5 } 或 \${ 2 gt 5 }   | false |
| \<= 或 le  | 小于等于 | \${ 5 <= 12 } 或 \${ 5 le 12}  | true  |
| \>= 或 ge  | 大于等于 | \${ 3 >= 5 } 或 \${ 3 ge 5 }   | false |

#### 逻辑运算

| 逻辑运算符 | 说明     | 范例                                                  | 结果  |
| ---------- | -------- | ----------------------------------------------------- | ----- |
| && 或 and  | 与运算   | \${12==12 && 12 < 11}或\${12\==12 and 12 < 11}        | false |
| \|\|或or   | 或运算   | \${12 == 12 \|\| 12 < 11} 或 \${ 12 == 12 or 12 < 11} | true  |
| !或not     | 取反运算 | \${!true} 或 \${not true}                             | false |

#### 算术运算

| 算术运算符 | 说明 | 范例                            | 结果 |
| ---------- | ---- | ------------------------------- | ---- |
| +          | 加法 | \${12+12}                       | 24   |
| -          | 减法 | \${12 - 2}                      | 10   |
| \*         | 乘法 | \${12 \* 12}                    | 144  |
| /或div     | 除法 | \${144/12} 或 \${ 144 div 12}   | 12   |
| %或mod     | 取模 | \${144 % 10} 或 \${ 144 mod 10} | 4    |

#### empty运算

empty运算可以判断一个数据是否为空，如为空，输出true，否则输出false。

为空的几种情况：

1. 值为null时
2. 值为空串时
3. 值为Object类型数组，且长度为零时
4. List集合，元素个数为零时
5. Map集合，元素个数为零时

示例：

```jsp
<h1>empty运算符</h1>
<%
    request.setAttribute("k1",null);
    request.setAttribute("k2","");
    request.setAttribute("k3",new Object[]{});
    request.setAttribute("k4",new ArrayList<String>());
    request.setAttribute("k5",new HashMap<String,Object>());
%>
${empty k1}<br>
${empty k2}<br>
${empty k3}<br>
${empty k4}<br>
${empty k5}
```

#### 三元运算符

```java
表达式1 ? 表达式2 : 表达式3
```

若表达式1的值为真时，返回表达式2的值；若表达式1的值为假时，返回表达式3的值。

#### "."点运算符 和 [] 中括号运算符

.点运算符，输出JavaBean对象中某个属性的值。

[]中括号运算符，输出有序集合中某个元素的值。且可以输出map集合中key里含有的特殊字符的key的值。

示例：

```jsp
<%--点运算符 和 中括号运算符--%>
<%
    Map<String,Object> map = new HashMap<String,Object>();
    map.put("a.a","a");
    map.put("b-b","b");
    map.put("c+c","c");
    request.setAttribute("map",map);
%>
${map["a.a"]}<br>
${map["b-b"]}<br>
${map["c+c"]}<br>
```

#### EL表达式的11个隐含对象

这11个隐含对象，是EL表达式中自己定义的，可以直接使用。

| 变量             | 类型                 | 作用                                            |
| ---------------- | -------------------- | ----------------------------------------------- |
| pageContext      | PageContextImpl      | 获取jsp中的九大内置对象                         |
| pageScope        | Map<String,Object>   | 获取pageContext域中的数据                       |
| requestScope     | Map<String,Object>   | 获取Request域中的数据                           |
| sessionScope     | Map<String,Object>   | 获取Session域中的数据                           |
| applicationScope | Map<String,Object>   | 获取ServletContext域中的数据                    |
| param            | Map<String,String>   | 获取请求参数的值                                |
| paramValues      | Map<String,String[]> | 获取请求参数的值，获取多个值的时使用            |
| header           | Map<String,String>   | 获取请求头的信息                                |
| headerValues     | Map<String,String[]> | 获取请求头的多个信息                            |
| cookie           | Map<String,Cookie>   | 获取当前请求的Cookie信息                        |
| initParam        | Map<String,String>   | 获取在web.xml中配置的<context-param> 上下文参数 |

##### EL获取四个特定的域中的属性

| 变量             | 域               |
| ---------------- | ---------------- |
| pageScope        | pageContext域    |
| requestScope     | Request域        |
| sessionScope     | Session域        |
| applicationScope | ServletContext域 |

```jsp
<h1>四个特定的域中的属性</h1>
<%
    pageContext.setAttribute("k2","pageContext");
    request.setAttribute("k2","request");
    session.setAttribute("k3","session");
    application.setAttribute("k4","application");
%>
${pageScope.k2}<br>
${requestScope.k2}<br>
${sessionScope.k3}<br>
${applicationScope.k4}<br>
```

##### pageContext对象的使用

1. 协议
2. 服务器ip
3. 服务器端口
4. 获取工程名
5. 获取请求方法
6. 获取客户端ip地址
7. 获取会话的id编号

```jsp
<h1>pageContext对象的使用</h1>
<%--
request.getScheme() 它可以获取请求的协议
request.getServerName() 获取请求的服务器 ip 或域名
request.getServerPort() 获取请求的服务器端口号
getContextPath() 获取当前工程路径
request.getMethod() 获取请求的方式（GET 或 POST）
request.getRemoteHost() 获取客户端的 ip 地址
session.getId() 获取会话的唯一标识
--%>
<%--<%--%>
    <%--pageContext.setAttribute("req",request);--%>
<%--%>--%>
协议：${pageContext.request.scheme}<br>
服务器ip：${pageContext.request.serverName}<br>
服务器端口：${pageContext.request.serverPort}<br>
获取工程路径：${pageContext.request.contextPath}<br>
获取请求方法：${pageContext.request.method}<br>
获取客户端ip地址：${pageContext.request.remoteHost}<br>
获取会话的id编号：${pageContext.session.id}<br>
```

##### EL表达式其他隐含对象的使用

| 变量        | 类型                 | 说明                 |
| ----------- | -------------------- | -------------------- |
| param       | Map<String,String>   | 获取请求参数的值     |
| paramValues | Map<String,String[]> | 获取请求参数的多个值 |

示例

请求地址：http://localhost:8080/test.jsp?name=sz&pwd=111111&hobby=java&hobby=cpp

```jsp
<h1>param&paramValues</h1>
请求参数name值：${param.name}<br>
pwd:${param.pwd}<br>

name：${paramValues.name[0]}<br>
hobby: ${paramValues.hobby[0]}  ${paramValues.hobby[1]}<br>
```

| 变量         | 类型                 | 说明               |
| ------------ | -------------------- | ------------------ |
| header       | Map<String,String>   | 获取请求头         |
| headerValues | Map<String,String[]> | 获取清求头的多个值 |

```jsp
<h1>获取请求头的值</h1>
User-Agent：${header["User-Agent"]}<br>
Connection：${header.Connection}<br>
User-Agent：${headerValues["User-Agent"][0]}<br>
```

| 变量   | 类型               | 说明                     |
| ------ | ------------------ | ------------------------ |
| cookie | Map<String,Cookie> | 获取当前请求的Cookie信息 |

```jsp
<h1>cookie</h1>
Cookie的名称：${cookie.JSESSIONID.name}<br>
Cookie的值：${cookie.JSESSIONID.value}<br>
```

| 变量      | 类型               | 说明                                           |
| --------- | ------------------ | ---------------------------------------------- |
| initParam | Map<String,String> | 获取在web.xml中配置的<context-param>上下文参数 |

web.xml的配置

```xml
<context-param>
    <param-name>name</param-name>
    <param-value>is-name</param-value>
</context-param>
```

jsp中获取

```jsp
<h1>initParam</h1>
${initParam.name}<br>
```

## JSTL标签库

JSTL(JSP Standard Tag Library)：JSP标准标签库。是一个不断完善的开放源代码的JSP标签库。

EL表达式主要是为了替换jsp中的表达式脚本，而标签库则式为了替换代码脚本。让JSP页面更加简洁。

| 功能范围       | URI                                    | 前缀 |
| -------------- | -------------------------------------- | ---- |
| ==核心标签库== | http://java.sun.com/jsp/jstl/core      | c    |
| 格式化         | http://java.sun.com/jsp/jstl/fmt       | fmt  |
| 函数           | http://java.sun.com/jsp/jstl/functions | fn   |
| <s>数据库</s>  | http://java.sun.com/jsp/jstl/sql       | sql  |
| <s>XML</s>     | http://java.sun.com/jsp/jstl/xml       | x    |

引入示例：

```jsp
<%--jstl 核心库--%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%--XML 标签库--%>
<%@ taglib prefix="x" uri="http://java.sun.com/jsp/jstl/xml"%>
<%--FMT标签库--%>
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt"%>
<%--SQL 标签库--%>
<%@ taglib prefix="sql" uri="http://java.sun.com/jsp/jstl/sql" %>
<%--FUNCTIONS 标签库--%>
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
```

### JSTL标签库的使用步骤

1. 导入jstl标签库的jar包；maven项目中引入JSTL依赖。

   jar方式：

   taglibs-standard-impl-1.2.1.jar

   taglibs-standard-spec-1.2.1.jar

   maven方式：

   ```xml
   <dependency>
       <groupId>javax.servlet</groupId>
       <artifactId>jstl</artifactId>
       <version>1.2</version>
   </dependency>
   ```

2. 使用taglib指令引入标签库

   ```jsp
   <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
   ```

### core核心库使用

#### <c:set />

作用：set标签可以往域中保存数据

```jsp
<c:set />
    作用：set 标签可以往域中保存数据
    
    域对象.setAttribute(key,value);
    scope 属性设置保存到哪个域
        page 表示 PageContext 域（默认值）
        request 表示 Request 域
        session 表示 Session 域
        application 表示 ServletContext 域
    var 属性设置 key 是多少
    value 属性设置值
--%>
保存之前：${ sessionScope.abc}<br>
<c:set scope="session" var="abc" value="aaa"/>
保存之后：${ sessionScope.abc}<br>
```

#### <c:if />

```jsp
<%--
    <c:if/>
    用做if判断
        test属性表示判断的条件（使用EL表达式输出）
--%>
<c:if test="${12 == 13}">
    <h6>12等于13</h6>
</c:if>
<c:if test="${12 != 13}">
    <h6>12不等于13</h6>
</c:if>
```

#### \<c:choose>\<c:when>\<c:otherwise>标签

```jsp
<%--
    <c:choose><c:when><c:otherwise>标签
        作用：多路判断。与switch...case...default非常接近
            choose标签开始选择判断
            when标签表示每一种判断情况
                test属性表示当前这种判断的值
            otherwise标签表示剩下的情况

        注意：
            1、标签里不能使用html注释，要使用jsp注释
            2、when标签的父标签一定要是choose标签
--%>
<%
    request.setAttribute("height",180);
%>
<c:choose>
    <c:when test="${requestScope.height > 190}">
        <h1>超高</h1>
    </c:when>
    <c:when test="${requestScope.height > 180}">
        <h2>很高</h2>
    </c:when>
    <c:when test="${requestScope.height > 170}">
        <h3>还可以</h3>
    </c:when>
    <c:otherwise>
        <c:choose>
            <c:when test="${requestScope.height > 160}">
                <h2>160</h2>
            </c:when>
            <c:when test="${requestScope.height > 150}">
                <h2>150</h2>
            </c:when>
            <c:when test="${requestScope.height > 140}">
                <h1>140</h1>
            </c:when>
            <c:otherwise>
                <h1>低于140</h1>
            </c:otherwise>
        </c:choose>
    </c:otherwise>
</c:choose>
```

#### <c:forEach />

```jsp
<%--
    <c:forEach />
        begin属性设置开始的索引
        end属性设置结束的索引
        var属性表示循环的变量（也是当前正在遍历的数据）
--%>
<c:forEach begin="1" end="10" var="i">
    <p>${i}</p>
</c:forEach>
```

##### 遍历Object数组

```jsp
<%--
    遍历Object数组
        items 表示遍历的数据源 → 遍历的集合
        var 当前遍历到的数据
--%>
<%
    request.setAttribute("arr",new Integer[]{123,456,789});
%>
<c:forEach items="${requestScope.arr}" var="item">
    ${item} <br>
</c:forEach>
```

##### 遍历Map集合

```jsp
<%
    Map<String,Object> map1 = new HashMap<>();
    map1.put("k1","v1");
    map1.put("k2","v2");
    map1.put("k3","v3");
    request.setAttribute("mapfor",map1);
%>
<c:forEach items="${requestScope.mapfor}" var="item">
    <p>${item.key} - ${item.value}</p>
</c:forEach>
```

##### 遍历List集合（集合内存放对象）

Item类

```java
package top.jch.learn;

public class Item {

    private Integer id;
    private String name;
    private String info;

    public Item(Integer id, String name, String info) {
        this.id = id;
        this.name = name;
        this.info = info;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getInfo() {
        return info;
    }

    public void setInfo(String info) {
        this.info = info;
    }

    @Override
    public String toString() {
        return "Item{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", info='" + info + '\'' +
                '}';
    }
}
```

jsp页面

```jsp
<%
    List<Item> items = new ArrayList<>();
    for (int i = 0; i <= 10 ; i++) {
        items.add(new Item(i,"name"+(i*i),"info:" + (i*i*i)));
    }
    request.setAttribute("items",items);
%>
<table>
    <tr>
        <th>ID</th>
        <th>name</th>
        <th>info</th>
        <th>遍历信息</th>
    </tr>
    <%--
        items 遍历的集合
            var 游标
            begin 开始索引
            end 结束索引
            step 步长
            varStatus 游标状态
    --%>
    <c:forEach begin="3" end="10" step="3" varStatus="status" items="${requestScope.items}" var="item">
        <tr>
            <td>${item.id}</td>
            <td>${item.name}</td>
            <td>${item.info}</td>
            <td>步长：${status.step} 开始位置：${status.begin} 结束位置：${status.end} 数量：${status.count}</td>
        </tr>
    </c:forEach>
</table>
```

