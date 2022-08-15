- DFA-Boot 1.0.0.5 从Spring 2.1.17-RELEASE 升级到2.5.13，其中包含三个大版本，改动的部分较多，故提供本文来说明需要关注的功能点和升级事项。本文将按照Spring和SpringBoot两部分说明升级的事务。
- ## Spring升级事项
- ### JDK依赖
- Spring Framework 5.1 需要 JDK 8 或更高版本，并且首次特别支持 JDK 11（作为下一个长期支持版本）。我们强烈建议针对 JDK 11 的任何应用程序升级到 Spring Framework 5.1，在类路径和模块路径上提供无警告体验。
- ### 依赖升级
- Jackson 依赖升级到2.12
- Groovy 依赖升级至3.0
- 废弃对于RxJava 1.x的支持，推荐使用RxJava 2.x和3.x
- JCA CCI 支持已被弃用
- ### 核心容器升级
- `InstantiationAwareBeanPostProcessorAdapter` 现在已弃用，取而代之的是 `(Smart)InstantiationAwareBeanPostProcessor`
- `@EventListener` 方法使用 `Ordered.LOWEST_PRECEDENCE` now 的隐式顺序值，与事务同步和 `@TransactionalEventListener` 方法保持一致。如果需要自定义排序，请始终 `@Order` 在所有Listener方法上声明值
- ### web application升级
- `@ExceptionHandler` 方法现在在查找匹配项时会检查所有异常原因。以前只检查第一个就返回了。
- `WebSocketConfigurationSupport` 并且 `WebSocketMessageBrokerConfigurationSupport` 已被重构为不需要 CGLIB 代理
- 弃用  `MediaType.APPLICATION_JSON_UTF8`  和  `MediaType.APPLICATION_PROBLEM_JSON_UTF8`。
#### CORS 处理

在 Spring Framework 5.2 中对CORS 处理进行了[重大更新：](https://github.com/spring-projects/spring-framework/commit/d27b5d0ab6e8b91a77e272ad57ae83c7d81d810b)
- CORS 处理现在仅用于启用 CORS 的端点
- `Origin` 跳过带有标头的同源请求的 CORS 处理
- 为 CORS 端点上的非 CORS 请求添加了不同的标头
  
  这些更改引入了一种 `AbstractHandlerMapping#hasCorsConfigurationSource` 方法（在 Spring MVC 和 WebFlux 中），以便能够有效地检查 CORS 端点。升级到 Spring Framework 5.2 时，扩展 `AbstractHandlerMapping` 和支持 CORS 的处理程序映射应覆盖 `hasCorsConfigurationSource` 其自定义逻辑。
-
- ### Spring MVC升级
- `LocaleResolver` ,  `ThemeResolver` , `FlashMapManager` 和 `RequestToViewNameTranslator` bean 现在被声明在注解 `WebMvcConfigurationSupport` 的 `DelegatingWebMvcConfiguration` 级别。
- JavaConfig 覆盖其中一个默认 bean 的非 Spring Boot 项目可能需要更新，因为 bean 声明现在应该发生在带有注释的配置类中 `@EnableWebMvc` （仍然使用它们的[良好-已知名称](https://docs.spring.io/spring-framework/docs/5.3.x/javadoc-api/constant-values.html#org.springframework.web.servlet.DispatcherServlet.FLASH_MAP_MANAGER_BEAN_NAME)），例如 `LocaleResolver`
- ```
  @Configuration
  public class WebConfig extends DelegatingWebMvcConfiguration {
  - @Bean
  	@Override
  	public LocaleResolver localeResolver() {
  return new FixedLocaleResolver(new Locale("fr", "FR"));
  	}
  }
  ```
- `@RequestParam` 并在需要参数时强制 a和 Servlet集合/数组 `@RequestPart` 中的至少一个元素（即未明确标记为可选），否则将参数解析为。 `MultipartFile`  `Part`  `MultipartFile`  `Part`  `null`
- Spring MVC 默认不再执行 `.*` 后缀模式匹配
- ## SpringBoot 升级
- ### 默认禁用JMX
	- 从[#16090 开始](https://github.com/spring-projects/spring-boot/issues/16090)，默认情况下不再启用 JMX。可以使用配置属性启用此功能 `spring.jmx.enabled=true` 。如果您使用 IDE 功能来管理您的应用程序，您可能还希望在此处启用该标志。
- ### JUnit 5
- `spring-boot-starter-test` 现在默认提供 JUnit 5。JUnit 5 的老式引擎默认包含在内，以支持现有的基于 JUnit 4 的测试类，以便您可以在准备好迁移到 JUnit 5 时迁移到 JUnit 5。也可以在同一模块中混合使用基于 JUnit 4 和 JUnit 5 的测试类。如果您愿意，这允许您逐步迁移到 JUnit 5。
- 请注意，JUnit 4 的 Maven Surefire 插件不支持该 `listener` 属性。如果您有类似以下的 Maven 配置：
- ```
  <configuration>
  	<properties>
  <property>
  	<name>listener</name>
  	<value>com.example.CustomRunListener</value>
  </property>
  	</properties>
  </configuration>
  ```
- 您不能使用 `junit-vintage-engine` ，您需要显式回滚到 JUnit 4：
- ```
  <dependencies>
  	<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-test</artifactId>
  <scope>test</scope>
  <exclusions>
  	<exclusion>
  		<groupId>org.junit.jupiter</groupId>
  		<artifactId>junit-jupiter</artifactId>
  	</exclusion>
  	<exclusion>
  		<groupId>org.junit.vintage</groupId>
  		<artifactId>junit-vintage-engine</artifactId>
  	</exclusion>
  	<exclusion>
  		<groupId>org.mockito</groupId>
  		<artifactId>mockito-junit-jupiter</artifactId>
  	</exclusion>
  </exclusions>
  	</dependency>
  	<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
  	</dependency>
  </dependencies>
  ```
	- ### AssertJ 升级至3.12
	-