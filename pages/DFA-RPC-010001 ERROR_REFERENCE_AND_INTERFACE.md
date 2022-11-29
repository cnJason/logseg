- #### 应用名称
	- DFA-RPC
#### 标签
	- 初始化
	- Registry
	- Provider
#### 异常码编码
	- DFA-RPC-010001： ERROR_REFERENCE_AND_INTERFACE
	- 对应的SOFA-RPC的code是：RPC-010000018 ERROR_REFERENCE_AND_INTERFACE
#### 问题概要
- 控制台输出如下场景的异常：
- ```
  DFA-RPC-010001: Provider 引用为 [SampleServiceImpl] .这不是 [com.shine.dfa.rpc.sample.service.SampleService]的实例, Provider config key 为:
  [com.shine.dfa.rpc.sample.service.SampleService:DEFAULT] ! 
  ```
-
#### 问题原因
	- 问题场景
		- 服务无法启动，并且出现上述错误日志(Exception)
	- 原因分析
		- 1. 服务端暴露的服务SampleServiceImpl不是一个实例。
		  2. 服务端暴露的服务SampleServiceImpl不是SampleService的实现类。
		  3. 服务端暴露的服务SampleServiceImpl的版本不是com.shine.dfa.rpc.sample.service.SampleService:DEFAULT。
#### 解决方案
	- 按照上面步骤排查对应的服务的异常。
#### 适配版本
- DFA-Boot 1.0.1.0 之后的版本可使用此异常码判断。
- DFA-Boot 1.0.0.0-1.0.0.06版本的错误码为：RPC-010000018 ERROR_REFERENCE_AND_INTERFACE
#### 备注
-