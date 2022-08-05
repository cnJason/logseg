- ## 背景
- 参照SpringBoot Actuator 设计属于DFA体系自己的Actuator，提供对于服务的状态监控、服务的依赖监控、服务状态上报，通用服务信息织入接口等功能。
- ## Spring Boot Actuator
- Spring Boot Actuator是Springboot的四大核心之一，SpringBoot的四大核心如下：
- 1、Actuator：springboot程序监控器
  2、自动装配：简单配置甚至零配置即可运行项目
  3、starter：jar包的引入，解决jar版本冲突问题
  4、CLI：命令行
- 当程序引入依赖并且写好配置之后，启动项目即可看见actuator的对应的端点，SpringBoot Actuator包含的endpoint如下
- ![image.png](../assets/image_1659342798115_0.png)
- 而默认只开启了info和health两个端点。
- ## MircoMeter
- Micrometer 是一个用于基于 JVM 的应用程序的指标检测库。它为最流行的监控系统的检测客户端提供了一个简单的外观，让您可以检测基于 JVM 的应用程序代码，而无需供应商锁定。它旨在为您的指标收集活动增加很少甚至没有开销，同时最大限度地提高指标工作的可移植性。
- Micrometer提供了与供应商无关的接口，包括 timers（计时器），gauges（量规），counters（计数器）， distribution summaries（分布式摘要）， long task timers（长任务定时器）。它具有维度数据模型，当与维度监视系统结合使用时，可以高效地访问特定的命名度量，并能够跨维度深入研究。
- ## DFA Boot Actuator
- DFA Boot Actuator参照Springboot Actuator的使用方式进行设计，并且进行部分的裁剪，目前只暴露四个endpoint：ping，info，health和metrics，并且提供info，health和metrics这三个endpoint的读写能力。
-
- ### 整体设计
- #### endpoint保护
- DFA Boot Actuator模块对所有的endpoint需要有自我保护的能力。包含以下功能：
- 1. 快照能力(返回上一次成功的数据)
  2. 流控能力(可以配置每个endpoint的rate limited)
- #### 信息写入
- DFA Boot Actuator模块需要提供入口给DFA-Boot的组件和业务系统进行info,health和metrics的信息的写入。
- #### endpoint输出
- 通过http输出对应的endpoint信息。
- #### 服务数据输出
- 通过Spring Bean的模式输出对应的域信息。
- #### 组件、端点配置
- 通过配置文件来处理组件和端点的开关、流控的配置。
- #### 自定义Metrics配置
- 提供@Metrics的Annotation将业务特定指标输出到metrics的检查点中，Metrics参照micrometer的能力来对接。
- ## ping端点
- ping端点用于提供此服务是否存活的能力，返回的数据结构如下：
- ```json
  {
    "status":"running",  //状态- ok表示启动完成，running表示正在启动。
    "timestamp": "1659345380000", 、// 当前时间.用时间戳显示.
    "duration": "500hour30min", // 运行时间。格式可配置。默认为HH hour , mm min
  }
  ```
-
- ## health端点
- health端点用于提供此服务本身以及它依赖的各模块和各中间件的联通状态。返回的数据结构如下
- ```json
  {
    "status": "up",
    "details":{
      "dfa-boot": {
      	"status": "UP",
        	"version": "1.0.0.5-SNAPSHOT",
        	"details": {
            	"runtime-dfa-boot" : {
                	"status": "UP",
                	"version": "1.0.0.5-SNAPSHOT"	
              },
            "web-dfa-boot" : {
                	"status": "UP",
                	"version": "1.0.0.5-SNAPSHOT"	
              },
            "shine-soa-dfa-boot" : {
                	"status": "UP",
                	"version": "1.0.0.5-SNAPSHOT"	
              },
            "jdbc-dfa-boot" : {
                	"status": "UP",
                	"version": "1.0.0.5-SNAPSHOT"	
              }
          }
    	},
      "redis": {
        "status": "UP"
      },
      "database": {
         "status": "UP"
      }
    }
  }
  ```
- 上面的应用的详细健康信息发现，健康信息包含DFA组件的状态和中间件的状态，示例的报文中描述的就是启用监控的这个spring boot应用确实是连接了redis和DB，从这里可以判断服务使用的中间件状态。
- 状态有UP 和SHUTDOWN两个状态。
- ## info端点
-
-
-