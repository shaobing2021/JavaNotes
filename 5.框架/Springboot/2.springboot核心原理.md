### pox.xml解析

* 点进去parent

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-dependencies</artifactId>
  <version>2.3.1.RELEASE</version>
</parent>
```

* 继续点进去,对应组件版本，因此只需要start，不需要指定版本

```xml
<jedis.version>3.3.0</jedis.version>
...	
```

* ​	pom.xml

```xml
//控制版本与打包等内容
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
</parent>
//实现http接口，包含了springmvc，使用tomcat作为默认服务器
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
//用于测试
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
</dependency>
//热部署
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
</dependency>
```

### 启动器

```xml
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

因此只需要使用spring-boot-starter-xxx导入功能

### 注解解析

#### main函数

```java
package com.roosevelt.demo;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
//标注这个类是一个springboot应用，启动类下所有资源
@SpringBootApplication
public class DemoApplication {
	//启动springboot应用
	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}
}
```

> springbootApplication注解

```java
@Target(ElementType.TYPE) // 注解的适用范围，其中TYPE用于描述类、接口（包括包注解类型）或enum声明
@Retention(RetentionPolicy.RUNTIME) // 注解的生命周期，保留到class文件中（三个生命周期）
@Documented // 表明这个注解应该被javadoc记录
@Inherited // 子类可以继承该注解
@SpringBootConfiguration // 继承了Configuration，表示当前是注解配置类
@EnableAutoConfiguration // 开启springboot的注解功能，springboot的四大神器之一，其借助@import的帮助
@ComponentScan(excludeFilters = { // 扫描路径设置（具体使用待确认）
@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
      @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```

主要的三个注解

* @Configuration（@SpringBootConfiguration点开查看发现里面还是应用了@Configuration
* @EnableAutoConfiguration 开启springboot的注解功能，springboot的四大神器之一，其借助@import的帮助
* @ComponentScan // 扫描路径设置

> @SpringBootConfiguration

点进去发现最终是`component`说明这是一个配置类

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {
@Component  //表示是一个组件
```

> @EnableAutoConfiguration   开启自动配置功能

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage  //自动配置包
@Import(AutoConfigurationImportSelector.class)  //自动导入选择器
public @interface EnableAutoConfiguration {

//自动配置包
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import(AutoConfigurationPackages.Registrar.class)  
public @interface AutoConfigurationPackage {
-----------------------------------------------------------------------
static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {
		@Override
		public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
			register(registry, new PackageImports(metadata).getPackageNames().toArray(new String[0]));
      //计算new packageImports的值，可以看到result为com.shaobing
		}
		@Override
		public Set<Object> determineImports(AnnotationMetadata metadata) {
			return Collections.singleton(new PackageImports(metadata));
		}
	}
//该类将主配置类（@SpringBootApplication标注的类）所在包及下面所有子包中所有组件扫描到Spring容器中
---------------------------------------------------------------------------  
//看自动导入选择器AutoConfigurationImportSelector，加载META-INF下所有配置文件  
  public String[] selectImports(AnnotationMetadata annotationMetadata) {
//获取所有的配置
protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
		AnnotationAttributes attributes = getAttributes(annotationMetadata);
//有了自动配置类，免去手动编写配置注入功能注解等的工作
		List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
//如何获取配置的呢
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
		List<String> configurations = 
      //最重要的springFactoriesLoader，能够加载的类，参数类加载器
      SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
				getBeanClassLoader());
  Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
				+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
	}
protected Class<?> getSpringFactoriesLoaderFactoryClass() {
		return EnableAutoConfiguration.class;
//自动配置的核心文件
META-INF/spring.factories
//核心loadSpringFactory


```

![image-20200710112023452](https://gitee.com/shaobing2021/typora/raw/master/img/20200728100848.png)



```java
//加载完这些自动配置类，自动配置类内部
@Configuration(proxyBeanMethods = false)  //是一个组件
public class WebMvcAutoConfiguration {
		@Bean
		@ConditionalOnMissingBean   //配置视图解析器
		public InternalResourceViewResolver defaultViewResolver() {
}
```

##### 自动装配原理

Springboot所有自动配置都是再启动的时候扫描并加载。spring.factories所有的自动配置类都在里面，但是不一定生效，要判断条件是否成立。只有导入勒对应的start，就有对应的启动器，有了启动器，自动装配就会生效，然后配置成功。

1.springboot启动的时候从类路径下/META-INF/spring.factories获取指定的值

2.将这些自动配置的类导入容器，但是不一定生效，要判断条件是否成立

3.以前自动配置的东西，只要导入对应的start，就会有对应的启动器。然后启动器生效

4.整合javaEe，解决方案和自动配置的东西都在spring-boot-autoconfigur-2.2.Release.jar包下

5.它会把所有需要导入的组件以类名的方式返回，这些组件就会被添加到容器

6.容器中也会存在许多XXautoconfiguration的文件，就是这些类给容器中导入这些场景所需要的组件并自动配置，@Configuration，javaConfig

7.有了自动配置类，免去了手动配置

![装配原理1](https://gitee.com/shaobing2021/typora/raw/master/img/20200728100849.png)

个人理解：pom.xml会导入依赖，springboot会加载自动配置类，当满足条件时候，例如xml有该配置start，就会导入。其次，自动配置类会对应properites bean，可以从yaml文件进行导入。

## 解析自动配置类

### HttpEncodingAutoConfiguration

> 解析spring.factories中xxxautoConfiguration

```java
//表示这是一个配置类
@Configuration(proxyBeanMethods = false)
//自动配置属性：httpProperties，可以通过yml文件进行修改
@EnableConfigurationProperties(ServerProperties.class)
//spring底层注解，根据不同条件判断当前配置或者类是否生效
@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)
@ConditionalOnClass(CharacterEncodingFilter.class)
@ConditionalOnProperty(prefix = "server.servlet.encoding", value = "enabled", matchIfMissing = true)
public class HttpEncodingAutoConfiguration {
```

## 精髓

1、SpringBoot启动会加载大量的自动配置类

2、我们看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中；

3、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件存在在其中，我们就不需要再手动配置了）

4、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可；

**xxxxAutoConfigurartion：自动配置类；**给容器中添加组件

**xxxxProperties:封装配置文件中相关属性；**

## 自动配置生效类

**通过启用 debug=true属性；来让控制台打印自动配置报告，这样我们就可以很方便的知道哪些自动配置类生效；**

```java
#开启springboot的调试类debug=true
```

**Positive matches:（自动配置类启用的：正匹配）**

**Negative matches:（没有启动，没有匹配成功的自动配置类：负匹配）**

**Unconditional classes: （没有条件的类）**

==要想配置类生效必须导入对应的start==