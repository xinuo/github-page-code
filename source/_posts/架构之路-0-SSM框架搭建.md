title: 架构之路(0)-SSM框架搭建
author: Cry Rain
tags:
  - Spring
  - 架构之路
categories:
  - 架构之路
date: 2017-02-04 11:26:00
---
# 架构之路(0)-SSM框架搭建

> SSM:Spring+SpringMVC+MyBatis  


> **环境配置**(仅我自己的配置,可以参考)  
> JDK1.7+   
> Eclipse neon  
> git客户端/Eclipse的egit插件     
> Maven/Eclipse的Maven插件
> Tomcat

## 安装必要组件
### 安装jdk1.7+
具体jdk的安装以及配置环境变量就不多说了.相信作为java开发者这是基本功夫.
### 安装Eclipse
安装Eclipse的步骤也不叙述.直接安装好新版本的Eclipse,会自带git以及Maven的配置.具体可以看下图.
![paste image](http://od5y2z5kt.bkt.clouddn.com/lotaqihqajwoio35w9z0lzphgs)  
## 导入github上的ssm框架项目.
在项目右键选取**Import**->**Import Projects from Git**  
![paste image](http://od5y2z5kt.bkt.clouddn.com/1uclkwphyzyl9np6p6cm3qysb8)  
假如没有新的git库的话,这里选择Clone Uri   
![paste image](http://od5y2z5kt.bkt.clouddn.com/dnfjiuriq5ercqi1bqgklsky7c)  
然后复制
[https://github.com/Javen205/SSM.git](https://github.com/Javen205/SSM.git)  填充到uri中  
![paste image](http://od5y2z5kt.bkt.clouddn.com/aeymwtgevwl3v02m1bear3jntl)  
选择分支branch  
![paste image](http://od5y2z5kt.bkt.clouddn.com/d52byoazb143lhfda9tujy7b86)  
选择你想放置的目录地址.  
![paste image](http://od5y2z5kt.bkt.clouddn.com/8hewek9s1ai44u34neq7o9zmtp)  
选择导入已有的Eclipse项目  
![paste image](http://od5y2z5kt.bkt.clouddn.com/yg6429jxfo2vcw6di3740c08n6)  
**Finish**!

然后导入进去后,会发现Maven会自动导入依赖包  
![paste image](http://od5y2z5kt.bkt.clouddn.com/b2585lc9jl5ygozpdkg57o0b69)  
假如Maven导入出错,可以设置使用新版本的Maven.
修改Eclipse配置
![paste image](http://od5y2z5kt.bkt.clouddn.com/3550lywyhy5cxrqs2o6j7ttg6w)
修改成Maven配置,确定.
![paste image](http://od5y2z5kt.bkt.clouddn.com/10s47l08pp8feqbwz484etmvds)
这样导入就完成了.

## 部署项目
### 修改jdbc.properties
把jdbc.properties中url地址修改成你自己的mysql路径,这样就可以运行了.

### 添加Tomcat Server
![paste image](http://od5y2z5kt.bkt.clouddn.com/0n0p44ycmaa69xuf1v85g47qj8)  
因为Eclipse是刚安装完,所以需要新建一个新的Tomcat服务器这里采用tomcat8.0

![paste image](http://od5y2z5kt.bkt.clouddn.com/8q6jt653ehqb8aroha1d5938to)  
选择本地的tomcat,完成.
最后在tomcat添加ssm项目
![paste image](http://od5y2z5kt.bkt.clouddn.com/bpgrwrzfqqgtjz0cg0u3lxo2wk) 
![paste image](http://od5y2z5kt.bkt.clouddn.com/dm6r9skaye653uvc4q576c4djp) 
Finish

### Trouble Shoot
经常部署项目适合会发现找不到类,此时需要将Maven的路径部署在项目路径上.对项目右键->Properties->Deployment Assembly->Add->Java Build Path Entrieds->Maven 依赖->添加到项目的路径上
![paste image](http://od5y2z5kt.bkt.clouddn.com/10efwe9uunty5v8u2g676g78op)
添加成功后启动项目.
![paste image](http://od5y2z5kt.bkt.clouddn.com/c73gjl1p4vlom8dmo5hw2v2p5x)
启动成功后访问:
http://localhost:8080/maven02/
看到**Hello World!**则部署成功.
![paste image](http://od5y2z5kt.bkt.clouddn.com/m9xsrkexvn30xiary9w9hg57uy)