# Jenkins

## 安装

在基于Debian的发行版（如Ubuntu）上，您可通过`apt`安装Jenkins

在an apt repository可获得最新版本，较老但稳定的LTS版本在this apt repository这里可获得

```
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
```

安装这个软件包将会：

+ 将Jenkins设置为启动时启动的守护进程。查看`/etc/init.d/jenkins`获取更多细节
+ 创建一个'jenkins'用户来运行此服务
+ 直接将控制台日志输出到文件`/var/log/jenkins/jenkins.log`。如果您正在解决Jenkins问题，请检查此文件
+ /etc/default/jenkins`为启动填充配置参数，例如JENKINS_HOME
+ 将Jenkins设置为在端口8080上进行监听。使用浏览器访问此端口以开始配置

如果你的`/etc/init.d/jenkins`文件无法启动Jenkins，编辑`/etc/default/jenkins`， 修改 ----HTTP_PORT=8080----`为----HTTP_PORT=8081----` 在这里，“8081”也可被换为其他可用端口。

