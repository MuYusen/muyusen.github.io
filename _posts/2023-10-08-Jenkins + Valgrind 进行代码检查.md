---
layout: mypost
title: Jenkins + Valgrind 进行代码检查
categories: [Jenkins,Valgrind]
---

[参考](https://plugins.jenkins.io/valgrind/)

## Jenkins 插件安装

Valgrind Plug-in (`插件有点老`)

## 新建`Pipeline`项目，编写`Pipeline script`

```bash
node('cm_22.04') {
   stage('Checkout') {
      git 'https://github.com/eXistence/ValgrindTest.git'
   }
   stage('Build') {
      sh 'sh build.sh'
   }
   stage('Valgrind') {
        runValgrind (
          childSilentAfterFork: true,
          excludePattern: '',
          generateSuppressions: true,
          ignoreExitCode: true,
          includePattern: 'bin/test_leak',
          outputDirectory: '',
          outputFileEnding: '.memcheck',
          programOptions: '',
          removeOldReports: true,
          suppressionFiles: '',
          tool: [$class: 'ValgrindToolMemcheck',
            leakCheckLevel: 'full',
            showReachable: true,
            trackOrigins: true,
            undefinedValueErrors: true],
          traceChildren: true,
          valgrindExecutable: '',
          valgrindOptions: '',
          workingDirectory: ''
        )

        publishValgrind (
          failBuildOnInvalidReports: false,
          failBuildOnMissingReports: false,
          failThresholdDefinitelyLost: '',
          failThresholdInvalidReadWrite: '',
          failThresholdTotal: '',
          pattern: '*.memcheck',
          publishResultsForAbortedBuilds: false,
          publishResultsForFailedBuilds: false,
          sourceSubstitutionPaths: '',
          unstableThresholdDefinitelyLost: '',
          unstableThresholdInvalidReadWrite: '',
          unstableThresholdTotal: ''
        )
   }
}
```

+ `stage('Build'):编译程序`
+ `runValgrind() 运行Valgrind分析工具`，`publishValgrind()公布分析结果`
+ Valgrind 的Jenkins插件，当前只支持`Memcheck`和`Helgrind`两种工具

## pipeline中并行`Memcheck`和`Helgrind`

目的是想在一个Pipeline任务中并行分析`Memcheck`和`Helgrind`。

`结果显示有问题，不建议，最好还是放在不同的任务中`

```bash
node('cm_22.04') {
   stage('Checkout') {
      git 'https://github.com/eXistence/ValgrindTest.git'
   }
   stage('Build') {
      sh 'sh build.sh'
   }
   parallel Memcheck:{
       stage('Memcheck') {
            runValgrind (
              childSilentAfterFork: true,
              excludePattern: '',
              generateSuppressions: true,
              ignoreExitCode: true,
              includePattern: 'bin/test_leak',
              outputDirectory: '',
              outputFileEnding: '.memcheck',
              programOptions: '',
              removeOldReports: true,
              suppressionFiles: '',
              tool: [$class: 'ValgrindToolMemcheck',
                leakCheckLevel: 'full',
                showReachable: true,
                trackOrigins: true,
                undefinedValueErrors: true],
              traceChildren: true,
              valgrindExecutable: '',
              valgrindOptions: '',
              workingDirectory: ''
            )

            publishValgrind (
              failBuildOnInvalidReports: false,
              failBuildOnMissingReports: false,
              failThresholdDefinitelyLost: '',
              failThresholdInvalidReadWrite: '',
              failThresholdTotal: '',
              pattern: '*.memcheck',
              publishResultsForAbortedBuilds: false,
              publishResultsForFailedBuilds: false,
              sourceSubstitutionPaths: '',
              unstableThresholdDefinitelyLost: '',
              unstableThresholdInvalidReadWrite: '',
              unstableThresholdTotal: ''
            )
       }
    },Helgrind:{
       stage('Helgrind') {
            runValgrind (
              childSilentAfterFork: true,
              excludePattern: '',
              generateSuppressions: true,
              ignoreExitCode: true,
              includePattern: 'bin/test_leak',
              outputDirectory: '',
              outputFileEnding: '.helgrind',
              programOptions: '',
              removeOldReports: true,
              suppressionFiles: '',
              tool: [$class: 'ValgrindToolHelgrind',
                leakCheckLevel: 'full',
                showReachable: true,
                trackOrigins: true,
                undefinedValueErrors: true],
              traceChildren: true,
              valgrindExecutable: '',
              valgrindOptions: '',
              workingDirectory: ''
            )

            publishValgrind (
              failBuildOnInvalidReports: false,
              failBuildOnMissingReports: false,
              failThresholdDefinitelyLost: '',
              failThresholdInvalidReadWrite: '',
              failThresholdTotal: '',
              pattern: '*.helgrind',
              publishResultsForAbortedBuilds: false,
              publishResultsForFailedBuilds: false,
              sourceSubstitutionPaths: '',
              unstableThresholdDefinitelyLost: '',
              unstableThresholdInvalidReadWrite: '',
              unstableThresholdTotal: ''
            )
       }
    }
}

```
