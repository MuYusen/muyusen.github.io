---
layout: mypost
title: Jenkins Job Builder
categories: [Jenkins]
---

[参考](https://jenkins-job-builder.readthedocs.io/en/latest/index.html)

Jenkins Job Builder(JJB)使用YAML和JSON文件对Jenkins Job进行简单的描述，并使用他们来配置Jenkins。你可以将Job描述以一种可读的文本格式保存在版本控制系统中，让修改和审核更容易。JJB有一个灵活的模板系统，可以很容易的创建很多类似的任务。

## 安装

```bash
    $pip install --user jenkins-job-builder
```

## 源代码和示例下载

```bash
    git clone https://opendev.org/jjb/jenkins-job-builder.git
```

## 配置文件

默认路径(按顺序)

+ `~/.config/jenkins_jobs/jenkins_jobs.ini`
+ `<script directory>/jenkins_jobs.ini`
+ `/etc/jenkins_jobs/jenkins_jobs.ini`

```bash
[job_builder]
ignore_cache=True
keep_descriptions=False
include_path=.:scripts:~/git/
recursive=False
exclude=.*:manual:./development
allow_duplicates=False
update=all

[jenkins]
user=jenkins
password=1234567890abcdef1234567890abcdef
url=https://jenkins.example.com
query_plugins_info=False
##### This is deprecated, use job_builder section instead
#ignore_cache=True

[plugin "hipchat"]
authtoken=dummy

[plugin "stash"]
username=user
password=pass
```

## 命令

```bash
usage: jenkins-jobs [-h] [--conf CONF] [-l LOG_LEVEL] [--ignore-cache] [--flush-cache] [--version] [--allow-empty-variables] [--server SECTION] [--user USER] [--password PASSWORD]
                    {delete,delete-all,get-plugins-info,list,test,update} ...

positional arguments:
  {delete,delete-all,get-plugins-info,list,test,update}
                        update, test, list or delete job
    delete-all          delete *ALL* jobs from Jenkins server, including those not managed by Jenkins Job Builder.
    get-plugins-info    get plugins info yaml by querying Jenkins server.
    list                List jobs

options:
  -h, --help            show this help message and exit
  --conf CONF           configuration file [JJB_CONF]
  -l LOG_LEVEL, --log_level LOG_LEVEL
                        log level (default: info) [JJB_LOG_LEVEL]
  --ignore-cache        ignore the cache and update the jobs anyhow (that will only flush the specified jobs cache)
  --flush-cache         flush all the cache entries before updating
  --version             show version
  --allow-empty-variables
                        Don't fail if any of the variables inside any string are not defined, replace with empty string instead.
  --server SECTION, -s SECTION
                        The Jenkins server ini section to use. Defaults to 'jenkins' [JJB_SECTION]
  --user USER, -u USER  The Jenkins user to use for authentication. This overrides the user specified in the configuration file. [JJB_USER]
  --password PASSWORD, -p PASSWORD
                        Password or API token to use for authenticating towards Jenkins. This overrides the password specified in the configuration file. [JJB_PASSWORD]

```

## 任务定义

JJB的任务定义可以被保存在任意数量的YAML和Json文件中，不论是以何种方式组织的。当在执行`jenkins-jobs`命令时，可以指定单个YAML文件的路径和目录。如果选择了一个目录，那么该目录中的所有.yaml/.yml或.json文件都将被读取，并且它们定义的所有Job都将被创建和更新。
