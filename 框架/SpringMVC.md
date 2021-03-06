[TOC]
# 1.配置文件配置
## 1.1 配置主配置文件路径和名字
1. 在`web.xml`下配置`SpringMVC`的`servlet-class`和`url-pattern`
2. 配置文件默认建立在`WEB-INFO`下且要求默认名为`servlet-name+"-servlet.xml"`，可以在配置`web.xml`时，在`servlet`中的`init-param`标签下指定配置文件的**路径**和**名字**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://java.sun.com/xml/ns/javaee"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
	version="3.0">
	<display-name>ego-manager-web</display-name>
	<welcome-file-list>
		<welcome-file>/WEB-INF/pages/login.jsp</welcome-file>
	</welcome-file-list>
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:spring/applicationContext-*.xml</param-value>
	</context-param>
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	<servlet>
		<servlet-name>springmvc</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:spring/springmvc.xml</param-value>
		</init-param>
	</servlet>
	<servlet-mapping>
		<servlet-name>springmvc</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>

	<!-- 解决post提交乱码filter begin -->
	   <!-- 配置过滤器参数 -->
	<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>
	   <!-- 配置过滤器的过滤范围 -->
	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	<!-- 解决post提交乱码filter end -->
</web-app>
```
## 1.2 主配置文件
通过在后台Controllerr层配置``@RequestMapping`来实现前台页面`URI`的匹配
对于需要后台向前台返回数据的情况，需要在对应的方法前添加`@ResponseBody`注解


```xml
	<!-- 开启扫描 -->
	<mvc:annotation-driven />
	<!-- 指定controller扫描的包 -->
	<context:component-scan base-package="com.bjsxt.controller" />
	<!-- 静态资源路径映射 -->
	<mvc:resources location="/WEB-INF/static/" mapping="/**"></mvc:resources>

	<!-- 视图解析器 -->
	<bean id="viewResolver"
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<!-- 前缀 -->
		<property name="prefix" value="/WEB-INF/pages/"></property>
		<!-- 后缀 -->
		<property name="suffix" value=".jsp"></property>
	</bean>

	<!-- 配置文件上传解析 -->
	<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<!-- 最大文件上传大小 单位为byte 20M-->
		<property name="maxUploadSize" value="20971520"/>
		<!-- 指定编码 方式  -->
		<property name="defaultEncoding" value="UTF-8"/>
```

# 2. 请求转发和重定向

```java
	@RequestMapping("/dispatcher.action")
	public String dispatcher() {
		System.out.println("UserAction.dispatcher()" + UUID.randomUUID());
		return "dispatcher";
	}

	@RequestMapping("/chain.action")
	public String chain() {
		System.out.println("UserAction.chain()" + UUID.randomUUID());
		return "forward:/teacher/teach.action";
	}

	@RequestMapping("/redirect.action")
	public String redirect(String uname, String realname, RedirectAttributes redirectAttributes) throws UnsupportedEncodingException {
		System.out.println("UserAction.redirect(" + uname + ")(" + realname + ")" + UUID.randomUUID());
		return "redirect:/redirect.jsp?uname=lisi&realname=%E5%BC%A0%E4%B8%89";
	}

	@RequestMapping("/redirectAction.action")
	public String redirectAction() {
		System.out.println("UserAction.redirectAction()" + UUID.randomUUID());
		return "redirect:/teacher/teach.action";
	}
```
# 3. Ajax
使用注解
前端：

```js
function getAjaxData(){
	$.ajax({
		url:"/ajax/getData",
		dataType:"JSON",
		success:function(result){
			alert(result.name);
		}
	);
}

function uploadData(){
	var user = {"id":3,"name":"bjsxt","password":"123456"};
	$.ajax({
		type:"POST",
		url:"${ctx}/ajax/uploadData",
		data:JSON.stringify(user),
		contentType:"application/json",
		dataType:"JSON",
		success:function(result){
			alert(result.name);
		}
	});
}
```

后台：

```java

@Controller
@RequestMapping("/ajax")
public class AjaxController {

	//返回json数据
	@RequestMapping("/getData")
	@ResponseBody
	public User getData(){
		User user = new User(2,"401","111111");
		return user;
	}

	//接收json数据
	@RequestMapping("/uploadData")
	@ResponseBody
	public User uploadData(@RequestBody User user,ModelMap modelMap){
		return user;
	}
}
```
# 6. 路由功能
`@PathVariable` 用来获取 `URI`路径中的变量

/{UserID}/find/{id}

```java
@Controller
@RequestMapping("/{UserID}")
public class ControllerTest{
	@RequestMapping("/find/{id}")
	public void find(@PathVariable String id,String name){
		//方法实体
	}
}
```

## 6.1 匹配原则

1. URI模板变量的数目和通配符数量的总和最少的那个路径模板更准确。
2. 如果两个模板的URI模板数量和通配符数量总和一致，则路径更长的那个模板更准确
3. 如果两个模板的数量和长度均一致，则那个具有更少通配符的模板是更加准确的



# 7. 拦截器
## 7.1 实现接口`HandlerInterceptor`
## 7.2 继承`HandlerInterceptorAdaptor`抽象类
