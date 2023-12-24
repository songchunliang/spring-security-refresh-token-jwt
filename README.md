# 自动创建table的解释
这个项目使用了 Spring Boot 和 Hibernate 这两个框架，它们可以自动创建数据库表。

在 `application.properties` 文件中，有一个属性叫做 `spring.jpa.hibernate.ddl-auto`，这个属性用来控制 Hibernate 的数据库表生成策略。如果这个属性的值被设置为 `create` 或者 `create-drop`，那么 Hibernate 会在应用启动的时候自动创建数据库表。

具体来说，Hibernate 会根据你的实体类（即使用 `@Entity` 注解的类）来生成对应的数据库表。例如，如果你有一个 `User` 实体类，那么 Hibernate 会创建一个 `user` 表。如果 `User` 实体类有一个 `roles` 属性，并且这个属性被 `@ManyToMany` 注解标记，那么 Hibernate 还会创建一个 `user_roles` 表来存储用户和角色之间的关系。
因此，这个项目在启动的时候会自动创建 `roles` 和 `user_roles` 这些表，是因为它的实体类和 Hibernate 的配置导致的。


在 `User` 实体类中，有一个 `roles` 属性被标记为 `@ManyToMany` 关系。这表示一个用户可以有多个角色，一个角色也可以被多个用户拥有。在关系型数据库中，这种多对多的关系通常通过一个中间表来实现，这个中间表存储了主表的记录之间的关联关系。
在这个项目中，`User` 和 `Role` 之间的多对多关系就是通过 `user_roles` 这个中间表来实现的。当你启动应用时，Hibernate 会自动创建这个 `user_roles` 表。

具体来说，`User` 实体类中的 `roles` 属性的定义可能类似于下面这样：

```java
@ManyToMany
@JoinTable(
  name = "user_roles", 
  joinColumns = @JoinColumn(name = "user_id"), 
  inverseJoinColumns = @JoinColumn(name = "role_id"))
private Set<Role> roles;
```

在这个定义中，`@JoinTable` 注解用来指定中间表的名称和结构。`name` 属性指定了中间表的名称，`joinColumns` 和 `inverseJoinColumns` 属性分别指定了中间表中的外键。

因此，当 Hibernate 创建 `user` 和 `role` 表之后，它会继续创建 `user_roles` 表，并根据 `User` 实体类中的 `roles` 属性的定义来设置 `user_roles` 表的结构。

# 关于这个类的解释： [AuthEntryPointJwt.java](src%2Fmain%2Fjava%2Fcom%2Fbezkoder%2Fspring%2Fsecurity%2Fjwt%2Fsecurity%2Fjwt%2FAuthEntryPointJwt.java)

`AuthenticationEntryPoint` 是 Spring Security 中的一个接口，用于处理认证过程中出现的异常，特别是在用户请求需要认证而用户未提供有效凭证时的情况。它定义了一种方式来处理身份验证失败的场景，例如，用户试图访问需要身份验证的资源但未提供有效的身份验证凭证。

主要的方法是 `commence`，该方法在认证失败时被调用。开发者可以通过实现这个接口并重写 `commence` 方法来定义如何处理认证失败的情况。

```java
public interface AuthenticationEntryPoint {
    void commence(HttpServletRequest request, HttpServletResponse response,
                  AuthenticationException authException) throws IOException, ServletException;
}
```

其中：
- `request` 表示发起请求的 HttpServletRequest。
- `response` 表示要发送到客户端的 HttpServletResponse。
- `authException` 表示导致认证失败的异常，例如缺少凭证或无效凭证。

`commence` 方法的实现可以执行一些操作，比如：
- 返回一个特定的 HTTP 状态码，指示认证失败。
- 重定向用户到登录页面。
- 返回自定义的错误消息或响应。

这个接口的实现通常用于定制认证失败时的行为，以满足应用程序的需求。在 Spring Security 的配置中，你可以通过 `http.exceptionHandling().authenticationEntryPoint(...)` 来指定特定的 `AuthenticationEntryPoint` 实现。

一个简单的示例是使用默认的 `Http403ForbiddenEntryPoint`，该实现在认证失败时返回 HTTP 403 Forbidden 状态码：

```java
http.exceptionHandling()
    .authenticationEntryPoint(new Http403ForbiddenEntryPoint());
```

当然，你也可以自定义实现 `AuthenticationEntryPoint` 接口，以满足你应用程序的特定需求。
