# Spring Boot **2.3.3**

## 一、Getting Started

### 1.Creating the POM

idea创建一个Maven项目，在POM文件中指定其为父项目。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>cn.eli486</groupId>
    <artifactId>springboot_learn</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <!--
	spring-boot-starter-parent作为父类场景标识，其依赖于
		 <parent>
   			 <groupId>org.springframework.boot</groupId>
   			 <artifactId>spring-boot-dependencies</artifactId>
    		 <version>2.3.3.RELEASE</version>
  		</parent>
	spring-boot-dependencies作为根节点，它配置管理了绝大多数组件的依赖版本
		<properties>
    		<activemq.version>5.15.13</activemq.version>
    		<antlr2.version>2.7.7</antlr2.version>
    		<appengine-sdk.version>1.9.81</appengine-sdk.version>
    		<artemis.version>2.12.0</artemis.version>
    		<aspectj.version>1.9.6</aspectj.version>
			。。。
		</properties>
		这也是我们在引用这些组件时可以不配置版本的原因，springboot帮我们设定了默认版本，除非我们用特定		 的版本覆盖默认的。	
	-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.3.RELEASE</version>
    </parent>
   
    
    
    <!--maven插件，无需导出war包，导出jar包也可执行程序-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

### 2.Adding Classpath Dependencies

```xml
 <!--引入所需场景依赖，这里我们引入web模块来更直观展现效果-->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

### 3.Writing the Code

```java
import org.springframework.boot.*;
import org.springframework.boot.autoconfigure.*;
import org.springframework.web.bind.annotation.*;

@RestController
@EnableAutoConfiguration
public class Example {

    @RequestMapping("/")
    String home() {
        return "Hello World!";
    }

    public static void main(String[] args) {
        SpringApplication.run(Example.class, args);
    }

}
```

### 4.Running the Example

在浏览器中打开 `localhost:8080`, 就会看到

```html
Hello World!
```

### 5. Creating an Executable Jar

从命令行运行`mvn package`

如果查看`target`目录，则应该看到`myproject-1.0-SNAPSHOT.jar`。该文件的大小应为10 MB左右。如果您想窥视内部，可以使用`jar tvf`，如下所示：

```cmd
$ jar tvf target / myproject-1.0-SNAPSHOT.jar
```

您还应该`myproject-0.0.1-SNAPSHOT.jar.original`在`target`目录中看到一个更小的文件。这是Maven在Spring Boot重新打包之前创建的原始jar文件。

要运行该应用程序，请使用以下`java -jar`命令：

```cmd
$ java -jar target / myproject-1.0-SNAPSHOT.jar 
```

## 二、Using Spring Boot

### 1.Starters

springboot提供了一系列的starters，即场景适配器，其中包含了当前场景所需的很多依赖，例如要开发web模块，我们要引入spring-boot-starter-web。

除了springboot官方提供的starters，也支持第三方或自定义starter，但在命名上要有规范。官方的命名以spring-boot-starter-*为前缀，而第三方或自定义starter以项目名称为前缀，如myproject-spring-boot-starter

### 2. Structuring Your Code

springboot不需要任何特定的代码布局就可以工作。

#### 2.1. Using the “default” Package

当一个class文件没有**package**包声明，那它默认放在**default package**包下,通常这种创建是应该避免的，在一些特定情况下会引发问题，如`@ComponentScan`, `@ConfigurationPropertiesScan`, `@EntityScan`等注解的使用。

通常推荐的创建包的方式是用反域名的命名方式，如com.example.projects

#### 2.2. Locating the Main Application Class

作为主程序入口的类，通常用注解**@SpringBootApplication**标识，且这个类存放在root包之下其他class之上。

通常的目录结构例如：

```java
com
 +- example
     +- myapplication
         +- Application.java
         |
         +- customer
         |   +- Customer.java
         |   +- CustomerController.java
         |   +- CustomerService.java
         |   +- CustomerRepository.java
         |
         +- order
             +- Order.java
             +- OrderController.java
             +- OrderService.java
             +- OrderRepository.java
```

其中Application.java作为主程序启动类，标识@SpringBootApplication并声明启动main方法,

例如：

```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

### 3. Configuration Classes

springboot支持基于Java的配置。虽然也可以和XML一起使用，但通常建议是采用注解@Configuration标识的类来实现配置。

通常的做法：

1.创建实体类，绑定配置文件属性，指定前缀

```java
@Data
@ConfigurationProperties(prefix = "eli")
@Configuration
public class User {
    private String username;
    private String password;
}

```

2.配置文件application.yml

```yml
eli:
  username: eli
  password: 123
```

3.controller层注入实体

```java
@RestController
public class HelloController {

    @Autowired
    User user;

    @RequestMapping("/user")
    public String  getUser(){
        return String.format ("Hello %s", user.getUsername ());
    }
}
```

4.显示结果

```html
Hello eli
```



#### 3.1. Importing Additional Configuration Classes

你不需要将所有的配置都写在一个@Configuration标识的类中， 可以用`@Import` 注解去导入额外的配置类。另外，可以用`@ComponentScan `去自动扫描所有的spring组件，包括@Configuration配置类。

`@import`：

1.实体类

```java
@Data
public class User {
    @Value ("eli.username")
    private String username;
    private String password;
}

```

2.Controller

```java
@RestController
@Import (User.class)
public class HelloController {

    @Autowired
    User user;
    
    @RequestMapping("/user")
    public String  getUser(){
        return String.format ("Hello %s", user.getUsername ());
    }
}
```



#### 3.2. Importing XML Configuration

如果一定要基于XML来配置，仍然建议先配置一个@Configuration配置类，然后用@ImportResource注解去导入XML配置文件。

看看专业的讲解：

[深入理解spring注解之@ComponentScan注解](https://www.cnblogs.com/jpfss/p/11171655.html)👍

[Spring如何引入其他资源：和@Import @ImportResource](https://blog.csdn.net/qq_26323323/article/details/89789962)🙃



1. 配置类

   ```java
   @Configuration
   @ImportResource (locations={"classpath:applicationContext.xml"})
   public class MyConfig {
   
   }
   ```

   2.xml文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd">
   
       <bean id="user" class="cn.eli486.pojo.User" name="user"/>
   </beans>
   ```

   3.controller

   ```java
   @RestController
   public class HelloController {
   
       @Autowired
       User user;
       
       @RequestMapping("/user")
       public String  getUser(){
           return String.format ("Hello %s", user.getUsername ());
       }
   }
   ```

### 4. Auto-configuration

springboot的自动配置会尝试将你所引入的jar包依赖自动配置到你的Spring应用中，你只需要在你的一个配置类上标识`@EnableAutoConfiguration` 或者 `@SpringBootApplication`即可。

当然你也可以在@SpringBootApplication的属性exclude中排除你不想要自动配置的类,更多的详细内容可以在debug模式中观察。

### 5. Spring Beans and Dependency Injection

通常，我们是通过`@ComponentScan` 发现Beans并用`@Autowired`去注入。如果这个Bean有构造函数，你也可以省去用@Autowired，注解构造函数注入  

```java
@Service
public class DatabaseAccountService implements AccountService {

    private final RiskAssessor riskAssessor;

    public DatabaseAccountService(RiskAssessor riskAssessor) {
        this.riskAssessor = riskAssessor;
    }

    // ...

}
```

### 6. Using the @SpringBootApplication Annotation

`@SpringBootApplication `涵盖了自动配置、组件扫描、额外添加配置类这三个功能，是因为它包含了三个注解：

`@EnableAutoConfiguration`:启动Springboot的自动配置机制

`@ComponentScan`:扫描应用所在包下的所有@Component组件

`@Configuration`:允许在上下文中注册额外的beans或导入另外的配置类

### 7. Running Your Application

运行你的应用程序可以有多种方式：

#### 1.Running from an IDE

在你的IDE工具中启动

#### 2.Running as a Packaged Application

打包成一个jar包文件，用命令去执行,可以携带参数

```cmd
$ java -jar target / myapplication-0.0.1-SNAPSHOT.jar
```

3. #### Using the Maven Plugin

   使用Maven插件命令去执行，可以携带参数

   ```cmd
   $ mvn spring-boot:run
   ```

### 8. Developer Tools

springboot提供了热部署工具，可以实现代码的快速重建。

Maven:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

## 三、Spring Boot Features

