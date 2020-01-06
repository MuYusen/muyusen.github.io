---
layout: mypost
title: Jenkins+SonarQube进行代码检查
categories: [Jenkins,SonarQube]
---
## Jenkins+SonarQube进行代码检查

本文以Ubuntu操作系统为例介绍Sonar的安装配置，以及如何与Jenkins进行集成，通过pmd-cpd、checkstyle、findbugs等工具对代码进行持续审查。

SonarQube 是一个用于代码质量管理的开放平台。通过插件机制，Sonar 可以集成不同的测试工具，代码分析工具，以及持续集成工具。
与持续集成工具（例如 Hudson/Jenkins 等）不同，Sonar 并不是简单地把不同的代码检查工具结果（例如 FindBugs，PMD 等）直接显示在 Web 页面上，而是通过不同的插件对这些结果进行再加工处理，通过量化的方式度量代码质量的变化，从而可以方便地对不同规模和种类的工程进行代码质量管理。

Sonarqube分为两个部分：服务器端和客户端。其中服务器端为一个Web系统，用来展示扫描的结果；客户端是一个扫描工具，用来扫描源码，并上传到服务器端。

### SonarQube Server 安装配置

从https://www.sonarqube.org/downloads/ 下载最新的SonarQube工具，解压到相应位置，美其名曰${sonarqube}

1. 创建数据库——MYSQL

```
    CREATE DATABASE sonar CHARACTER SET utf8 COLLATE utf8_general_ci;
    CREATE USER 'sonar' IDENTIFIED BY 'sonar';
    GRANT ALL ON sonar.* TO 'sonar'@'%' IDENTIFIED BY 'sonar';
    GRANT ALL ON sonar.* TO 'sonar'@'localhost' IDENTIFIED BY 'sonar';
```

2. 修改${sonarqube}/conf/sonar.properties数据库配置
```
    sonar.jdbc.username=sonar
    sonar.jdbc.password=sonar
    sonar.jdbc.url=jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false
``` 
下载mysql数据库链接文件mysql-connector-java-5.1.41.jar，将其复制到${sonarqube}/extensions/jdbc-driver/mysql文件夹下。
    
3. 修改sonarqube服务端口
```    
    vi ${sonarqube}/conf/sonar.properties
    
    sonar.web.port=9000 #默认是9000,可以根据自己的需要进行修改
```
4. 插件安装

从 https://redirect.sonarsource.com/doc/plugin-library.html或http://www.sonarsource.com/plugins下载需要的插件，然后放到${sonarqube}/extensions/plugins目录下面，重启server就可以了。

    
5. 启动sonarqube服务
```    
    cd ${sonarqube}/bin/linux-x86-64
    ./sonar.sh
```    

这是在浏览器中打开：http://localhost:9000/,就打开web页，内置用户为admin，默认密码admin。第一次加载会花费一些时间，因为会往sonar数据库中建表并导入一下数据。
    

### SonarQube Scanner 安装配置

下载扫描分析工具：https://sonarsource.bintray.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-3.0.3.778-linux.zip，并解压到相应位置，美其名曰${SonarqubeScanner}

1. 修改配置
```    
    vi ${SonarqubeScanner}/conf/sonar-scanner.properties

    #----- Default SonarQube server
    sonar.host.url=http://localhost:9000

    #----- Default source code encoding
    sonar.sourceEncoding=UTF-8
```
    

### Jenkins的配置
1. 安装插件
```
    在“系统管理”——“管理插件”——“可选插件”中选择需要安装的插件。
    
    Sonar Gerrit Plugin
    Sonargraph Integration Jenkins Plugin
    Sonargraph Plugin
    SonarQube Scanner for Jenkins
```    
2. 配置SonarQube Server
    + 管理员身份登录到jenkins，“系统管理”——“系统设置”
    + 在“SonarQube Server”中，选择“Add SonarQube”，添加相应的配置
    ```    
        比如
        Name：localhost_sonar
        Server URL：http://localhost:9000/
        Server authentication toke： #Server中“My Account”，“Security”自己生成。
    ```

3. 配置SonarQube Scanner
    
    + 管理员身份登录到jenkins，“系统管理”——“Global Tool Configuration”
    + 在“SonarQube Scanner”中，选择“新增SonarQube Scanner”，配置扫描工具的安装位置。
    ```    
        比如：
        Name：localhost_sonar_scanner
        SONAR_RUNNER_HOME：${SonarqubeScanner}
        不勾选“自动安装”
    ```

4. 配置jenkinsjob
    + 新建Jenkins Job，在“增加构建步骤”中，选择“Execute SonarQube Scanner”.
    + 在“Analysis properties”中添加如下内容：
``` 
    sonar.projectKey=915c43ea5c1846120d94a4465ba10a6be52b1695 #server中project的key，用于将扫描结果上传到对应的项目中去，建project中的必填项。
    sonar.sources=${SonarqubeScanner}/src/test #需要扫描分析的代码的路径
```
    启动构建，就会自动扫描在${SonarqubeScanner}/src/test中的代码
        
    需要注意的是，这个代码路径最好在jenkins job的${WOORKSPACE}下面，不然扫描不出来结果。


### 参考文档

+ https://www.ibm.com/developerworks/cn/java/j-lo-sonar/
+ http://www.uml.org.cn/jchgj/201307251.asp

+ https://docs.sonarqube.org/display/SONAR/Installing+the+Server
+ https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner
+ https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner+for+Jenkins