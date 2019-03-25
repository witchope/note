# Spring Security 概念浅析

- [Spring Security 概念浅析](#spring-security-概念浅析)
  - [Authentication (鉴权) and Authorization (授权)](#authentication-鉴权-and-authorization-授权)
    - [Authentication](#authentication)
    - [Authorization](#authorization)
  - [WebSecurity](#websecurity)
  - [参考](#参考)

## Authentication (鉴权) and Authorization (授权)

鉴权主要解决的问题是：你是谁 （who are you?）, 而授权则是：你可以干什么？（what are you allowed to do?）。这是两个在 Spring Security 很重要的概念。务必要区分。

### Authentication

在 Spring Security 中承担鉴权责任的接口是 `AuthenticationManager` ，它只有一个方法：

```java
public interface AuthenticationManager {

  Authentication authenticate(Authentication authentication)
    throws AuthenticationException;

}
```

一般在 `AuthenticationManager` 的 `authenticate()` 中存在以下三种情况：

1. 如果鉴权通过，返回一个 `Authentication`
2. 如果鉴权失败，抛出一个 `AuthenticationException`
3. 如果不能确定，则返回 `null`

最常用的 `AuthenticationManager` 实现是 `ProviderManager`，其中包含了一组 `AuthenticationProvider` 实例；

`AuthenticationProvider` 和 `AuthenticationManager` 有点类似，但是前者又有额外的方法可以判断是否支持特定的 `Authentication` :

```java
public interface AuthenticationProvider {

    Authentication authenticate(Authentication authentication)
            throws AuthenticationException;

    boolean supports(Class<?> authentication);

}
```

很多时候一个应用多个逻辑分组的资源需要鉴权，而每个分组都有自己的专有的 `AuthenticationManager`。一般是 `ProviderManager` ，通常所有 `ProviderManager` 会共享一个父类，而这个父类 `provider` 扮演一个后备资源的角色。

![authentication](../assets/authentication.png)

通常我们只需要配置局域的 `AuthenticationManager`，无需过多关心全局的，除非需要共享数据：

```java
@Configuration
public class ApplicationSecurity extends WebSecurityConfigurerAdapter {

  @Autowired
  DataSource dataSource;

   ... // web stuff here

  @Override
  public configure(AuthenticationManagerBuilder builder) {
    builder.jdbcAuthentication().dataSource(dataSource).withUser("dave")
      .password("secret").roles("USER");
  }

}
```

### Authorization

一旦鉴权成功，我们就可以进入授权的步骤了，主要实现类是 `AccessDecisionManager` , 类中同样拥有一组 `AccessDecisionVoter` , 它们之间的关系有点类似于 `ProviderManager` 之于 `AuthenticationProviders 。

而 `AccessDecisionVoter` 可以当作是一个被 `ConfigAttributes` 修饰的 `Authentication` ：

```java
boolean supports(ConfigAttribute attribute);

boolean supports(Class<?> clazz);

int vote(Authentication authentication, S object,
        Collection<ConfigAttribute> attributes);
```

## WebSecurity

Spring Security 很大一部分功能通过一系列的 Servlet `Filters` 链来实现的。

下面就是常见的 Filter （正序）：

- `ChannelProcessingFilter` ：实现重定向到不同的协议
- `SecurityContextPersistenceFilter` ：负责在 `SecurityContextHolder` 中生成 `SecurityContext`
- `ConcurrentSessionFilter` : 负责更新 SessionRegistry
- `UsernamePasswordAuthenticationFilter` : 负责鉴权处理机制，往 `SecurityContextHolder` 写入有效的 `Authtication`
- `RememberMeAuthenticationFilter` : 负责开启 remember-me 服务
- `AnonymousAuthenticationFilter` ： 负责往 `SecurityContextHolder` 写入匿名 `Authtication`
- `ExceptionTranslationFilter` : 捕捉 Spring Security Exception 以返回合适的 HTTP 错误码或者执行 `AuthenticationEntryPoint`
- `FilterSecurityInterceptor` : 为了保护 web URIs 并抛出异常

## 参考

- [Spring Security Architecture](https://spring.io/guides/topicals/spring-security-architecture)
- [How Spring Security Filter Chain works](https://stackoverflow.com/questions/41480102/how-spring-security-filter-chain-works)