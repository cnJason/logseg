- **背景**
  
  DFA-Boot是新意公司基于SpringBoot（SOFA-Boot）上开发的面向证券行业提供特有能力的开发套件。目前已经满足基本的开发需求，由于DFA-Boot 1.0.1.0版本需要提供一个稳定的基线能力，基于现有的能力发现各模块的加载顺序相对模糊，且由于是基于SpringBoot的。这些模块的加载顺序与Spring的加载顺序是糅杂在一起的。故梳理此文档，并且后续会基于此文档设计基于DFA-Boot的加载钩子用于提供业务的加载顺序能力。
  
  **模块划分**
  
  DFA-Boot目前存在以下几个模块：
- common-dfa-boot
- runtime-dfa-boot
- jdbc-dfa-boot
- rpc-dfa-boot
- web-dfa-boot
- shine-soa-dfa-boot
  
  **除了common-dfa-boot以外，其他的模块均有自动装配的需求。**
  
  **各模块梳理**
  
  **common-dfa-boot**
  
  common-dfa-boot只有工具类和基本属性。没有自动装配的需求，故无需考虑。
  
  **runtime-dfa-boot**
  
  runtime-dfa-boot的自动装配能力在runtime-dfa-boot-starter中，并且包括了以下几个配置：
- DfaCRCConfiguration
- DfaLocalPropertiesConfiguration
- DfaRuntimeAutoConfiguration
  
  下面我们将梳理以上三个配置的加载顺序：
  
  **DfaCRCConfiguration**
  
  **加载顺序**
  
  before DfaRuntimeAutoConfiguration
  
  after DfaConfigClientApp( 在有crc配置的时候加载）
  
  **功能说明**
  
  用于装配基于CRC的log/appconfig/redis配置。
  
  **DfaLocalPropertiesConfiguration**
  
  **加载顺序**
  
  项目启动后（无约束）
  
  **功能说明**
  
  用于装配基于本地的log/redis配置。
  
  **DfaRuntimeAutoConfiguration**
  
  **加载顺序**
  
  after  DfaCRCConfiguration
  
  after SofaFramework
  
  **功能说明**
  
  自动装配redis/Dfa Annotation/ 审计日志检查点。
  
  **runtime-dfa-boot加载顺序**
  
  注册中心模式的加载顺序：
  
  本地模式的加载顺序：
  
  **jdbc-dfa-boot**
  
  jdbc-dfa-boot的自动装配能力在jdbc-dfa-boot-starter中，并且包括了以下几个配置：
- DfaJdbcAutoConfiguration
- DfaJdbcMasterSlaveAutoConfiguration
  
  下面我们将梳理以上两个配置的加载顺序：
  
  **DfaJdbcAutoConfiguration**
  
  **加载顺序**
  
  before DataSourceAutoConfiguration
  
  after DfaConfigClientApp
  
  **加载条件**
  
  shine.dfa.datasource.use-local-config == false or shine.dfa.datasource.user-local-config == false
  
  **功能说明**
  
  用于装配基于CRC的datasource配置，并且监听CRC上的配置，进行动态更新。
  
  **备注：基于本地配置文件的datasource配置由Springboot的DataSourceAutoConfiguration提供。**
  
  **DfaJdbcMasterSlaveAutoConfiguration**
  
  **加载顺序**
  
  before DataSourceAutoConfiguration
  
  after DfaConfigClientApp
  
  **加载条件**
  
  shine.dfa.datasource.use-local-config == false or shine.dfa.datasource.user-local-config == false
  
  并且
  
  datasource.splitting.enabled == true
  
  **功能说明**
  
  用于装配基于CRC的读写分离数据源配置，并且监听CRC上的配置，进行动态更新。
  
  **备注：基于本地配置文件的datasource配置由shardingsphere-jdbc提供（待梳理）。**
  
  **jdbc-dfa-boot加载顺序**
  
  单数据源的加载顺序
  
  配置读写分离的加载顺序
  
  **rpc-dfa-boot**
  
  rpc-dfa-boot的自动装配能力在rpc-dfa-boot-starter中，并且包括了以下一个配置：
- DfaRpcAutoConfiguration
  
  下面我们将梳理这个配置的加载顺序：
  
  **DfaRpcAutoConfiguration**
  
  **加载顺序**
  
  项目启动后（无约束），但是泛化调用依赖DfaApplicationContext和RegistryConfig。所以是在runtime-dfa-boot加载完成进行加载。
  
  **功能说明**
  
  用于装配基于CRC的泛化调用配置。
  
  **rpc-dfa-boot加载顺序**
  
  **web-dfa-boot**
  
  web-dfa-boot的自动装配能力在web-dfa-boot-starter中，并且包括了以下配置：
- DfaWebSocketConfiguration
- WebBaseConfiguration
- WebBasePreConfiguration
- WebConfiguration
- WebLocalConfiguration
- WebPropertiesConfiguration
- WebPropertiesLocalConfiguration
  
  下面我们将梳理这个配置的加载顺序：
  
  **DfaWebSocketConfiguration**
  
  **加载顺序**
  
  项目启动后（无约束）
  
  **加载条件**
  
  项目有引入websocket 模块，目前用这个类判断：org.springframework.web.socket.server.standard.ServerEndpointExporter
  
  **功能说明**
  
  用于装配websocket功能。
  
  **WebBaseConfiguration**
  
  **加载顺序**
  
  before MultipartAutoConfiguration
  
  **加载条件**
  
  无
  
  **功能说明**
  
  用于装配WebMvc的配置，例如拦截器等。
  
  **WebBasePreConfiguration**
  
  **加载顺序**
  
  before WebMvcAutoConfiguration
  
  **加载条件**
  
  无
  
  **功能说明**
  
  用于加载i18n配置等与WebMvc无关的前置配置。
  
  **WebConfiguration**
  
  **加载顺序**
  
  after WebBaseConfiguration
  
  after DfaConfigClientApp
  
  **加载条件**
  
  shine.dfa.web.use-local-config == false
  
  **功能说明**
  
  用于加载crc上的web拦截器配置。（CSRF等）
  
  **WebLocalConfiguration**
  
  **加载顺序**
  
  after WebBaseConfiguration
  
  **加载条件**
  
  shine.dfa.web.use-local-config == true
  
  **功能说明**
  
  用于加载本地配置文件上的web拦截器配置。（CSRF等）
  
  **WebPropertiesConfiguration**
  
  **加载顺序**
  
  before WebBaseConfiguration
  
  after DfaConfigClientApp
  
  **加载条件**
  
  shine.dfa.web.use-local-config == false
  
  **功能说明**
  
  用于加载crc上的web配置。
  
  **Web****Properties****LocalConfiguration**
  
  **加载顺序**
  
  before WebBaseConfiguration
  
  **加载条件**
  
  shine.dfa.web.use-local-config == true
  
  **功能说明**
  
  用于加载本地配置文件上的web配置。
  
  **web-dfa-boot加载顺序**
  
  使用CRC的情况下加载顺序如下：
  
  使用本地配置情况下的加载顺序如下：
  
  **shine-soa-dfa-boot**
  
  shine-soa-dfa-boot的自动装配能力在shine-soa-dfa-boot-starter中，并且包括了以下几个配置：
- DfaEjbAutoConfiguration
- DfaShineAutoConfiguration
- DfaShineCRCAutoConfiguration
- DfaShineLocalAutoConfigurationon
  
  下面我们将梳理这个配置的加载顺序：
  
  **DfaEjbAutoConfiguration**
  
  **加载顺序**
  
  before WebConfiguration
  
  after EjbAgent
  
  **功能说明**
  
  用于装配ejb-dfa-boot组件（适配Eframe3.0)
  
  **DfaShineAutoConfiguration**
  
  **加载顺序**
  
  before WebConfiguration
  
  **功能说明**
  
  用于重写WebConfiguration中配置的默认组件，例如字典，审计日志等。
  
  **DfaShineCRCAutoConfiguration**
  
  **加载顺序**
  
  before DfaShineAutoConfiguration
  
  **加载条件**
  
  shine.dfa.soa.use-local-config == false
  
  **功能说明**
  
  用于加载crc上id为shine.dfa.soa的配置信息。
  
  **DfaShineLocalAutoConfiguration**
  
  **加载顺序**
  
  before DfaShineAutoConfiguration
  
  **加载条件**
  
  shine.dfa.soa.use-local-config == true
  
  **功能说明**
  
  用于加载配置文件中开头为shine.dfa.soa的配置信息。
  
  **shine-soa-dfa-boot加载顺序**
  
  使用CRC的情况下加载顺序如下：
  
  使用本地配置文件的情况加载顺序如下：
  
  **SofaBoot加载顺序**
  
  由于DFA-Boot是依赖sofa-boot的。故需要整理下sofa-boot的加载顺序。以便于进行梳理的时候能够做到准确的找到对应的切点和融合方案，原始数据参看：f附录：《DFA-Boot加载顺序清单》
  
  **优化点**
- 对于dfa-boot的各组件进行调整，启动顺序将调整为：Spring Initiallize --> DFAConfigApp -> runtime-dfa-boot --> shine-soa-dfa-boot --> jdbc-dfa-boot -->  web-dfa-boot 。
- 添加三个hook用于业务方进行监听：
- runtme-dfa-boot和rpc-dfa-boot 加载完成后
- jdbc-dfa-boot加载完成后。
- web-dfa-boot加载完成后。	
  
  **Hook用途**
- runtime-dfa-boot/rpc-dfa-boot 加载完成后，即可初始化其他第三方独立中间件，例如mongodb，消息队列等组件。
- jdbc-dfa-boot 加载完成后，即可初始化不依赖endpoint的定时任务等组件。
- web-dfa-boot加载完成后可认为服务已经启动完毕，可加载所有服务启动完毕后需要执行的动作。
  
  **runtime-dfa-boot hook设计**
  
  runtime-dfa-boot启动的hook命名为：DfaRuntimeInitialized，并且处于Spring Initiallize --> DFAConfigApp  之后，jdbc-dfa-boot 之前。
  
  **jdbc-dfa-boot hook设计**
  
  jdbc-dfa-boot启动的hook命名为：DfaDatasourceInitialized，并且处于runtime-dfa-boot  之后，web-dfa-boot之前。
  
  **web-dfa-boot hook设计**
  
  web-dfa-boot启动的hook命名为：DfaWebInitialized，并且处于jdbc-dfa-boot -> Spring servlet  之后，WebMvcAutoConfiguration 之前。
  
  **附录**
  
  **DFA-Boot加载顺序清单**
- i18n相关的Bean
- MessageSourceAutoConfiguration
- MessageSourceProperties
- ResourceBundleMessageSource
- Tomcat相关bean
- ServletWebServerFactoryConfiguration$EmbeddedTomcat
- WebSocketServletAutoConfiguration$TomcatWebSocketConfiguration
- TomcatWebSocketServletWebServerCustomizer
- ServletWebServerFactoryAutoConfiguration
- server-org.springframework.boot.autoconfigure.web.ServerProperties
- ServletWebServerFactoryCustomizer
- TomcatServletWebServerFactoryCustomizer
- EmbeddedWebServerFactoryCustomizerAutoConfiguration$TomcatWebServerFactoryCustomizerConfiguration
- tomcatWebServerFactoryCustomizer
- SpringServlet部分bean：
- HttpEncodingAutoConfiguration
- HttpEncodingAutoConfiguration$LocaleCharsetMappingsCustomizer
- ErrorMvcAutoConfiguration
- DispatcherServletAutoConfiguration$DispatcherServletRegistrationConfiguration
- DispatcherServletAutoConfiguration$DispatcherServletConfiguration
- servlet.WebMvcProperties
- servlet.DispatcherServlet
- web-dfa-boot相关配置
- WebBaseConfiguration
- WebPropertiesConfiguration
- DFAConfigApp 相关配置
- getDFAConfigClientApp
- shine-dfa-boot相关配置
- DfaShineAutoConfiguration
- runtime-dfa-boot相关配置
- DfaRuntimeAutoConfiguration
- DfaCRCConfiguration
- DfaLocalPropertiesConfiguration
- jedisLocalConfig
- jedisConfig
- redisClient
- dfaSecurityManager
- SofaRpcAutoConfiguration
- SofaBootRpcProperties
- consumerConfigHelper
- consumerConfigContainer
- providerRegisterProcessor
- processorContainer
- zookeeperConfigurator
- multicastConfigurator
- localFileConfigurator
- meshConfigurator
- nacosConfigurator
- sofaRegistryConfigurator
- consulConfigurator
- rpc-dfa-boot相关配置
- DfaRpcAutoConfiguration
- serviceCombConfigurator
- registryConfigMap
- registryConfigContainer
- shine-soa-dfa-boot相关配置（2）
- com.shine.dfa.shine.web.session.RedisLoginManager
- com.shine.dfa.shine.permission.DefaultPermissionsLoader
- com.shine.dfa.shine.permission.PermissionsRealm
- com.shine.dfa.shine.permission.DefaultPermissionsHandler
- com.shine.dfa.shine.web.session.RedisDebugManager
- com.shine.dfa.web.interceptor.SimpleCORSFilter
- dfaMessageSource
- com.shine.dfa.shine.i18n.WebInitializer
- runtime-dfa-boot相关配置（2）
- com.shine.dfa.runtime.DfaApplicationContext
- com.shine.dfa.runtime.cache.guava.DefaultLocalCacheManager
- web-dfa-boot相关配置（2）
- com.shine.dfa.web.autoconfigure.WebConfiguration
- spring servlet 相关配置
- javax.servlet.MultipartConfigElement
- dispatcherServletRegistration
- errorPageCustomizer
- tomcatServletWebServerFactory
- OrderedRequestContextFilter
- WebMvcAutoConfiguration
- formContentFilter
- characterEncodingFilter
- SpringBoot启动
- dfaSampleApplication
- jdbc-dfa-boot相关配置
- DataSourceConfiguration$Hikari
- DataSourceProperties
- spring配置
- Controller相关配置
- Service相关配置
- DAO相关配置
- shine-soa-dfa-boot相关配置（3）
- dfaSoaProperties
- gridDataConverter
- autoExportBodyAdvice
- epbosRequestBodyAdvice
- profileLoader
- DefaultUserProfile
- DefaultApplicationProfileLoader
- DefaultApplicationProfile
- GlobalExceptionHandler
- autoPagedResponseBodyAdvice
- web-dfa-boot配置（3）
- WebBasePreConfiguration
- SessionLocaleResolver
- WebMvcAutoConfiguration
- SpringDataWebConfiguration
- WebMvcAutoConfiguration$EnableWebMvcConfiguration
- mvcContentNegotiationManager
- mvcConversionService
- jdbc-dfa-boot配置（2）