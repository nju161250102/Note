# SpringSecurity

## 5.Java配置
Spring框架自3.1起普遍提供了对Java配置的支持。从Spring Security 3.2起用户可以使用Spring Security的Java配置而不需要任何XML文件。  

### 5.1 Hello Web Security Java Configuration
Java配置类创建了一个叫springSecurityFilterChain的Servlet过滤器，负责保护web应用的安全，包括保护应用的URL、验证用户名和密码以及重定向到登录表单。下面是一个最基础的配置类示例：  
```
@EnableWebSecurity
public class WebSecurityConfig implements WebMvcConfigurer {

	@Bean
	public UserDetailsService userDetailsService() throws Exception {
		InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();
		manager.createUser(User.withDefaultPasswordEncoder().username("user").password("password").roles("USER").build());
		return manager;
	}
}
```

### 5.2 HttpSecurity
WebSecurityConfig仅仅包含了如何鉴别用户权限的信息。在WebSecurityConfigurerAdapter的configure(HttpSecurity http)方法中提供了一个默认安全配置。  
```
protected void configure(HttpSecurity http) throws Exception {
	http
		.authorizeRequests()
			.anyRequest().authenticated()
			.and()
		.formLogin()
			.and()
		.httpBasic();
}
```
上面的默认配置包括：  
    * 确保任何对应用的访问都需要身份认证
    * 允许用户使用基于表单的登录进行身份验证
    * 允许用户使用HTTP基本身份验证进行身份验证  

### 5.3 Java配置和表单登录[.formLogin()部分]
向Spring Security提供自己的表单登录界面：  
```
.formLogin()
    .loginPage("/login") 
    .permitAll();        
```
loginPage()指明了登录网页的地址，permitAll()使得所有用户均有权限访问登录页面。  
登录表单的用户名和密码必须作为名叫username和password的Http参数发送。  

### 5.4 授权请求[.authorizeRequests()部分]
我们可以通过向http.authorizeRequests()方法添加多个子项来指定URL的自定义请求。  
```
.authorizeRequests()
    .antMatchers("/resources/**", "/signup", "/about").permitAll()
    .antMatchers("/admin/**").hasRole("ADMIN")
    .antMatchers("/db/**").access("hasRole('ADMIN') and hasRole('DBA')")
    .anyRequest().authenticated()
    .and()
```
* 按顺序逐个匹配URL路径
* 使用通配符来匹配多个URL
* hasRole的参数不需要ROLE_前缀
* 没有合适匹配结果的URL匹配至anyRequest()

### 5.5 处理登出[.logout()部分]
```
.logout()
    .logoutUrl("/my/logout")
    .logoutSuccessUrl("/my/index")
    .logoutSuccessHandler(logoutSuccessHandler)
    .invalidateHttpSession(true)
    .addLogoutHandler(logoutHandler)
    .deleteCookies(cookieNamesToClear)
    .and()
```
* logoutUrl指定登出发生的路径（必须使用POST方法），logoutSuccessUrl()指定登出成功后跳转的地址。

### 5.8 Authentication
#### 5.8.2 JDBC Authentication
```
.jdbcAuthentication()
    .dataSource(dataSource)
    .withDefaultSchema()
    .withUser(users.username("user").password("password").roles("USER"))
    .withUser(users.username("admin").password("password").roles("USER","ADMIN"));
```

### 5.10 方法安全
在任意@Configuration注解上加上@EnableGlobalMethodSecurity注解，之后每个方法上均可使用@Secured。  

