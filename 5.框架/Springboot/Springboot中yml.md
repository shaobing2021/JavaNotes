## 语法

> 对空格要求严格，掌握语法以及和properties对比

```yaml
#properties只能保存键值对
#key-value
name: shaobing
#object
student:
  name: shaobing
  age: 13
#行内写法
studnet: {name: shaobing,age: 3}
#arr
pets:
  - cat
  - dog
  - pig
dogs: [hong,huang,lan]
```

## 注入配置文件

> 编写实体类

```java
@Component  //注册bean到容器中
public class Dog {
    private String name;
    private Integer age;
    
    //有参无参构造、get、set方法、toString()方法  
}
```

思考，我们原来是如何给bean注入属性值的！@Value，给狗狗类测试一下：

```java
@Component //注册bean
public class Dog {    
      @Value("阿黄")    
      private String name;    
      @Value("18")    
      private Integer age;
}
```

在SpringBoot的测试类下注入狗狗输出一下；

```java
@SpringBootTestclass DemoApplicationTests {
    @Autowired //将狗狗自动注入进来    
  	Dog dog;
    @Test    
    public void contextLoads() {        
    	System.out.println(dog); //打印看下狗狗对象    
    }
}
```

## 使用yml配置

使用yml注入javabean

```java
package com.shaobing.pojo;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.context.pro perties.ConfigurationProperties;
import org.springframework.stereotype.Component;
import java.util.Date;
import java.util.List;
import java.util.Map;
@Data
@Component
//有参无参有没有无所谓，同样能够注入
@NoArgsConstructor
@AllArgsConstructor
//通过该注解进行绑定
@ConfigurationProperties(prefix = "dog")
public class Dog {
    private String name;
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Person person;
}
@Data
@Component
public class Person {
    private String name;
    private int age;
}
```

==yml中对应配置==

```
dog:
  name: shaobing
  age: 11
  happy: false
  birth: 2019/11/02
  maps: {k1: v1,k2: v2}
  lists:
    - code
    - music
    - girl
  person:
    name: xiaohuang
    age: 3
```

注意点：使用@ConfigurationProperties会爆红，因此要加上依赖

```xml
<!-- 导入配置文件处理器，配置文件进行绑定就会有提示，需要重启 -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-configuration-processor</artifactId>
  <optional>true</optional>
</dependency>
```

## PropertySource 

**@PropertySource ：**加载指定的配置文件；

**@configurationProperties**：默认从全局配置文件中获取值；

1、我们去在resources目录下新建一个**person.properties**文件

```
name=kuangshen
```

2、然后在我们的代码中指定加载person.properties文件

```java
@PropertySource(value = "classpath:person.properties")
@Component //注册beanpublic 
class Person {
    @Value("${name}")    
    private String name;
    ......  
}
```

## 配置文件占位符

```yaml

person:
    name: qinjiang${random.uuid} # 随机uuid
    age: ${random.int}  # 随机int
    happy: false
    birth: 2000/01/01
    maps: {k1: v1,k2: v2}
    lists:
      - code
      - girl
      - music
    dog:
      name: ${person.hello:other}_旺财   #如果存在hello则走hello，否则就默认ohter
      age: 1
```



通过yaml进行注入步骤

1.写个bean，使用@Component以及@ConfigurationProperties（prefix=“”）注入

2.写yaml，给prefix中赋值，注意要和bean中名字一致

3.注意使用ConfigurationProperties可能会报错

可以指定绑定的文件名：@propertySource（vlue=“classpath：XXX.properties”）

随后需要使用@Value("${name}")

## yaml和propertiess对比

松散绑定：数据库一般下划线，bean中一般大小写

jsr303校验

yaml文件的放置四处具有不同优先级

yaml有测试生产等不同环境，因此多环境配置，可以选择激性活，也可以---进行分离

## 总结

![img](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7KtjyIb9NEaYlz0tCWSiboOYjMibiaov73iaTsiaWEPoArDcAB1Ooibx9uR5JxtacIuicHblEtUI9SrySX2A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

* 编写类，给上component注解
* 则启动时候，会自动加载springapplication路径下的包路径
* 然后扫描表上component注解的类，进行注册bean
* 对于类中的属性赋值可通过@Value，但是springboot中可通过@ConfgurationProperties和yaml共同配置
* 从而之后类使用，只需要@autowired即可

## 松散绑定

> 比如我的yml中写的last-name，这个和lastName是一样的， - 后面跟着的字母默认是大写的。这就是松散绑定。可以测试一下

## jsr303验证

```xml
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

### JSR可验证内容

```xml
import javax.validation.constraints.Email;
点进constraints可以看到可以声明的注解
```

```java
@NotNull(message="名字不能为空")
private String userName;
@Max(value=120,message="年龄最大不能查过120")
private int age;
@Email(message="邮箱格式错误")
private String email;

空检查
@Null       验证对象是否为null
@NotNull    验证对象是否不为null, 无法查检长度为0的字符串
@NotBlank   检查约束字符串是不是Null还有被Trim的长度是否大于0,只对字符串,且会去掉前后空格.
@NotEmpty   检查约束元素是否为NULL或者是EMPTY.
    
Booelan检查
@AssertTrue     验证 Boolean 对象是否为 true  
@AssertFalse    验证 Boolean 对象是否为 false  
    
长度检查
@Size(min=, max=) 验证对象（Array,Collection,Map,String）长度是否在给定的范围之内  
@Length(min=, max=) string is between min and max included.
日期检查
@Past       验证 Date 和 Calendar 对象是否在当前时间之前  
@Future     验证 Date 和 Calendar 对象是否在当前时间之后  
@Pattern    验证 String 对象是否符合正则表达式的规则
.......等等
除此以外，我们还可以自定义一些数据校验规则
```

### 多文件环境配置

疑问？yml和yaml哪个优先级高？

1. `file:./config/`      springboot/config/application.yml    优先级最高1
2. `file:./config/*/`
3. `file:./`  springboot/application.yaml  优先级高2
4. `classpath:/config/`   src/main/java  or src/main/resources/config/applicaiton.yaml
5. `classpath:/`    src/main/resources/applicaiton.yaml   优先级一般（默认）

> 指定生产环境

```yaml
#在application.yml中指定测试环境
spring.profiles.active=test  #有个application-test.yml即可

server:
  port: 8081
spring:
  profiles:
    active: dev
---
server:
  port: 8082
spring:
  profiles: dev
---
server:
  port: 8083
spring:
  profiles: test
```

