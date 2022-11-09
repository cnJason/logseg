title:: 20220928001 DFA- Boot 添加reflections小于0.9.11版本检查，并且存在黑名单中的jar包不允许启动

## 需求说明

DFA-Boot 添加reflections小于0.9.11版本检查，并且存在黑名单中的jar包不允许启动。
## 需求编号

20220928001
## 开发完成时间

2022-09-28
## 修改模块和版本

runtime-dfa-boot v1.0.1.0
## 变更内容

DFA-Boot 添加reflections小于0.9.11版本检查，并且存在黑名单中的jar包不允许启动。
## 在线文档是否调整

否
- ## SVN记录
  
  runtime-dfa-boot 代码修改
  
  ```
  Index: JAVA/runtime-dfa-boot/src/main/java/com/shine/dfa/runtime/dependency/loader/DfaDependencyInfoLoader.java
  IDEA additional info:
  Subsystem: com.intellij.openapi.diff.impl.patch.CharsetEP
  <+>UTF-8
  ===================================================================
  diff --git a/JAVA/runtime-dfa-boot/src/main/java/com/shine/dfa/runtime/dependency/loader/DfaDependencyInfoLoader.java b/JAVA/runtime-dfa-boot/src/main/java/com/shine/dfa/runtime/dependency/loader/DfaDependencyInfoLoader.java
  --- a/JAVA/runtime-dfa-boot/src/main/java/com/shine/dfa/runtime/dependency/loader/DfaDependencyInfoLoader.java	(revision 12199)
  +++ b/JAVA/runtime-dfa-boot/src/main/java/com/shine/dfa/runtime/dependency/loader/DfaDependencyInfoLoader.java	(revision 12200)
  @@ -1,5 +1,6 @@
   package com.shine.dfa.runtime.dependency.loader;
  
  +import com.alibaba.nacos.shaded.com.google.common.collect.Lists;
   import com.fasterxml.jackson.databind.ObjectMapper;
   import com.fasterxml.jackson.databind.type.TypeFactory;
   import com.google.common.cache.Cache;
  @@ -69,7 +70,7 @@
     @Override
     public void postProcessEnvironment(ConfigurableEnvironment environment, SpringApplication application) {
         String printDependency = environment.getProperty("shine.dfa.debugMode.print.dependency");
  -
  +        List<Dependency> errorDependencies = Lists.newArrayList();
         try {
             // 默认是开，只有shine.dfa.debugMode.print.dependency是false的时候不输出.
             if(!StringUtils.hasText(printDependency) || !printDependency.equalsIgnoreCase("false")){
  @@ -91,26 +92,38 @@
                 }
             }
             for (Dependency blacklistDependency : blacklistDependencies) {
  +
  +                // 全匹配.
                 if(dependencies.contains(blacklistDependency)){
  -                    log.warn("项目依赖jar包在黑名单中:"+ blacklistDependency.toString());
  +                    log.error("项目依赖jar包在黑名单中:"+ blacklistDependency.toString());
  +                    errorDependencies.add(blacklistDependency);
                 }
  +
                 String groupId = blacklistDependency.getGroupId();
                 String artifactId = blacklistDependency.getArtifactId();
                 String version = blacklistDependency.getVersion();
  +
                 for (Dependency dependency : dependencies) {
                     String dependencyArtifactId = dependency.getArtifactId();
                     String dependencyGroupId = dependency.getGroupId();
  +                    // 版本不存在.
                     if(!StringUtils.hasText(version)){
                         if(dependencyArtifactId.equalsIgnoreCase(artifactId) && dependencyGroupId.equalsIgnoreCase(groupId)){
  -                            log.warn("项目依赖jar包在黑名单中:" + blacklistDependency);
  +                            log.error("项目依赖jar包在黑名单中:" + blacklistDependency);
  +                            errorDependencies.add(blacklistDependency);
                         }
                     }
  +                    // 版本和artifact不存在.
                     if(!StringUtils.hasText(artifactId)){
                         if(dependencyGroupId.equalsIgnoreCase(groupId)){
  -                            log.warn("项目依赖jar包的groupId在黑名单中:"+ blacklistDependency);
  +                            log.error("项目依赖jar包的groupId在黑名单中:"+ blacklistDependency);
  +                            errorDependencies.add(blacklistDependency);
                         }
                     }
                 }
  +            }
  +            if(errorDependencies.size() > 0){
  +                throw new RuntimeException("DFA-Boot Dependency Check Contains Exception, Please Look the ERROR log above.");
             }
         }
     }
  Index: JAVA/runtime-dfa-boot/src/main/resources/dependency-blacklist.json
  IDEA additional info:
  Subsystem: com.intellij.openapi.diff.impl.patch.CharsetEP
  <+>UTF-8
  ===================================================================
  diff --git a/JAVA/runtime-dfa-boot/src/main/resources/dependency-blacklist.json b/JAVA/runtime-dfa-boot/src/main/resources/dependency-blacklist.json
  --- a/JAVA/runtime-dfa-boot/src/main/resources/dependency-blacklist.json	(revision 12199)
  +++ b/JAVA/runtime-dfa-boot/src/main/resources/dependency-blacklist.json	(revision 12200)
  @@ -1,12 +1,42 @@
   [
   {
  -    "groupId":"com.alibaba.nacos",
  -    "artifactId":"nacos-api",
  -    "version":"1.4.0"
  +    "groupId": "com.alibaba.nacos",
  +    "artifactId": "nacos-api",
  +    "version": "1.4.0"
   },
   {
  -    "groupId":"com.itextpdf",
  -    "artifactId":"itext-asian",
  -    "version":""
  +    "groupId": "com.itextpdf",
  +    "artifactId": "itext-asian",
  +    "version": ""
  +  },
  +  {
  +    "groupId": "org.reflections",
  +    "artifactId": "reflections",
  +    "version": "0.9.10"
  +  },
  +  {
  +    "groupId": "org.reflections",
  +    "artifactId": "reflections",
  +    "version": "0.9.9"
  +  },
  +  {
  +    "groupId": "org.reflections",
  +    "artifactId": "reflections",
  +    "version": "0.9.8"
  +  },
  +  {
  +    "groupId": "org.reflections",
  +    "artifactId": "reflections",
  +    "version": "0.9.7"
  +  },
  +  {
  +    "groupId": "org.reflections",
  +    "artifactId": "reflections",
  +    "version": "0.9.6"
  +  },
  +  {
  +    "groupId": "org.reflections",
  +    "artifactId": "reflections",
  +    "version": "0.9.5"
   }
   ]
  \ No newline at end of file
  ```