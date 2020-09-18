Shiro整合Thymeleaf

1.引入依赖

```pom
　<!--thymeleaf中使用shiro-->
        <dependency>
            <groupId>com.github.theborakompanioni</groupId>
            <artifactId>thymeleaf-extras-shiro</artifactId>
            <version>2.0.0</version>
        </dependency>
```

2.ShiroConfig中编写shiroDialect

```java
@Bean(name = "shiroDialect")
    public ShiroDialect shiroDialect(){
        return new ShiroDialect();
    }
```

3.html页面添加xmlns

```html
<html lang="zh_CN" xmlns:th="http://www.thymeleaf.org"
      xmlns:shiro="http://www.pollix.at/thymeleaf/shiro">
```

4.在标签中使用shiro标签

```html
<div shiro:hasPermission="add">
    <a href="/1">1111</a>
</div>

<div shiro:hasPermission="update">
    <a href="/2">2222</a>
</div>

<div shiro:hasPermission="all">
    <a href="/1">1111</a>
    <a href="/2">2222</a>
</div>
```





Shiro注解使用

ShiroConfig注入Bean

```java
@Bean
    public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor(SecurityManager securityManager) {
        AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor = new AuthorizationAttributeSourceAdvisor();
        authorizationAttributeSourceAdvisor.setSecurityManager(securityManager);
        return authorizationAttributeSourceAdvisor;
    }

    @Bean
    public DefaultAdvisorAutoProxyCreator getDefaultAdvisorAutoProxyCreator() {
        DefaultAdvisorAutoProxyCreator advisorAutoProxyCreator = new DefaultAdvisorAutoProxyCreator();
        advisorAutoProxyCreator.setProxyTargetClass(true);
        return advisorAutoProxyCreator;
    }
```

@RequiresAuthentication

验证用户是否登录，等同于方法subject.isAuthenticated() 结果为true时。

@RequiresUser

验证用户是否被记忆，user有两种含义：

一种是成功登录的（subject.isAuthenticated() 结果为true）；

另外一种是被记忆的（subject.isRemembered()结果为true）。

@RequiresGuest

验证是否是一个guest的请求，与@RequiresUser完全相反。

 换言之，RequiresUser  == !RequiresGuest。

此时subject.getPrincipal() 结果为null.

@RequiresRoles

例如：@RequiresRoles("aRoleName");

  void someMethod();

如果subject中有aRoleName角色才可以访问方法someMethod。如果没有这个权限则会抛出异常[AuthorizationException](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/authz/AuthorizationException.html)。

@RequiresPermissions

例如： @RequiresPermissions({"file:read", "write:aFile.txt"} )
  void someMethod();

要求subject中必须同时含有file:read和write:aFile.txt的权限才能执行方法someMethod()。否则抛出异常[AuthorizationException](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/authz/AuthorizationException.html)。