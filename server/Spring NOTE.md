# Spring NOTE

## 1 配置

### 1.1 web.xml 详解

1. 自动扫描
```xml
<!-- 自动扫描该包，使SpringMVC认为包下用了@controller注解的类是控制器 -->
<context:component-scan base-package="com.javen.controller" />
```
2. 注解驱动
```xml
<!-- 扩充了注解驱动，可以将请求参数绑定到控制参数-->
<mvc:annotation-driven />
```
3. 静态资源处理
```xml
<!-- 静态资源处理 css js images -->
<mvc:resources location="/resources/**" mapping="/resource/"/>
```
4. 避免IE执行AJAX时，返回JSON出现下载文件
```xml
<!-- 避免IE执行AJAX时，返回JSON出现下载文件 -->
<bean id="mappingJacksonHttpMessageConverter" 
class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter">
<property name="supportedMediaTypes">
<list>
<value>text/html;charset=UTF-8</value>	
</list>
</property>
</bean>
```
5. 启动SpringMVC的注解功能，完成请求和注解POJO的映射
```xml
<!-- 启动SpringMVC的注解功能，完成请求和注解POJO的映射 -->
<bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">
<property name="messageConverters">
<list>
<ref bean="mappingJacksonHttpMessageConverter"/><!-- JSON转换器 -->
</list>	
</property>
</bean>
```
6. 配置文件上传
```xml
<!-- 配置文件上传，如果没有使用文件上传可以不用配置，当然如果不配，那么配置文件中也不必引入上传组件包 -->
<bean id="multipartResolver"
class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
<!-- 默认编码 -->
<property name="defaultEncoding" value="utf-8" />
<!-- 上传文件最大值 -->
<property name="maxUploadSize" value="10485760000"/>
<!-- 内存中的最大值 -->
<property name="maxInMemorySize" value="40960"/>
<!-- 启用是为了推迟文件解析，以便捕获文件大小异常 -->
<property name="resolveLazily" value="true"/>
</bean>
```
7. 配置viewResolver
```xml
<!-- 配置viewResolver。可以用多个viewResolver。使用order属性排序。InternalResourceViewResolver 放在最后 -->
<bean class="org.springframework.web.servlet.view.ContentNegotiatingViewResolver">
<property name="order" value="1"></property>
<property name="mediaTypes">
<map>
<!-- 告诉视图解析器，返回的类型为json格式 -->
<entry key="json" value="application/json" />
<entry key="xml" value="application/xml" />
<entry key="htm" value="text/htm" />
</map>
</property>
<property name="defaultViews">
<list>
<!-- ModelAndView里的数据变成JSON -->
<bean class="org.springframework.web.servlet.view.json.MappingJacksonJsonView" />
</list>
</property>
<property name="ignoreAcceptHeader" value="true"></property>
</bean>
```
8. 定义跳转的文件的前后缀 ，视图模式配置
```xml
<!-- 定义跳转的文件的前后缀 ，视图模式配置--> 
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"> 
<!-- 这里的配置我的理解是自动给后面action的方法return的字符串加上前缀和后缀，变成一个 可用的url地址 --> 
<property name="prefix" value="/WEB-INF/jsp/" /> 
<property name="suffix" value=".jsp" /> 
</bean>
```
9. destroy-method="close"的作用是当数据库连接不使用的时候,就把该连接重新放到数据池中,方便下次使用调用.