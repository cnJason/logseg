title:: DFA-Boot 1.0.0.6

-
- TODO [[DFA-Boot  应用配置参数自检方案]]
  :LOGBOOK:
  CLOCK: [2022-07-28 Thu 16:25:21]
  :END:
- TODO  [[DFA-Boot  各模块开关梳理]]
  :LOGBOOK:
  CLOCK: [2022-07-28 Thu 16:25:33]
  CLOCK: [2022-07-28 Thu 16:34:04]--[2022-07-28 Thu 16:34:05] =>  00:00:01
  CLOCK: [2022-07-28 Thu 16:34:05]--[2022-07-28 Thu 16:34:06] =>  00:00:01
  CLOCK: [2022-07-28 Thu 16:34:07]--[2022-07-28 Thu 16:34:08] =>  00:00:01
  CLOCK: [2022-07-28 Thu 16:34:11]--[2022-07-28 Thu 16:34:11] =>  00:00:00
  CLOCK: [2022-07-28 Thu 16:34:12]--[2022-07-28 Thu 16:34:13] =>  00:00:01
  CLOCK: [2022-07-28 Thu 16:34:14]--[2022-07-28 Thu 16:34:15] =>  00:00:01
  :END:
- TODO [[DFA-Boot Actuator方案设计]]
- TODO [[基于Lite模式开发手册]]
- TODO 基于注册中心的开发手册
- TODO 重构jdbc-dfa-boot-starter
- TODO 支持多数据库配置（基于CRC）
- TODO DFA-Trace剥离
- TODO DFA组件版本自检方案设计
- TODO DFA-Boot启动参数添加关键信息校验
- TODO  DFABoot基于华为注册中心开发 -20220728001(高优先级) ok
- ## 1.0.0.6 
  [版本升级]
- [更新] rpc-dfa-boot    依赖为 v1.0.0.6
   [更新] rpc-dfa-all    依赖为 v5.8.5
   [更新] shine-soa-dfa-boot 依赖升级至 v1.0.0.6
   [更新] runtime-dfa-boot 依赖升级至 v1.0.0.6
   [更新] jdbc-dfa-boot 依赖升级至 v1.0.0.6
   [新增] dfa-registry-hwsc-plugin 依赖为v1.0.0.0
  
  [功能说明]
- [新增] Boot-20220728001 dfa-registry-hwsc-plugin提供注册至华为CSE的能力.
- [新增] Boot-20220728002 DFA-Boot Actuator设计