# Maven 使用手册
## 开发环境
1. ```JDK 1.8```
2. ```Maven 3.2.5```

## 下载及安装
1. 下载 [jdk1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 并安装
2. 下载 [Maven 3.2.5](http://maven.apache.org/download.html) 并解压

## 配置环境变量
### 配置jdk环境变量
1. 将字符串“`;C:\Program Files\Java\jdk1.8\bin`”添加到系统变量“Path”的末尾
2. 用户变量新建一个classpath变量并赋值"`.`"
3. 在cmd控制台中通过`java -version`来测试java环境变量是否配置正确

### 配置maven环境变量
1. 添加 M2_HOME、M2、MAVEN_OPTS 到环境变量中,使用系统属性设置环境变量。
   * `M2_HOME=C:\Program Files\Apache Software Foundation\apache-maven-3.2.5`
   * `M2=%M2_HOME%\bin`
   * `MAVEN_OPTS=-Xms256m -Xmx512m`
2. 添加 M2 变量到系统“Path”变量中,添加字符串 “`;%M2%`” 到系统“Path”变量末尾
3. 在cmd控制台中通过`mvn --version`来测试mvn环境变量是否配置正确

## 常用命令
> | 命令 | 解释 |
> | -------- | -------- |
> | `mvn archetype:create` | 创建 Maven 项目 |
> | `mvn clean` | 清除目标目录中的生成结果 |
> | `mvn compile` | 编译源代码 |
> | `mvn test-compile` | 编译测试代码 |
> | `mvn test` | 运行应用程序中的单元测试 |
> | `mvn package` | 依据项目生成 jar,war 文件 |
> | `mvn install` | 在本地 Repository 中安装 jar,war |
> | `mvn eclipse:eclipse` |  生成 Eclipse 项目文件 |
> | `mvn site` | 生成项目相关信息的网站 |

## 创建maven项目
### 创建Maven的普通java项目 
   ``` mvn archetype:generate -DgroupId=com.hand.app -DartifactId=项目名 -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false ```

### 创建Maven的Web项目 
  1. 进入命令行输入以下内容创建webapp项目：
    * ``` mvn archetype:generate -DgroupId=com.hand.app -DartifactId=项目名 -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false ```
    * （补充：在该命令行后面加上`-X`可查看创建web项目的进度）
  2. 进入工程目录(cd 项目名)，使用以下命令将项目生产为eclipse项目`mvn eclipse:eclipse`

  > | 命令 | 解释 |
  > | -------- | -------- |
  > | `mvn archetype:generate` | 固定格式 |
  > | `-DgroupId` | 组织标识（包名） |
  > | `-DartifactId` | 项目名称 |
  > | `-DarchetypeArtifactId` | 指定ArchetypeId，maven-archetype-quickstart，创建一个Java Project；maven-archetype-webapp，创建一个Web Project |
  > | `-DinteractiveMode` | 是否使用交互模式 |
    
## mvn编译执行项目
### 普通项目
* main目录的class：在项目根目录下运行命令`mvn clean compile`进行项目编译
* test目录下class：在项目根目录下运行`mvn clean test`进行测试
* 编译完成后在项目根目录下生成了`target`文件夹，里面存放了`class`文件
* 使用`mvn exec:java -Dexec.mainClass="com.hand.app.App"`运行App这个mainClass

### web项目
* `mvn tomcat:run` 启动tomcat，根据给定的端口号，在浏览器上输入`localhost:端口号/项目名/index.jsp`运行项目

## POM.xml文件
`<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>`  //当前POM模型的版本<br/>
  `<groupId>SpringMVC</groupId>`  //项目或者组织的唯一标志<br/>
  `<artifactId>JspExam10105</artifactId>` //项目的通用名称<br/>
  `<packaging>war</packaging>` //打包机制<br/>
  `<version>0.0.1-SNAPSHOT</version>`  //项目版本<br/>
  `<name>JspExam10105 Maven Webapp</name>`  //用户描述项目的名称<br/>
  `<url>http://maven.apache.org</url>`  //只是写明开发团队的网站，无关紧要<br/>
  `<dependencies>
      <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>3.8.1</version>
        <scope>test</scope>`  //指定当前依赖范围<br/>
      `</dependency>  
  </dependencies>`  //依赖关系<br/>
  `<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>`  //项目字符为UTF-8<br/>
  `</properties>`  //为pom定义一些常量，在pom中的其它地方可以直接引用<br/>
  `<build>
    <finalName>SpringMVC</finalName>`  //指定去掉后缀的工程名字<br/>
    `<pluginManagement>
		<plugins>
			<plugin>
				<groupId>org.apache.tomcat.maven</groupId>
				<artifactId>tomcat8-maven-plugin</artifactId>
				<version>2.3-SNAPSHOT</version>
			</plugin>`  //配置maven插件<br/>
		`</plugins>
    </pluginManagement>`  //用于管理plugin<br/>
	`<resources>  
		<resource>  
			<directory>src/main/java</directory>`  //用于管理plugin<br/>
			`<includes>  
				<include>**/*.properties</include>`  //指定包含文件的patterns,符合样式并且在directory目录下的文件将会包含进project的资源文件<br/>
				`<include>**/*.xml</include>  
			</includes>  
			<filtering>false</filtering>`  //指定是否将filter文件(即上面说的filters里定义的```*.property```文件)的变量值在这个resource文件有效<br/>
		`</resource>  
		<resource>  
			<directory>src/main/resources</directory>  
			<includes>  
				<include>**/*.properties</include>  
				<include>**/*.xml</include>  
			</includes>  
			<filtering>false</filtering>  
		</resource> 
	</resources>`  //描述工程中资源的位置<br/>
   `</build>
 </project>`

 
  
  

