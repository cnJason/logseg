- #### 应用名称
	- DFA-RPC
#### 标签
	- registry
#### 异常码编码
	- DFA-RPC-030014： ``ERROR_LOCAL_FILE_NULL``
	-
#### 问题概要
- 控制台输出如下场景的异常：
- ```
  ERROR DFA-RPC-030014:  LocalRegistry 文件为 null
  ```
-
#### 问题原因
	- 场景描述：
	  1. shine.dfa.rpc.registry.address 配置的文件是否存在.
	  2. shine.dfa.rpc.registry.address 配置的文件是否可读写
#### 解决方案
	- 正确处理shine.dfa.rpc.registry.address配置的文件.
#### 适配版本
	- DFA-Boot 1.0.1.0 之后的版本可使用此异常码判断。
#### 备注
	- 无