## 需求说明

runtime-dfa-boot需要去除默认trace-span配置
## 需求编号

20220922006
## 开发完成时间

2022-09-29
## 修改模块和版本

-dfa-boot v1.0.1.0
## 变更内容

runtime-dfa-boot需要去除默认trace-span配置
## 在线文档是否调整

否
- ## SVN记录
  
  web-dfa-boot 变更：
  
  
  ```java
  Index: JAVA/runtime-dfa-boot/src/main/resources/logback-spring.xml
  IDEA additional info:
  Subsystem: com.intellij.openapi.diff.impl.patch.CharsetEP
  <+>UTF-8
  ===================================================================
  diff --git a/JAVA/runtime-dfa-boot/src/main/resources/logback-spring.xml b/JAVA/runtime-dfa-boot/src/main/resources/logback-spring.xml
  --- a/JAVA/runtime-dfa-boot/src/main/resources/logback-spring.xml	(revision 12254)
  +++ b/JAVA/runtime-dfa-boot/src/main/resources/logback-spring.xml	(revision 12255)
  @@ -8,7 +8,7 @@
     <appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender">
         <!--展示格式 layout-->
         <layout class="ch.qos.logback.classic.PatternLayout">
  -            <pattern>%d{HH:mm:ss} %-5level [%X{SOFA-TraceId}] [%thread] %logger:%line %msg%n</pattern>
  +            <pattern>%d{HH:mm:ss} %-5level  [%thread] %logger:%line %msg%n</pattern>
         </layout>
     </appender>
     <appender name="fileAllLog"
  @@ -40,7 +40,7 @@
         <!--日志输出编码格式化 -->
         <encoder>
             <charset>UTF-8</charset>
  -            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level [%X{SOFA-TraceId}] [%thread] %logger:%line %msg%n</pattern>
  +            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level [%thread] %logger:%line %msg%n</pattern>
         </encoder>
     </appender>
  
  @@ -67,7 +67,7 @@
         <!--日志输出编码格式化 -->
         <encoder>
             <charset>UTF-8</charset>
  -            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level [%X{SOFA-TraceId}] [%thread] %logger:%line %msg%n</pattern>
  +            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level  [%thread] %logger:%line %msg%n</pattern>
         </encoder>
     </appender>
  ```