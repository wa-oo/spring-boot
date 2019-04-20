# Spring Boot入门 

## 1、Spring Boot简介

> 是来简化Spring项目应用开发，约定大于配置，去繁从简，just run就能创建一个独立的，产品级的应用。 
>
> 整个Spring技术栈的大整合。 
>
> J2EE开发的一站式解决方案。 

### 优点：

* 快速创建独立运行的Spring项目以及与主流框架集成。 

* 使用嵌入式的servlet容器，应用无需打成war包。 

* starters(启动器)自动依赖和版本控制。 

* 大量的自动配置，简化开发，也可修改默认值。 

* 无需配置xml，无代码生成，开箱即用。 

* 准生产环境的运行时应用监控。 

* 与云计算的天然即成 

## 2、微服务 

2014 Martin fowler 

微服务：架构风格 

一个应用应该是一组小型服务，可以通过http的方式进行互通。 

单体应用：All In One； 

每一个功能元素最终都是一个可独立替换和独立升级的软件单元。 

[微服务文档](https://martinfowler.com/microservices/) 

## 3、Spring Boot Hello World 

功能需求： 

浏览器发送hello请求，服务器接受请求并处理，响应hello world字符串。 

### 1、创建maven工程 

### 2、导入Spring Boot相关的依赖 

```xml 
<parent> 

<groupId>org.springframework.boot</groupId> 

<artifactId>spring-boot-starter-parent</artifactId> 

<version>2.1.3.RELEASE</version> 

</parent> 

<dependencies> 

<dependency> 

<groupId>org.springframework.boot</groupId> 

<artifactId>spring-boot-starter-web</artifactId> 

</dependency> 

</dependencies> 
```

### 3、编写一个主程序：启动Spring Boot应用 

```java 
/** 

 * @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用 

*/ 

@SpringBootApplication 

public class HelloWorldMainApplication { 

public static void main(String[] args) { 

// Spring应用启动起来 

SpringApplication.run(HelloWorldMainApplication.class, args); 

} 

} 
```

### 4、编写相关的controller、service 

```java 
@Controller 

public class HelloController { 

    @ResponseBody 

    @RequestMapping("/hello") 

    public String hello(){ 

    return "Hello World!"; 

    } 

} 
```

### 5、简化部署工作 

```xml 
<!--这个插件可以将应用打包成一个可执行的jar包--> 

<build> 

<plugins> 

<plugin> 

<groupId>org.springframework.boot</groupId> 

<artifactId>spring-boot-maven-plugin</artifactId> 

</plugin> 

</plugins> 

</build> 
```

将这个应用打成jar包，使用java -jar的命令进行执行 

## 4、Hello World探究 

### 1、pom文件 

* 父项目 

```xml 
<parent> 

<groupId>org.springframework.boot</groupId> 

<artifactId>spring-boot-starter-parent</artifactId> 

<version>2.1.3.RELEASE</version> 

</parent> 

他的父项目 

<parent> 

<groupId>org.springframework.boot</groupId> 

<artifactId>spring-boot-dependencies</artifactId> 

<version>2.1.3.RELEASE</version> 

<relativePath>../../spring-boot-dependencies</relativePath> 

</parent> 

他来管理Spring Boot应用里的所有依赖版本
```

Spring Boot版本仲裁中心。 

以后导入依赖默认是不需要写版本的。（没有在dependencies里面管理的依赖自然需要声明版本号） 

* 导入的依赖 

```xml 
<dependency> 

<groupId>org.springframework.boot</groupId> 

<artifactId>spring-boot-starter-web</artifactId> 

</dependency> 
```

spring-boot-starter：spring-boot场景启动器，帮我门导入了web模块正常运行所依赖的组件。 

Spring Boot将所有的功能场景都抽取出来，做成一个个都starters（启动器），只需要在项目里面引入这些starter相关场景的所有依赖都会导入进来，要用什么功能就导什么场景的启动器。 

### 2、主程序类，主入口类 

```java 
/** 

 * @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用 

 */ 

@SpringBootApplication 

public class HelloWorldMainApplication { 

    public static void main(String[] args) { 

        // Spring应用启动起来 

        SpringApplication.run(HelloWorldMainApplication.class, args); 

    } 

} 
```

@**SpringBootApplication**：Spring Boot应用，标注在某个类上，说明这个类是SpringBoot的主配置类，Spring Boot就应该运行这个类的main方法来启动Spring Boot应用。 

```java 
@Target(ElementType.TYPE) 

@Retention(RetentionPolicy.RUNTIME) 

@Documented 

@Inherited 

@SpringBootConfiguration 

@EnableAutoConfiguration 

@ComponentScan(excludeFilters = { 

@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class), 

@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) }) 

public @interface SpringBootApplication {} 
```

​		@**SpringBootConfiguration**：Spring Boot的配置类 

​				标注在某个类上，表示这是一个Spring Boot的配置类； 

​				@**Configuration**：配置类上标注这个注解； 

​								配置类---配置文件； 

​								配置类也是容器中的一个组件；@Component 

​		@**EnableAutoConfiguration**：开启自动配置功能 

​				以前我们需要配置的东西，Spring Boot帮我们自动配置，@**EnableAutoConfiguration**告诉Spring Boot开启自动配置功能，这样自动配置才能生效。

```java
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```

​			@**AutoConfigurationPackage**：自动配置包 

​					@Import(AutoConfigurationPackages.Registrar.class)：

​					Spring的底层注解@Import，给容器中导入一个组件，导入的组件由AutoConfigurationPackages.Registrar.class决定。

==将主配置类（@SpringbootApplication标注的类）的所在包及下面所有子包里面的所有组件扫描到Spring容器中。==

​			@**Import**(AutoConfigurationImportSelector.class)

​					给容器导入组件

​					AutoConfigurationImportSelector.class：导入哪些组件的选择器；

​					将所有需要导入的组件以全类名的方式返回，这些组件就会被添加到容器中。

​					会给容器中导入非常多的自动配置类（xxxAutoConfiguration），就是给容器中导入这个场景需要的所有组件，并配置好这些组件。

有了自动配置类，免去了我们手动编写配置注入功能组件等工作。

==Spring Boot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration中指定的值，将这些值做为自动配置类导入到容器中，自动配置类就生效，帮我们进行自动配置工作。==以前我们需要配置的东西，自动配置类都帮我们做了。

J2EE的整体整合解决方案和自动配置都在spring-boot-autoconfigure-2.1.3.RELEASE.jar。

##5、使用Spring Initializer快速创建Spring Boot项目

默认生成的Spring Boot项目：

- 主程序已经生成好了，我们只需要我们自己的逻辑。
- resources文件夹中的目录结构：
	- static：保存所有的静态资源（js，css，images）
	- templates：保存所有的模版页面（Spring Boot默认jar包使用嵌入式的tomcat，默认不支持jsp页面），可以使用模版引擎（freemarker，thymeleaf）
	- application.properties：Spring Boot应用的配置文件，可以修改一些默认设置