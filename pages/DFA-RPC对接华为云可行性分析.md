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
- 基于上面的三个目标，我们将
-
-
-
-