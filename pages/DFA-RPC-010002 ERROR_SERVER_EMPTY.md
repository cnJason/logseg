- #### 应用名称
	- DFA-RPC
#### 标签
	- 初始化
	- Registry
	- Provider
#### 异常码编码
	- DFA-RPC-010002： ERROR_SERVER_EMPTY
	- 对应的SOFA-RPC的code是：RPC-010000019 ERROR_SERVER_EMPTY
#### 问题概要
- 控制台输出如下场景的异常：
- ```
  DFA-RPC-010002:  key 为 [com.shine.dfa.rpc.sample.service.SampleService:DEFAULT] 的 Provider config 没有指定 server 的值! 
  ```
-
#### 问题原因
	- 问题场景
		- 服务无法启动，并且出现上述错误日志(Exception)
	- 原因分析
		- 1. shine.dfa.rpc.registry.address是否配置。
#### 解决方案
	- 如果shine.dfa.rpc.registry.address未配置，则添加配置，配置信息参看备注的地址。
#### 适配版本
- DFA-Boot 1.0.1.0 之后的版本可使用此异常码判断。
- DFA-Boot 1.0.0.0-1.0.0.06版本的错误码为：RPC-010000018 ERROR_REFERENCE_AND_INTERFACE
#### 备注
- http://10.168.106.130:5021/#/views/dfa/dfa-boot/dfa-rpc/overview?id=%e9%85%8d%e7%bd%ae%e9%a1%b9