# Spring Web Flow

是一个Web框架，适用于元素按规定流程运行的程序。

组件配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns:flow="http://www.springframework.org/schema/webflow-config"
 xsi:schemaLocation="http://www.springframework.org/schema/webflow-config 
   http://www.springframework.org/schema/webflow-config/spring-webflow-config-2.3.xsd
   http://www.springframework.org/schema/beans 
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

<!-- 创建流程执行器 -->
<flow:flow-executor id="flowExecutor" bese-path="/WEB-INF/flows"/>
	
<!-- 配置流程注册表 -->
<flow:flow-registry id="flowRegistry" base-path="/WEB-INF/flows">
	<flow:flow-location-pattern value="*-flow.xml" />
</flow:flow-registry>

<flow:flow-registry id="flowRegistry">
	<flow:flow-location path="/WEB-INF/flows/flow.xml" />
</flow:flow-registry>

<!-- 配置FlowHandlerMapping -->
<bean class="org.springframework.webflow.mvc.servlet.FlowHandlerMapping">
	<property name="flowRegistry" ref="flowRegistry" />
</bean>

<!-- 配置 FlowHandlerAdapter -->
<bean class="org.springframework.webflow.mvc.servlet.FlowHandlerAdapter">
	<property name="flowExecutor" ref="flowExecutor" />
</bean>
```

流程执行器：负责创建和执行流程

流程注册表：加载流程定义，并让流程执行器能够使用它们

FlowHandlerMapping ：将流程请求发送给Spring Web Flow

FlowHandlerAdapter ：响应发送的流程请求并对其进行处理。是DispatcherServlet和Spring Web Flow的桥梁


组成流程的元素

- 状态：流程中事件发生的地点
	- 行为(Action)：流程逻辑发生的地方
	- 决策(Decision)：将流程分为2个方向，它会基于流程数据的评估结果确定流程方向
	- 结束(End)：是流程的最后一站，一旦进入End状态，流程就会终止
	- 子流程(Subflow)：会在当前正在运行的流程上下文中启动一个新的流程
	- 视图(View)：会暂停流程并邀请用户参与流程
- 转移：连接状态的工具
- 流程数据：流程的当前状况

视图状态

```xml
<!-- 定义视图状态
id属性的含义
	1. 标识这个状态
	2. 流程到达这个状态时要展现的逻辑视图名
-->
<view-state id="welcome" />
<!-- 属性含义
view：显示指定视图名
model：表单将绑定的流程作用域内的对象
-->
<view-state id="welcome" view="greeting" model="flowScope.paymentDetails" />
```

行为状态

行为状态是应用程序自身在执行任务。一般会触发Spring管理bean的一些方法，并根据方法调用的执行结果转移到另一个状态。

```xml
<action-state id="saveOrder">
	<!-- 给出了行为状态要做的事情 -->
	<evaluate expression="pizzaFlowActions.saveOrder(order)" />
	<transition to="thankYou" />
</action-state>
```

决策状态

将评估一个Boolean类型的表达式，在两个状态转移中选择一个。

```xml
<decision-state id="checkDeliveryArea">
	<if test="pizzaFlowActions.checkDeliveryArea(customer.zipCode)"
		then="addCustomer"
		else="deliveryWarning" />
</decision-state>
```

子流程状态

允许在一个正在执行的流程中调用另一个流程。类似于在一个方法中调用另一个方法。

```xml
<subflow-state id="order" subflow="pizza/order">
	<!-- 子流程的输入 -->
	<input name="order" value="order" />
	<!-- 子流程结束状态为orderCreated时，转移到payment状态 -->
	<transition on="orderCreated" to="payment" />
</subflow-state>
```

结束状态

指定了流程的结束

```xml
<end-state id="customerReady" />
```

接下来会发生什么，取决于几个因素

- 如果结束的流程是一个子流程，那调用它的流程将会从 `<subflow-state>` 处继续执行。
- 如果end-state设置了view属性，指定的视图将会被渲染。如果添加"externalRedirect:"前缀的话，将会重定向到流程外部的页面，如果添加"flowRedirect:"前缀，将会重定向到另一个流程中。
- 以上都不是，会结束这个流程，开始一个新的流程。

转移

转移定义了状态完成时，流程要去向哪里。

转移使用 `<transition>` 元素来定义，它会作为各种状态元素的子元素。

```xml
<transition to="customerReady" />
<transition on="phoneEntered" to="lookupCustomer" />
<transition on-exception="xxx.xxx.MyException" to="registrationForm" />

```

属性to用于指定流程的下一个状态，如果 `<transition>` 元素只使用了to属性，则它时当前状态的默认转移项。

属性on用来指定触发转移的事件，当触发on指定的事件时，流程会进入to指定的状态。

属性on-exception类似on属性，指定了要发生转移的异常。

全局转移

通用的转移，流程中所有的状态都会默认拥有这个转移

```xml
<global-transitions>
	<transition on="cancel" to="endState" />
</global-transitions>
```

流程数据

流程数据保存在变量中，而变量可以在流程的各个地方进行引用。

```xml
<!-- 创建变量 -->
<var name="customer" class="xxx.xxx.Customer" />
<!-- 计算了一个SpEL表达式，并将结果放在了视图作用域的toppingsList变量中 -->
<evaluate result="viewScope.toppingsList" expression="T(xxx.xxx.Topping).asList()" />
<!-- set元素设置变量的值 -->
<set name="flowScope.pizza" value="new xxx.Pizza()" />
```


流程数据的作用域

|范围|生命作用域和可见性|
|---|---|
|Conversation|最高层的流程开始时创建，在最高层级的流程结束时销毁。被最高层级的流程和其他所有的子流程所共享|
|Flow|当流程开始时创建，流程结束时销毁。只在创建它的流程中是可见的|
|Request|当一个请求进入流程时创建，在流程返回时销毁|
|Flash|当流程开始时创建，在流程结束时销毁，。在视图状态渲染后，也会被清除|
|View|当进入视图状态时创建，这个状态退出时销毁。只在视图状态内是可见的。|

当使用 `<var>` 声明变量时，变量始终时流程作用域的。
当使用 `<set>` 或 `<evaluate>` 时，作用域通过name或者result属性的前缀指定。

定义基本流程

默认情况下，流程定义文件(xml文件)中的第一个状态也会是流程访问中的第一个状态。

```xml
<!-- 指定开始状态 -->
<flow ... start-state=".."></flow>
```

Spring Web Flow 为视图用户提供了一个 flowExecutionUrl 变量，它包含了流程的URl

JSP页面

```html
<form>
	<input type="hidden" name="_flowExecutionKey" value="${ flowExecutionKey }" />
	<input type="text" name="phoneNumber" />
	<input type="submit" name="_eventId_phoneEntered" value="Lookup" />
</from>
```

"_eventId_" 是提供给 Spring Web Flow 的线索，它表明了接下来要触发事件。

保护Web应用

```xml
<view-state id="restricted">
	<!-- 只有授权了ROLE_ADMIN访问权限的用户才能访问 -->
	<secured attributes="ROLE_ADMIN" match="all" />
</view-state>
```

attributes属性，指定了用户访问状态所需要的权限

match属性可以为[any, all]，any表示有其中一个权限即可，all表示需要有全部权限。

Spring Security

是一个安全性框架，为基于Spring的应用，提供声明式安全保护。在Web请求级别和方法调用级别，处理身份认证和授权。

过滤Web请求

简单的安全性配置

javaConfig

```java
@Configuration
// 启用Web安全性
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurationAdapter{
	// code
}

// 启用Spring MVC安全性
@EnableWebMvcSecurity

```