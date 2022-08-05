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
- ## 整体设计
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
- ##### 端点开关
- 我们可以通过在配置文件中添加以下配置：
- ```json
  # 这样写就可以开放所有的endpoint(ping，info，health和metrics)
  management.endpoints.web.exposure.include=*
  
  # 也可以指定开放指定的endpoint
  management.endpoints.web.exposure.include=ping,info
  
  # excludek可以配置关闭对应的endpoint.
  management.endpoints.web.exposure.exclude=beans
  
  ```
- ##### 流量控制
- 通过配置文件来处理组件和端点的开关的配置。
- 我们可以通过在配置文件中添加以下配置：
- ```json
  # 这样写就可以开放所有的endpoint(ping，info，health和metrics)
  management.endpoints.web.exposure.include=*
  
  # 也可以指定开放指定的endpoint
  management.endpoints.web.exposure.include=ping,info
  
  # excludek可以配置关闭对应的endpoint.
  management.endpoints.web.exposure.exclude=beans
  
  ```
-
- #### 自定义Metrics配置
- 提供@Metrics的Annotation将业务特定指标输出到metrics的指标中，Metrics参照micrometer的能力来对接。
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
- info端点用查看服务的静态信息，包括最后生效的配置文件、自定义的各种参数的信息。返回的数据格式如下：
- ```json
  {
    "shine.dfa.rpc.registry.address": "",
    "shine.dfa.config.serveraddr":"",
    "shine.dfa.config.namespace": "",
  }
  ```
- ## metrics端点
- metrics端点不是一个端点，而是指标端点的集合。通过访问metrics端点可以获得所有的metrics指标的信息。
- 如果访问metrics/{metricName} 可以获取指标具体的信息。
- ```json
  {
    "names": [
      "hikaricp.connections",
      "hikaricp.connections.acquire",
      "hikaricp.connections.active"
    ]
  }
  ```
- 具体的指标信息的格式如下：
- ```json
  {
    "name": "hikaricp.connections",
    "description": "hikaricp connection count",
    "baseUnit": "",
    "measurements" :[ // 指标列表.
      {
        "statistic":"VALUE", //统计类型
        "value":10  // 统计值.
      }
    ]
  }
  ```
- ### 采集端点指标
- 采集端点指标我们采用的方案是依赖mircometer的能力，提供以下四种采集的维度：
- 1. **Gauge**
  2. **Counter**
  3.  **Timer**
  4.  **Summary**
- #### Gauge(计量器)
- **Gauge**（计量器）是最简单的度量类型，只有一个简单的返回值，他用来记录一些对象或者事物的瞬时值。
  使用方式：
- ```java
  Metrics.gauge("aaaa.gauge",3);
  ```
- 只要设置了这个值，就可以在metrics端点看到 aaaa.gauge这个指标。如果访问metrics/aaaa.gauge这个endpoint的话，会得到对应的数值。
- #### Counter(计数器)
- **Counter**（计数器）简单理解就是一种只增不减的计数器。它通常用于记录服务的请求数量、完成的任务数量、错误的发生数量等等。
  使用方式：
- ```java
  static final Counter userCounter = Metrics.counter("user.counter.total");
   
      public void processCollectResult() {
          userCounter.increment(1D);
      }
  ```
- 只要调用了上面的方法，就会在计数器中+1，显示的逻辑同上。
- #### Timer(计时器)
- **Timer**（计时器）可以同时测量一个特定的代码逻辑块的调用（执行）速度和它的时间分布。简单来说，就是在调用结束的时间点记录整个调用块执行的总时间，适用于测量短时间执行的事件的耗时分布，例如消息队列消息的消费速率。
  使用方式：
- ```java
  private Timer timer = Metrics.timer("user.test.timer");
  
   
      public void processCollectResult() {
          userCounter.increment(1D);
      }
  
  @GetMapping("/hello")
      public void hello() {
   
          // 执行createOrder方法并记录执行时间
          timer.record(() -> createOrder());
      }
   
      //模拟方法耗时
      private void createOrder() {
          try {
              TimeUnit.SECONDS.sleep(3);
          } catch (InterruptedException e) {
          }
      }
  ```
- 只要调用了上面的方法，就会记录每次请求的耗时，显示的逻辑同上。
- #### Summary(摘要)
- **Summary**（摘要）用于跟踪事件的分布。它类似于一个计时器，但更一般的情况是，它的大小并不一定是一段时间的测量值。在 **micrometer **中，对应的类是 **DistributionSummary**，它的用法有点像 **Timer**，但是记录的值是需要直接指定，而不是通过测量一个任务的执行时间。
  使用方式：
- ```java
  private DistributionSummary summary = Metrics.summary("user.test.summary");
   
      @GetMapping("/hello")
      public void hello() {
          summary.record(2D);
          summary.record(3D);
          summary.record(4D);
      }
  
  ```
- 只要调用了上面的方法，就会记录每次请求的值，显示的逻辑同上。
-