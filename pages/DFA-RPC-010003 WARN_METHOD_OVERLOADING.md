- #### 应用名称
	- DFA-RPC
#### 标签
	- 初始化
	- Registry
	- Provider
#### 异常码编码
	- DFA-RPC-010003： WARN_METHOD_OVERLOADING
	-
#### 问题概要
- 控制台输出如下场景的异常：
- ```
  WARN DFA-RPC-010003:  Method with same name "sampleService.call" exists ! The usage of overloading method in rpc is deprecated.
  ```
-
#### 问题原因
	- 问题场景
		- 将应用设置成WARN级别后，日志出现以上错误码和错误信息。
	- 原因分析
		- 1. RPC调用的场景下对于重载方法不友好，不建议使用。
#### 解决方案
	- 修改服务提供信息。避免使用重载方法。
#### 适配版本
- DFA-Boot 1.0.1.0 之后的版本可使用此异常码判断。
#### 备注
-