# Spring Security

是一种基于 Spring AOP 和 Servlet 规范中的 Filter 实现的安全框架。

Spring Security 的模块

|模  块|描  述|
|ACL|支持通过访问控制列表(access control list，ACL)为域对象提供安全性|
|切面(Aspects)|一个很小的模块，当使用Spring Security注解时，会使用基于AspectJ的切面，而不是使用标准的Spring AOP|
|CAS客户端(CAS Client)|提供与Jasig的中心认证服务(Central Authentication Service，CAS)进行集成的功能|
|配置(Configuration)|包含通过XML和Java配置Spring Security的功能支持|
|核心(Core)|提供Spring Security基本库|
|加密(Cryptography)|提供了加密和密码编码的功能|
|LDAP|支持基于LDAP进行认证|
|OpenID|支持使用OpenID进行集中式认证|
|Remoting|提供了对Spring Remoting的支持|
|标签库(Tag Library)|Spring Security的JSP标签库|
|Web|提供了Spring Security基于Filter的Web安全性支持|

应用程序的类路径下至少要包含Core和Configuration这两个模块

过滤Web请求

```xml
<filter>
    <!-- 被委托的Filter -->
    <filter-name>springSecurityFilterChain</filter-name>
    <!-- 将工作委托给别的Filter的Filter -->
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
</filter>
```

JavaConfig

```java
public class SecurityWebInitializer extends AbstractSecurityWebApplicationInitializer {}
```

会拦截发往应用中的请求，并将请求委托给ID为 springSecurityFilterChain 的bean。


简单的安全性配置

JavaConfig

```java
@Configuration
@EnableWebSecurity // 启动 Web 安全性
// @EnableWebMvcSecurity 为Spring MVC启用Web安全性。配置了一个Spring MVC参数解析解析器
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    // 通过重载，配置Spring Security的Filter链
    configure(WebSecurity) 
    // 通过重载，配置如何通过拦截器保护请求
    protected void configure(HttpSecurity) throws Exception {}
    // 通过重载，配置user-detail服务
    configure(AuthenticationManagerBuilder) 
}
```

## 用户存储

在内存中维护用户存储。用于开发和测试

JavaConfig

```java
@Configuration
@EnableWebMvcSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication() // 启动内存用户存储
        .withUser("user").password("password").roles("USER").and()
        .withUser("admin").password("password").roles("USER", "ADMIN");
    }
}
```

配置用户详细信息的方法

|方  法|描  述|
|---|---|
|accountExpired(boolean)|定义账号是否已经过期|
|accountLocked(boolean)|定义账号是否已经锁定|
|and()|用来连接配置|
|authorities(GrantedAuthority...)|授予某个用户一项或多项权限|
|authorities(List<? extends GrantedAuthority>)|授予某个用户一项或多项权限|
|authorities(String...)|授予某个用户一项或多项权限|
|credentialsExpired(boolean)|定义凭证是否已经过期|
|disabled(boolean)|定义账号是否已被禁用|
|password(String)|定义用户的密码|
|roles(String...)|授予某个用户一项或多项角色|

基于数据库表进行认证

```java
@Autowired
DataSource dataSource;

@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.jdbcAuthentication()
        .dataSource(dataSource)
        .usersByUsernameQuery( // 所有查询都将用户名作为唯一的参数
            "select username, password, enabled from Users " +
            "where username=?")
        .authoritiesByUsernameQuery(
            "select username, authority from UserAuthorities " +
            "where username=?")
        .passwordEncoder(new StandardPasswordEncoder("53cr3t")); // 密码转码器
}

// 基于LDAP进行认证
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.ldapAuthentication()
        .userSearchFilter("(uid={0})")
        .groupSearchFilter("member={0}");
    // 指定查询基础
    auth.ldapAuthentication()
        .userSearchBase("ou=people")
        .userSearchFilter("(uid={0})")
        .groupSearchBase("ou=groups")
        .groupSearchFilter("member={0}");
}


// 默认的查询语句
public static final String DEF_USERS_BY_USERNAME_QUERY =
        "select username,password,enabled " +
        "from users " +
        "where username = ?";
public static final String DEF_AUTHORITIES_BY_USERNAME_QUERY =
        "select username,authority " +
        "from authorities " +
        "where username = ?";
public static final String DEF_GROUP_AUTHORITIES_BY_USERNAME_QUERY =
        "select g.id, g.group_name, ga.authority " +
        "from groups g, group_members gm, group_authorities ga " +
        "where gm.username = ? " +
        "and g.id = ga.group_id " +
        "and g.id = gm.group_id";
// PasswordEncoder
public interface PasswordEncoder {
    String encode(CharSequence rawPassword);
    boolean matches(CharSequence rawPassword, String encodedPassword);
}
```

略，暂时无法理解

### 拦截请求

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.formLogin().and() // 启用默认的登录页
        .authorizeRequest()
            .antMatchers("/some/**").authenticated()
            .antMatchers(HttpMethod.POST, "/somepath").authenticated()
            .anyRequest().permitAll();
        .and()
        .requiresChannel() // 为选定的URL强制使用HTTPS
            .antMatchers("/path").requiresSecure();
            .antMatchers("/httppath").requiresInsecure(); // 声明为始终通过HTTP传送
        .csdf().disable(); // 禁用csrf防护功能
}
```

防止跨站请求伪造

```jsp
<input type="hidden" name="${_csrf.parameterName}" value="${_csrf.token}" />
```





入门程序

引入spring security相关的依赖

```xml
spring-security-web
spring-security-config
```

配置上下文

```xml

```

加密算法

BCrypt 加密算法

```java
BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
String password = passwordEncoder.encode(myPassword); // 加密
```

## 保护方法应用

使用注解

Spring Security提供了三种不同的安全注解:

- Spring Security自带的@Secured注解;
- JSR-250的@RolesAllowed注解;
- 表达式驱动的注解，包括@PreAuthorize、@PostAuthorize、@PreFilter和@PostFilter。


```java
@Configuration
@EnableGlobalMethodSecurity(securedEnabled=true) // 启用基于注解的方法安全性，会创建一个切点，包括带有@Secured注解的方法。
public class MethodSecurityConfig extends GlobalMethodSecurityConfiguration {
    // 设置认证
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
            .withUser("user").password("password").roles("USER");
    }
}

@Secured("ROLE_ADMIN") // 只有具有 ROLE_ADMIN 权限的认证用户，才能调用该方法
public void someMethod(){}

// 使用 @RolesAllowed 注解(与 Secured 非常类似)
@Configuration
@EnableGlobalMethodSecurity(jsr250Enabled=true)
...
```

Spring Security 3.0提供了4个新的注解，可以使用SpEL表达式来保护方法调用
   
|注  解|描  述|
|---|---|
|@PreAuthorize|在方法调用之前，基于表达式的计算结果来限制对方法的访问|
|@PostAuthorize|允许方法调用，但是如果表达式计算结果为false，将抛出一个安全性异常|
|@PostFilter|允许方法调用，但必须按照表达式来过滤方法的结果|
|@PreFilter|允许方法调用，但必须在进入方法之前过滤输入值|

```java
// 配置
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled=true)
...

// 使用
@PreAuthorize("hasRole('ROLE_ADMIN')") // 限制只有具有某权限的用户才能访问
...

@PreAuthorize("(hasRole('ROLE_ADMIN') and #spittle.text.length() <= 140)") // 有某权限且spittle对象的text属性的长度小于等于140
public void someMethod(Spittle spittle) {...}

@PostAuthorize("returnObject.spitter.username == principal.username") // returnObject 代表返回对象
public Spittle getSpittleById(long id) {...}

// 事后对方法的返回值进行过滤
@PostFilter("hasRole('ROLE_ADMIN') || filterObject.spitter.username == principal.name")
public List<Spittle> getOffensiveSpittles(){...}
// 事先对方法的参数进行过滤
@PreFilter("hasRole('ROLE_ADMIN') || filterObject.spitter.username == principal.name")
public void deleteSpittles(List<Spittle> spittles){...}
```

principal是另一个Spring Security内置的特殊 名称，它代表了当前认证用户的主要信息(通常是用户名)。

@PostFilter会使用这个表达式计算该方法所返回集合的每个成员，将计算结果为false的成员移除掉。

定义许可计算器



