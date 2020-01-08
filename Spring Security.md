# Spring Security

入门程序

引入spring security相关的依赖

```java
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

