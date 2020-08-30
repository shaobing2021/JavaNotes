

### 一、导入数据库驱动

建议：mysql-connector-java-5.1.47.jar

1. 新建lib文件目录
2. 导入mysql-connector-java-5.1.37.jar
3. 右键add as library

### 二、测试

```java
package com.roosevlet.day1;

import java.sql.*;

/**
 * 第一个jdbc程序
 */
public class JdbcDemo1 {
    //1.加载对象
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        //1.固定写法，加载驱动
         Class.forName("com.mysql.jdbc.Driver");
         //2.用户信息和url
         String url1 =
"jdbc:mysql://localhost:3306/shop?useUnicode=true&characterEncoding=utf8&useSSL=true";
        String url = "jdbc:mysql://localhost:3306/shop";
         String username = "root";
        String password = "root";
        //3.连接成功，返回数据库对象,Connection 代表数据库
        Connection connection = DriverManager.getConnection(url1, username, password);
        //4.创建执行sql的对象Statement 执行sql对象
        Statement statement = connection.createStatement();
        //5.执行sql，返回结果
        String sql = "select * from account";
        //封装了查询出来的全部结果
        ResultSet resultSet = statement.executeQuery(sql);

        while (resultSet.next()){
            System.out.println("id="+resultSet.getObject("id"));
            System.out.println("name="+resultSet.getObject("name"));
            System.out.println("money="+resultSet.getObject("money"));
        }
        //6.释放连接
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```

#### 步骤总结

1. 加载驱动
2. 获取数据库DriverManage
3. 获得Sql对象 statement
4. 获得返回结果集
5. 释放连接

### 三、对象详解

1. 加载驱动

```java
Class.forName("com.mysql.jdbc.Driver");
点进Driver类
  本质
public class Driver extends NonRegisteringDriver implements java.sql.Driver {
    public Driver() throws SQLException {
    }
    static {
        try {
            DriverManager.registerDriver(new Driver());
        } catch (SQLException var1) {
            throw new RuntimeException("Can't register driver!");
        }
    }
}
```

2. url

`"jdbc:mysql://主机:端口/数据库?useUnicode=true&characterEncoding=utf8&useSSL=true"`

```java
"jdbc:mysql://localhost:3306/shop?useUnicode=true&characterEncoding=utf8&useSSL=true"
```

3. DriverManage

```
  Connection connection = DriverManager.getConnection(url1, username, password);
  //获取数据库对象
  connection.rollback()
  connection.commit()
  connection.setautocmmit()
```

4. Statement

> 执行sql的对象Statement 执行sql对象

```java
Statement statement = connection.createStatement();
PreparedStatement preparedStatement = connection.prepareStatement();

statement.executeQuery();//查询，返回resultSet
statement.execute();//执行任何sql
statement.executeUpdate();//更新插入删除 都是用这个，返回受影响的行数
statement.executeBatch();//执行多个sql
```

5. ResultSet

> 封装查询结果

* result.getObject  不知道数据类型
* result.getString   知道则使用指定数据类型
* result.getDouble
* result.getInt
* result.getDate
* result.getFloat

```java
result.beforeFirst（）   移动到最前面

result.afterLast （） 移动到最后面

result.next 移动到下一个
result.previous(row)  移动指定行
ResultSet resultSet = statement.executeQuery(sql)
        while (resultSet.next()){
            System.out.println("id="+resultSet.getObject("id"));
            System.out.println("name="+resultSet.getObject("name"));
            System.out.println("money="+resultSet.getObject("money"));
        }
```

6. 释放资源

```
resultSet.close();///先释放结果
statement.close();//释放执行
connection.close();//释放
```

### 四、JdbcUtil

#### 编写util类

```java
package com.roosevlet.day1;

import javax.swing.*;
import java.io.IOException;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

public class JdbcUtils {
    private static String driver = null;
    private static String url = null;
    private static String username = null;
    private static String password = null;
    static {
        //表示src目录下获取文件
        try {
            InputStream in = JdbcUtils.class.getClassLoader().getResourceAsStream("jdbc.properties");
            Properties properties = new Properties();
            properties.load(in);
            driver = properties.getProperty("driver");
            url = properties.getProperty("url");
            password = properties.getProperty("password");
            username = properties.getProperty("username");
            Class.forName(driver);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url,username,password);
    }
    public static void release(Connection connection, Statement statement, ResultSet resultSet){
        if (resultSet!=null){
            try {
                resultSet.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (statement!=null){
            try {
                statement.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (connection!=null){
            try {
                connection.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

> jdbc.properites配置文件

```java
driver = com.mysql.jdbc.Driver
url = jdbc:mysql://localhost:3306/shop?useUnicode=true&characterEncoding=utf8&useSSL=true
username = root
password = root
```

#### 测试

```java
package com.roosevlet.day1;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
public class JdbcDemo2 {
    private static Connection connection = null;
    private static Statement statement = null;
    private static ResultSet resultSet = null;
    public static void main(String[] args) {
        testUpdate();
    }

    /**
     * 删除
     */
    public static void testUpdate(){
        String sql = "UPDATE account SET NAME = 'new' WHERE id = 3";
        try {
            connection = JdbcUtils.getConnection();
            statement = connection.createStatement();
            int i = statement.executeUpdate(sql);
            System.out.println(i);
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,statement,resultSet);
        }
    }

    /**
     * 删除
     */
    public static void testDelete(){
        String sql = "delete from account where id = 2";
        try {
            connection = JdbcUtils.getConnection();
            statement = connection.createStatement();
            int i = statement.executeUpdate(sql);
            System.out.println(i);
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,statement,resultSet);
        }
    }

    /**
     * 插入
     */
    public static void testInsert(){
        String sql = "insert into account values(null,'d','1000')";
        try {
             connection = JdbcUtils.getConnection();
            connection.setAutoCommit(false);
             statement = connection.createStatement();
            int i = statement.executeUpdate(sql);
            System.out.println(i);
            connection.commit();
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,statement,resultSet);
        }
    }

    public static void testQuery(){
        String sql = "select * from account ";
        try {
            connection = JdbcUtils.getConnection();
            connection.setAutoCommit(false);
            statement = connection.createStatement();
            resultSet = statement.executeQuery(sql);
            while (resultSet.next()){
                System.out.println(resultSet.getInt("id"));
                System.out.println(resultSet.getString("name"));
                System.out.println(resultSet.getObject("money"));
            }
            connection.commit();
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,statement,resultSet);
        }
    }
}
```

### 五、Sql注入问题

#### 测试Sql注入

```java
package com.roosevlet.day1;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class JdbcDemo3 {
    private static Connection connection = null;
    private static Statement statement = null;
    private static ResultSet resultSet = null;
    public static void main(String[] args) {
        String username = "lz";
        String password = "12345";
        testlogin(username,password);
    }
    public static void testlogin(String username,String password){
        String sql = "SELECT * FROM `user` WHERE username = '"+username+"' AND `password` = '"+password+"'";
        try {
            connection = JdbcUtils.getConnection();
            statement = connection.createStatement();
            ResultSet resultSet = statement.executeQuery(sql);
            if (resultSet.next()){
                System.out.println(resultSet.getString("username"));
                System.out.println(resultSet.getString("password"));
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,statement,resultSet);
        }
    }
}
```

本质

```
SELECT * FROM `user` WHERE username = ‘’ or '1=1' AND password = '' or '1=1'
```

####  preparedStatement

* 可以防止Sql注入(有转义字符就忽略)
* 效率更好

```java
//区别  继承于statement
public interface PreparedStatement extends Statement
```

```java
package com.roosevlet.day1;
import java.sql.*;
public class JdbcDemo3 {
    private static Connection connection = null;
    private static PreparedStatement preparedstatement = null;
    private static ResultSet resultSet = null;
    public static void main(String[] args) {
        String username = "lz";
        String password = "12345";
        testlogin(username,password);
    }
    public static void testlogin(String username,String password){
        //String sql = "SELECT * FROM `user` WHERE username = '"+username+"' AND `password` = '"+password+"'";
        //区别：使用占位符
        String sql = "select * from user where username = ? and password = ?";
        try {
            connection = JdbcUtils.getConnection();
            //区别:预先编译
            preparedstatement = connection.prepareStatement(sql);
            //手动参数赋值
            preparedstatement.setString(1,username);
            preparedstatement.setString(2,password);
            //注意点，如果是date
            //preparedstatement.setDate(5,new java.sql.Date(new Date().getTime()))
            //执行
            ResultSet resultSet = preparedstatement.executeQuery();
            if (resultSet.next()){
                System.out.println(resultSet.getString("username"));
                System.out.println(resultSet.getString("password"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,preparedstatement,resultSet);
        }
    }
}
```

#### 两者区别

* PreparedStatement 继承了 Statement 的所有功能
* PreparedStatement有预编译的过程
* PreparedStatement使用占位符
* PreparedStatement安全性更高，防止Sql注入，存在转义字符就忽略

```java
Statement与PreparedStatement区别
1.性能区别
Statement statement = conn.createStatement();
PreparedStatement preStatement = conn.prepareStatement(sql);
执行的时候:
ResultSet rSet = statement.executeQuery(sql);
ResultSet pSet = preStatement.executeQuery();
由上可以看出，PreparedStatement有预编译的过程，已经绑定sql，之后无论执行多少遍，都不会再去进行编译，
而 statement 不同，如果执行多少遍，则相应的就要编译多少遍sql，所以从这点看，preStatement 的效率会比 Statement要高一些
2.代码的可读性和可维护性
虽然用PreparedStatement来代替Statement会使代码多出几行,但这样的代码无论从可读性还是可维护性上来说.都比直接用Statement的代码高很多档次:
stmt.executeUpdate("insert into tb_name (col1,col2,col2,col4) values ('"+var1+"','"+var2+"',"+var3+",'"+var4+"')");//stmt是Statement对象实例
perstmt = con.prepareStatement("insert into tb_name (col1,col2,col2,col4) values (?,?,?,?)");
perstmt.setString(1,var1);
perstmt.setString(2,var2);
perstmt.setString(3,var3);
perstmt.setString(4,var4);
perstmt.executeUpdate(); //prestmt是 PreparedStatement 对象实例
3.安全性问题
即使到目前为止,仍有一些人连基本的恶义SQL语法都不知道.
String sql = "select * from tb_name where name= '"+varname+"' and passwd='"+varpasswd+"'";
如果我们把[' or '1' = '1]作为varpasswd传入进来.用户名随意,看看会成为什么?
select * from tb_name where name= 'zhangsan' and passwd = '123' ; trop table tb_name;
select * from tb_name = '随意' and passwd = '' or '1' = '1';
因为'1'='1'肯定成立,所以可以任何通过验证.更有甚者:
把[';drop table tb_name;]作为varpasswd传入进来,则:
select * from tb_name = '随意' and passwd = '';drop table tb_name;有些数据库是不会让你成功的,但也有很多数据库就可以使这些语句得到执行.
4.继承关系
作为 Statement 的子类，PreparedStatement 继承了 Statement 的所有功能，
Statement对象能做的操作Preparedstatement都能做，Preparedstatement能做的Statement不一定能做
```

#### 使用IDEA连接数据库

1. 点击database-- mysql--advanced
2. 点击serverTimezone--Asia/Shanghai

#### 事务

ACID

* 原子性：要么都执行要么都不执行
* 一致性：a+b=10,a改变b随之改变
* 隔离性：多个进程互不干扰
* 持久性：commit或rellback则不可回滚，一旦提交就不可逆

隔离性问题：

* 未提交读
* 提交读
* 可重复读
* 串行化

问题:

* 脏读：一个事务读到另一个事务未提交的数据
* 可重复读：在同一个事务内，重复读取表中的数据，前后读取数据不一致
* 幻读:一个事务无法读取另一个事务提交的数据

#### 代码测试事务

* 可以在catch语句显示定义回滚事务，但默认失败就会回滚

```java
package com.roosevlet.day1;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class JdbcDemo4 {
    public static void main(String[] args) {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
            String sql = "update account set money = money - 500 where id=7";
            String sql1 = "update account set money = money + 500 where id=8";
            connection = JdbcUtils.getConnection();
            //关闭自动提交，开启事务
            connection.setAutoCommit(false);
            preparedStatement = connection.prepareStatement(sql);
            preparedStatement.executeUpdate();
            int i = 1/0;
            preparedStatement = connection.prepareStatement(sql1);
            preparedStatement.executeUpdate();
            //业务执行完毕
            connection.commit();
        } catch (SQLException e) {
            try {
                connection.rollback();//如果失败则回滚事务,如果失败默认回滚，不写也回滚
            } catch (SQLException ex) {
                ex.printStackTrace();
            }
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,preparedStatement,resultSet);
        }
    }
}
```

### 五、数据库连接池

#### 概念

1. 概念：其实就是一个容器(集合)，存放数据库连接的容器。
	    当系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，从容器中获取连接对象，用户访问完之后，会将连接对象归还给容器。

2. 好处：
	1. 节约资源
	2. 用户访问高效

3. 实现：
	1. 标准接口：DataSource   javax.sql包下的
		1. 方法：
			* 获取连接：getConnection()
			* 归还连接：Connection.close()。如果连接对象Connection是从连接池中获取的，那么调用Connection.close()方法，则不会再关闭连接了。而是归还连接

	2. 一般我们不去实现它，有数据库厂商来实现
		1. C3P0：数据库连接池技术
		2. Druid：数据库连接池实现技术，由阿里巴巴提供的

4. C3P0：数据库连接池技术
	* 步骤：
		1. 导入jar包 (两个) c3p0-0.9.5.2.jar mchange-commons-java-0.2.12.jar ，
			* 不要忘记导入数据库驱动jar包
		2. 定义配置文件：
			* 名称： c3p0.properties 或者 c3p0-config.xml
			* 路径：直接将文件放在src目录下即可。

		3. 创建核心对象 数据库连接池对象 ComboPooledDataSource
		4. 获取连接： getConnection
	* 代码：
		 //1.创建数据库连接池对象
        DataSource ds  = new ComboPooledDataSource();
        //2. 获取连接对象
        Connection conn = ds.getConnection();
   
5. Druid：数据库连接池实现技术，由阿里巴巴提供的
	1. 步骤：
		1. 导入jar包 druid-1.0.9.jar
		2. 定义配置文件：
			* 是properties形式的
			* 可以叫任意名称，可以放在任意目录下
		3. 加载配置文件。Properties
		4. 获取数据库连接池对象：通过工厂来来获取  DruidDataSourceFactory
		5. 获取连接：getConnection
	* 代码：
		 //3.加载配置文件
        Properties pro = new Properties();
        InputStream is = DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties");
        pro.load(is);
        //4.获取连接池对象
        DataSource ds = DruidDataSourceFactory.createDataSource(pro);
        //5.获取连接
        Connection conn = ds.getConnection();
   
   2. 定义工具类
   		1. 定义一个类 JDBCUtils
   		2. 提供静态代码块加载配置文件，初始化连接池对象
   		3. 提供方法
   			1. 获取连接方法：通过数据库连接池获取连接
   			2. 释放资源
   			3. 获取连接池的方法

* 最小连接数：10

* 最大连接数：15业务最高承载上限

* 等待超时：100ms

编写连接池：实现接口DataSource

#### 连接池

* DBCP
* C3P0
* Druid ：阿里巴巴

##### DBCP连接池

> #### DBCP连接池
>
> 驱动包：commons-pool-1.6、commons-dbcp-1.4、mysql-connector-java-5.1.37-bin

###### DBCP_utils

```
package com.roosevlet.day1;

import org.apache.commons.dbcp.BasicDataSource;
import org.apache.commons.dbcp.BasicDataSourceFactory;

import javax.sql.DataSource;
import java.io.IOException;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

public class JdbcUtils_DBCP {
    private static DataSource dataSource = null;
    static {
        //表示src目录下获取文件
        try {
            InputStream in = JdbcUtils_DBCP.class.getClassLoader().getResourceAsStream("dbcp.properties");
            Properties properties = new Properties();
            properties.load(in);
            //创建数据源
            DataSource dataSource = BasicDataSourceFactory.createDataSource(properties);

        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    public static Connection getConnection() throws SQLException {
        //数据源自带连接
        return dataSource.getConnection();
    }
    public static void release(Connection connection, Statement statement, ResultSet resultSet){
        if (resultSet!=null){
            try {
                resultSet.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (statement!=null){
            try {
                statement.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (connection!=null){
            try {
                connection.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

###### Dbcp.properties

```java
#连接设置
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/shop
username=root
password=root

#<!-- 初始化连接 -->
initialSize=10

#最大连接数量
maxActive=50

#<!-- 最大空闲连接 -->
maxIdle=20

#<!-- 最小空闲连接 -->
minIdle=5

#<!-- 超时等待时间以毫秒为单位 6000毫秒/1000等于60秒 -->
maxWait=60000


#JDBC驱动建立连接时附带的连接属性属性的格式必须为这样：[属性名=property;]
#注意："user" 与 "password" 两个属性会被明确地传递，因此这里不需要包含他们。
connectionProperties=useUnicode=true;characterEncoding=gbk

#指定由连接池所创建的连接的自动提交（auto-commit）状态。
defaultAutoCommit=true

#driver default 指定由连接池所创建的连接的只读（read-only）状态。
#如果没有设置该值，则“setReadOnly”方法将不被调用。（某些驱动并不支持只读模式，如：Informix）
defaultReadOnly=

#driver default 指定由连接池所创建的连接的事务级别（TransactionIsolation）。
#可用值为下列之一：（详情可见javadoc。）NONE,READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE
defaultTransactionIsolation=READ_UNCOMMITTED
```

###### 测试代码

```java
/**
 * 查找
 */
public static void testQuery(){
    String sql = "select * from account ";
    try {
        connection = JdbcUtils_DBCP.getConnection();
        connection.setAutoCommit(false);
        statement = connection.createStatement();
        resultSet = statement.executeQuery(sql);
        while (resultSet.next()){
            System.out.println(resultSet.getInt("id"));
            System.out.println(resultSet.getString("name"));
            System.out.println(resultSet.getObject("money"));
        }
        connection.commit();
    } catch (SQLException e) {
        e.printStackTrace();
    }finally {
        JdbcUtils_DBCP.release(connection,statement,resultSet);
    }
}
```

##### C3P0连接池

> 驱动包：c3p0-0.9.5.2、mchange-commons-java-0.2.12、mysql-connector-java-5.1.37-bin

###### c3p0_utils

```java
package com.roosevlet.day1;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.apache.commons.dbcp.BasicDataSourceFactory;

import javax.sql.DataSource;
import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

public class JdbcUtils_C3P0 {
    private static ComboPooledDataSource dataSource = null;
    static {
        //表示src目录下获取文件
        try {
            InputStream in = JdbcUtils_C3P0.class.getClassLoader().getResourceAsStream("c3p0-config.xml");
            Properties properties = new Properties();
            properties.load(in);
            /**
             * 利用代码版配置
             * datasource = new ComboPooledDataSource();
             * datasource.setDriverClass();
             * datasource.setUser();
             * ...
             * datasource.setMaxpoolSize()
             */
            //创建数据源
            dataSource =new ComboPooledDataSource();

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static Connection getConnection() throws SQLException {
        //数据源自带连接
        return dataSource.getConnection();
    }
    public static void release(Connection connection, Statement statement, ResultSet resultSet){
        if (resultSet!=null){
            try {
                resultSet.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (statement!=null){
            try {
                statement.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (connection!=null){
            try {
                connection.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

###### c3p0-config.xml

```java
<c3p0-config>
  <!-- 使用默认的配置读取连接池对象 -->
  <default-config>
   <!--  连接参数 -->
    <property name="driverClass">com.mysql.jdbc.Driver</property>
    <property name="jdbcUrl">jdbc:mysql://localhost:3306/shop</property>
    <property name="user">root</property>
    <property name="password">root</property>
    
    <!-- 连接池参数 -->
    <property name="initialPoolSize">5</property>
    <property name="maxPoolSize">10</property>
    <property name="checkoutTimeout">3000</property>
  </default-config>
<!--如果ComboPooledDataSource ds = new ComboPooledDataSouce("otherc3p0")
则表示使用下连接池，可以写Orcal的
 -->
  <named-config name="otherc3p0"> 
    <!--  连接参数 -->
    <property name="driverClass">com.mysql.jdbc.Driver</property>
    <property name="jdbcUrl">jdbc:mysql://localhost:3306/day25</property>
    <property name="user">root</property>
    <property name="password">root</property>
    
    <!-- 连接池参数 -->
    <property name="initialPoolSize">5</property>
    <property name="maxPoolSize">8</property>
    <property name="checkoutTimeout">1000</property>
  </named-config>
</c3p0-config>
```

###### 测试代码

```java
public static void testQuery(){
    String sql = "select * from account ";
    try {
        connection = JdbcUtils_C3P0.getConnection();
        connection.setAutoCommit(false);
        statement = connection.createStatement();
        resultSet = statement.executeQuery(sql);
        while (resultSet.next()){
            System.out.println(resultSet.getInt("id"));
            System.out.println(resultSet.getString("name"));
            System.out.println(resultSet.getObject("money"));
        }
        connection.commit();
    } catch (SQLException e) {
        e.printStackTrace();
    }finally {
        JdbcUtils_C3P0.release(connection,statement,resultSet);
    }
}
```

##### Druid连接池

```java
public class DruidDemo {
    public static void main(String[] args) throws Exception {
        //1.导入jar包
        //2.定义配置文件
        //3.加载配置文件
        Properties pro = new Properties();
        InputStream is = DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties");
        pro.load(is);
        //4.获取连接池对象
        DataSource ds = DruidDataSourceFactory.createDataSource(pro);
        //5.获取连接
        Connection conn = ds.getConnection();
        System.out.println(conn);

    }
}
```

###### Druid.properties

```
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql:///db3
username=root
password=root
# 初始化连接数量
initialSize=5
# 最大连接数
maxActive=10
# 最大等待时间
maxWait=3000
```

##### Spring JDBC

> 概念
>
> * Spring框架对JDBC的简单封装。提供了一个JDBCTemplate对象简化JDBC的开发
> * 步骤：
> 	1. 导入jar包
> 	2. 创建JdbcTemplate对象。依赖于数据源DataSource
> 		* JdbcTemplate template = new JdbcTemplate(ds);
>
> 	3. 调用JdbcTemplate的方法来完成CRUD的操作
> 		* update():执行DML语句。增、删、改语句
> 		* queryForMap():查询结果将结果集封装为map集合，将列名作为key，将值作为value 将这条记录封装为一个map集合
> 			* 注意：这个方法查询的结果集长度只能是1
> 		* queryForList():查询结果将结果集封装为list集合
> 			* 注意：将每一条记录封装为一个Map集合，再将Map集合装载到List集合中
> 		* query():查询结果，将结果封装为JavaBean对象
> 			* query的参数：RowMapper
> 				* 一般我们使用BeanPropertyRowMapper实现类。可以完成数据到JavaBean的自动封装
> 				* new BeanPropertyRowMapper<类型>(类型.class)
> 		* queryForObject：查询结果，将结果封装为对象
> 			* 一般用于聚合函数的查询

###### 测试代码

```java
package cn.itcast.jdbctemplate;

import cn.itcast.domain.Emp;
import cn.itcast.utils.JDBCUtils;
import org.junit.Test;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;

import java.sql.Date;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;
import java.util.Map;

public class JdbcTemplateDemo2 {

    //Junit单元测试，可以让方法独立执行


    //1. 获取JDBCTemplate对象
    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
    /**
     * 1. 修改1号数据的 salary 为 10000
     */
    @Test
    public void test1(){

        //2. 定义sql
        String sql = "update emp set salary = 10000 where id = 1001";
        //3. 执行sql
        int count = template.update(sql);
        System.out.println(count);
    }

    /**
     * 2. 添加一条记录
     */
    @Test
    public void test2(){
        String sql = "insert into emp(id,ename,dept_id) values(?,?,?)";
        int count = template.update(sql, 1015, "郭靖", 10);
        System.out.println(count);

    }

    /**
     * 3.删除刚才添加的记录
     */
    @Test
    public void test3(){
        String sql = "delete from emp where id = ?";
        int count = template.update(sql, 1015);
        System.out.println(count);
    }

    /**
     * 4.查询id为1001的记录，将其封装为Map集合
     * 注意：这个方法查询的结果集长度只能是1
     */
    @Test
    public void test4(){
        String sql = "select * from emp where id = ? or id = ?";
        Map<String, Object> map = template.queryForMap(sql, 1001,1002);
        System.out.println(map);
        //{id=1001, ename=孙悟空, job_id=4, mgr=1004, joindate=2000-12-17, salary=10000.00, bonus=null, dept_id=20}

    }

    /**
     * 5. 查询所有记录，将其封装为List
     */
    @Test
    public void test5(){
        String sql = "select * from emp";
        List<Map<String, Object>> list = template.queryForList(sql);

        for (Map<String, Object> stringObjectMap : list) {
            System.out.println(stringObjectMap);
        }
    }

    /**
     * 6. 查询所有记录，将其封装为Emp对象的List集合
     */
    @Test
    public void test6(){
        String sql = "select * from emp";
        List<Emp> list = template.query(sql, new RowMapper<Emp>() {
            @Override
            public Emp mapRow(ResultSet rs, int i) throws SQLException {
                Emp emp = new Emp();
                int id = rs.getInt("id");
                String ename = rs.getString("ename");
                int job_id = rs.getInt("job_id");
                int mgr = rs.getInt("mgr");
                Date joindate = rs.getDate("joindate");
                double salary = rs.getDouble("salary");
                double bonus = rs.getDouble("bonus");
                int dept_id = rs.getInt("dept_id");
                emp.setId(id);
                emp.setEname(ename);
                emp.setJob_id(job_id);
                emp.setMgr(mgr);
                emp.setJoindate(joindate);
                emp.setSalary(salary);
                emp.setBonus(bonus);
                emp.setDept_id(dept_id);

                return emp;
            }
        });


        for (Emp emp : list) {
            System.out.println(emp);
        }
    }

    /**
     * 6. 查询所有记录，将其封装为Emp对象的List集合
     */

    @Test
    public void test6_2(){
        String sql = "select * from emp";
        List<Emp> list = template.query(sql, new BeanPropertyRowMapper<Emp>(Emp.class));
        for (Emp emp : list) {
            System.out.println(emp);
        }
    }

    /**
     * 7. 查询总记录数
     */

    @Test
    public void test7(){
        String sql = "select count(id) from emp";
        Long total = template.queryForObject(sql, Long.class);
        System.out.println(total);
    }

}

```



##### 结论

无论使用什么数据源，本质还是一样，Datasource接口不会变，方法就不会变

三种连接池的说明

https://www.cnblogs.com/lizm166/p/9804649.html

* DBCP、Druid是使用properties配置，c3p0使用xml 直接new ComboPooledDataSource