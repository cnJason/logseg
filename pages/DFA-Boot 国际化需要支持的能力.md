- ## 背景
- 需要梳理DFA-Boot中已经提供的能力中需要国际化的功能。
	- 1. 审计日志输出信息
	  2. 数据字典
	  3. 自定义错误信息(登录异常，认证异常)
	  4. 常用条件(condition和table_config)
	  5. 自动导出的(excel,pdf,dbf的内容)
	  6. log中的异常信息
- ## DFA-Boot待完成任务
- 基于上面需要国际化的功能，我们需要完成以下几个任务：
- 1. 审计日志支持i18n占位符。
  2. 数据字典BMS支持多语言配置(目前只需支持中文和英文)
  3.自定义错误信息(登录异常，认证异常) 支持i18n占位符
- 4.常用条件(condition和table_config)中的表达式支持i18n占位符
- 5. 自动导出的(excel,pdf,dbf的内容)支持i18n占位符。
  6.log中的异常信息支持i18n占位符
-
- ## 需要BMS提供以下支持
	- 提供获取已经设置的国际化key(根据项目名)，规则如下：
		- 字典KEY是以dict.开头，格式如：dict.{groupCode}.{dictCode}
		- 异常KEY是exception.开头，格式为exception.{exceptionName}
		- 业务异常是exception.BIZ.开头，格式为：exception.BIZ.{exceptionName}
		- 日志的key是以log.开头，格式如： log.{logName}
		- 表头和查询条件的key是以param. 开头，格式如：param.{paramName}
	- 提供设置国际化key的入口。
	- 提供国际化key的初始化脚本。
	-
-
-
-
-