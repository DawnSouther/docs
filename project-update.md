# 更新项目依赖的HAP版本

1. 选择希望的版本号
   此地址[查看 HAP Release 版本](https://rdc.hand-china.com/gitlab/HAP/hap/tags)  
2. 更改 项目Parent目录中的 pom.xml 
    * `<hap.version>` `3.0.7-RELEASE` `</hap.version>`
    
    2.x 的版本号是2.x-Release,3.X的版本号是3.x-RELEASE,注意大小写区别 
3. 更新IDE中的maven依赖：
   * `eclipse` 右键点击 HbiParent 工程，`Maven` → `Update Project...` 
   * `IDEA` ：右键点击 HbiParent 工程，`Maven` → `Reimport`(等候下载)
4. 在Parent目录下执行`mvn clean install`
5. 执行初始化数据库的<a href="project-create.md#liquibase">liquibase 命令</a>

   初始化数据库表结构及基础数据

> 注意 
  * 如果使用IDEA,请把`overlays`下面的其他HAP版本删除
  * 如果项目中过有覆盖过HAP的xml配置文件，更新以后请注意比对有没有变化
  * 新建项目默认依赖的是SNAPSHOT版本的HAP，会保持最新代码，系统表等可能会有变化，建议实际开发过程使用RELEASE版。

 