- #### 应用名称
	- DFA-RPC
#### 标签
	- registry
#### 异常码编码
	- DFA-RPC-030018： ERROR_EMPTY_ADDRESS
	-
#### 问题概要
- 控制台输出如下场景的异常：
- ```
  ERROR DFA-RPC-030018:  [NacosRegistry]注册中心的地址为空.
  ```
-
#### 问题原因
	- 场景描述：
		- 1. 未设置``shine.dfa.rpc.registry.address={}``的时候会出现以上错误信息
		  2. ``shine.dfa.rpc.registry.address={}``的格式不正确。
#### 解决方案
	- 在配置文件中设置``shine.dfa.rpc.registry.address={}``信息，并且设置正确的格式。正确的格式例子如下：``shine.dfa.rpc.registry.address=dfacrc://shinecrc/shinecrc@[10.168.106.84:9079]/app-demo``
- #### 适配版本
	- DFA-Boot 1.0.1.0 之后的版本可使用此异常码判断。
#### 备注
	- 无
-