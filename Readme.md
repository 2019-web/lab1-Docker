# 实验一 : Docker环境在云上服务器搭建

## 亚马逊云服务器的使用

在观看视频之前，要先进行“AWS在线研讨会2019”的注册，这里的注册比亚马逊云账号的注册要简单，提供有效的邮箱即可，不再赘述。

首先看两个视频教程演示:

- AWS_基础服务介绍及实操_-_计算、存储和访问权限管理

URL链接 : [https://amazonaws-china.com/cn/about-aws/events/webinar/#AWS_基础服务介绍及实操_-_计算、存储和访问权限管理](https://amazonaws-china.com/cn/about-aws/events/webinar/#AWS_基础服务介绍及实操_-_计算、存储和访问权限管理)

- AWS 基础服务介绍及实操 - 利用 Amazon VPC 服务搭建经典 Web 三层架构

URL链接 : [https://amazonaws-china.com/cn/about-aws/events/webinar/#AWS_基础服务介绍及实操_-_利用_Amazon_VPC_服务搭建经典_Web_三层架构](https://amazonaws-china.com/cn/about-aws/events/webinar/#AWS_基础服务介绍及实操_-_利用_Amazon_VPC_服务搭建经典_Web_三层架构)

## Docker的使用

### 准备 Docker 环境

下面演示在服务器的环境是 Ubuntu 18.04 下安装Docker，既然采用的是亚马逊云服务器，服务器在国外，可以使用Docker官方的文档来安装Docker。
[https://docs.docker.com/install/linux/docker-ce/ubuntu/](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

**注意 :** 安装的是 Docker CE 版本

```
docker run -idt --name demo -p 8001:8080 docker-demo-java-tomcat:latest  
```



如果使用 AWS 服务器，默认的安全组规则会拦截服务器的入站流量。为了能够正常访问，我们需要放开对 8080 端口的限制：

1. 进入 AWS 控制面板，点击左边的【安全组】

2. 点击下方的【入站】->【编辑】

3. 增加入站规则： HTTP 或 HTTPS 可以允许他人访问你服务器上的网址；自定义 TCP 端口 8080 ，允许来源 【任意位置】。

如果一切正常，就可以通过 服务器公网 IP:8080 来访问你的工程了。






