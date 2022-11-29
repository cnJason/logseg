#### 应用名称
	- DFA-RPC
#### 标签
	- registry
	- subscribe
#### 异常码编码
	- DFA-RPC-030006： *ERROR_SUB_PROVIDER*
	-
#### 问题概要
- 控制台输出如下场景的异常：
- ```
  ERROR DFA-RPC-030006:  订阅 provider 到 [{0}] 失败!
  ```
-
#### 问题原因
	- 场景描述：服务注册异常的情况下会出现上面的报错信息，目前收集到的错误场景可能为如下：
		- 1.CRC服务端问题，关注gRPC端口，偏移量为server.port + 1000。
		- 2.客户端与CRC网络通讯问题：
		-
#### 解决方案
	- 检查服务注册中心连接。
	  运行以下命令以检查客户端和服务端与服务注册中心的连接情况：netstat -a |grep 10009，
	  10009 端口是服务注册中心的监听端口，客户端和服务端与 10009 端口建立长连接，向服务注册中心发布和订阅服务。
	  如果客户端或者服务端与 10009 端口的连接断开，则需要重启应用恢复，并进一步排查端口异常断开的原因。
#### 适配版本
	- DFA-Boot 1.0.1.0 之后的版本可使用此异常码判断。
#### 备注
	- ![图片](https://mmbiz.qpic.cn/mmbiz_jpg/CmnN7FKTMS6Sj7Ry6etzXVJkJuyFHGicViamia43UjmfZEh7XYOTC0Ejsg7nXFkic9AjP4f5NUiarMVwicqibrIkFaZ0w/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)
	- ![图片](https://mmbiz.qpic.cn/mmbiz_jpg/CmnN7FKTMS6Sj7Ry6etzXVJkJuyFHGicVej74QFKjxwI49ISiawnnatnNyUgG4TkMsqaQ5UiaECTcOibJurVA6pwDw/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)
	-
	- ![图片](https://mmbiz.qpic.cn/mmbiz_jpg/CmnN7FKTMS6Sj7Ry6etzXVJkJuyFHGicV0HnG4cqysrh6e1yJCUWGuqMIT49ibrswOaIOTMJhpduJVHiaOmnlzItw/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)
	-