- ## 背景
- 需要分析华为云CSE和DFA-RPC对接的可行性和初步调研结果
- ## 可行性分析
- 1. 目前DFA-RPC的默认协议是bolt协议。而CSE推荐的默认协议是HTTP、dubbo、gRPC等协议。在传输层没有原生对于bolt协议的支持。故直接使用CSE提供的java-chassis提供的能力是无法完成对接的。
  2. 由于DFA-RPC的registry模块使用的是插件式的设计，故理论上只需要添加基于CSE的registry插件，即可完成此部分任务。
- ## 目标
- 1. 开发基于DFA-RPC的基于CSE的registry插件。
  2. 完成DFA-RPC基于CSE的冒烟验证。
  3. 完成DFA-RPC和CSE的属性映射关系。
- ## 任务拆解
- 基于上面的三个目标，我们将任务拆解成以下几个子任务：
- 1. 梳理CSE的API接口清单和元数据结构。
  2. 硬编码模式完成基于CSE和bolt协议的冒烟验证。
  3. 梳理DFA-RPC的registry模块的注册机制和方案。
  4. 梳理bolt传输需要的元数据信息和数据结构。
  5. 整合基于bolt协议的元数据结构与CSE元数据结构的对接场景。
  6.完成CSE的registry模块(DFA-RPC体系)。
-
- ## 华为CSE对应API梳理
- 华为CSE对应的API梳理参看： [[华为CSE对应API梳理]]
- ## DFA-RPC的registry模块的注册机制
- DFA-RPC的registry模块的注册机制参看： https://help.aliyun.com/document_detail/149925.html
- 对应的方法如下：
- public abstract boolean start();  //启动客户端 
  public abstract void register(ProviderConfig config);  //注册服务
  public abstract void unRegister(ProviderConfig config);  //取消注册，优雅关闭使用
  public abstract void batchUnRegister(List<ProviderConfig> configs); //批量取消注册
  public abstract List<ProviderGroup> subscribe(ConsumerConfig config); //订阅服务
  public abstract void unSubscribe(ConsumerConfig config); //取消订阅，优雅关闭使用
  public abstract void batchUnSubscribe(List<ConsumerConfig> configs); //批量取消订阅
-
- ## DFA-RPC对接CSE的接入点梳理
- 基于上面的分析，我们可知需要在DFA-RCP的registry的各方法中织入以下的方法来完成与CSE的对接。
- ### 启动客户端
- registry的方法名：start
- CSE需要调用的接口按顺序分别为：
- 1. 获取微服务引擎专享版用户Token
  2. 查询微服务唯一标识信息
  3. 查询指定微服务信息
  4. 创建微服务静态信息
  5. 修改微服务扩展属性信息
-
-
-
-
-
-
-
-
-