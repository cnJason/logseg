- DFA-Boot 1.0.0.5 从Spring 2.1.17-RELEASE 升级到2.5.13，其中包含三个大版本，改动的部分较多，故提供本文来说明需要关注的功能点和升级事项。本文将按照Spring和SpringBoot两部分说明升级的事务。
- ## Spring升级事项
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
-