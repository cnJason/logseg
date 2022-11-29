-
#### 应用名称
	- DFA-RPC
#### 标签
	- registry
	- register
#### 异常码编码
	- DFA-RPC-030004： ERROR_REG_PROVIDER
	-
#### 问题概要
- 控制台输出如下场景的异常：
- ```
  ERROR DFA-RPC-030018:  [NacosRegistry]注册 provider 到 [{0}] 失败! 服务名:[{1}]。请检查CRC的http/grpc端口是否正常可用和CRC服务是否可用，且@DfaService服务编写是否正确
  ```
-
#### 问题原因
	- 场景描述：服务注册异常的情况下会出现上面的报错信息，目前收集到的错误场景可能为如下：
		- 1. 如果日志里面只有输出 Start publish RPC service[{0}]，没有输出Stop publish RPC service[{0}]，并且出现上面的报错场景。则需要排查与CRC的http/grpc端口是否正常可用。并且CRC服务是否可用。
		  2. 如果日志没出现Start publish RPC service[{0}]，则需要判断@DfaService注解是否使用正确。并且没有重载同名方法。此时会有其他异常。
#### 解决方案
	- 1. 如果是场景1的情况，需要检查CRC的http/grpc端口是否正常可用和CRC服务是否可用。
	  2. 如果是场景2的方法，确认@DfaService服务编写是否正确。
#### 适配版本
	- DFA-Boot 1.0.1.0 之后的版本可使用此异常码判断。
#### 备注
	- 无
-