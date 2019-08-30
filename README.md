# 业务系统开发框架简介
- 框架基于SpringCloud开源分布式微服务框架构建，在SpringCloud的基础上我们定义了一系列标准和规范，并扩展开发了一系列的组件简化开发人员的开发工作，让开发人员更多关注编写业务逻辑相关的程序。
- 开发框架提供了快速构建分布式系统中一些常见模式的服务（例如配置管理，服务发现，断路器，智能路由，微代理，控制总线）,开发人员可以快速地使用这些服务。 

# 特性
开发框架专注于提供良好的开箱即用经验的典型用例和可扩展性机制覆盖。
- 分布式微服务框架
- 服务注册和发现
- 客户端负载均衡
- 路由
- 服务限流降级
- 服务熔断
- 服务线性扩展
- 组件库丰富多样
- 前后端分离
- 支持容器化快速部署
- 服务全链路跟踪
- 服务全方位监控

# 架构
架构图如下：
> ![image](http://git.jsptz.com/cloud/pictures/raw/master/arch6/arch6%E6%95%B4%E4%BD%93%E6%9E%B6%E6%9E%84.png "架构图")

**基础版主要由如下几个部分组成：**

服务名称| 说明
---|---
Consul | 服务注册中心，主要负责服务注册信息管理，提供服务Ip、端口、健康状态等信息
Apollo | 服务配置中心，集中管理各个微服务的配置文件
ateway Server | 服务路由中心，对外提供服务的统一入口，负责服务的转发、权限token发放、服务鉴权、服务限流、服务监控等功能
Misc Server | 微服务样例代码。提供一系列样例代码，例如用户管理、菜单管理、权限管理、数据字典管理、机构管理等。是主要业务代码书写的地方，并提供代码生成工具，生成mybatis的dao层代码以及mapper文件
 

# 目录详解 
|目录|说明|备注|
|----|----|----|
|database| 数据库脚本 | 目前提供mysql版| 
|misc| 杂项，放置一些公共工具、脚本、第三方组件等 |  
|monolith| 单体应用样例程序 | 不依赖spring cloud组件，可单独启动，具有Web能力，适合小系统|
|server| 微服务样例程序 | 基础组件目录，包含公共服务程序，例如网关程序、监控程序；包含公共功能微服务misc-server；包含样例程序showcase|
|software| nginx windows版 | 方便开发前端(避免跨域访问等)|
|web| 纯前端样例工程 |使用nginx进行部署  |

# 快速搭建
## 1.基础环境
 环境        | 版本      |备注
------------|-----------|---
JDK| 1.8+ | 
Maven| 3.0+|
Eclipse|OXYGEN.1+|J2EE最新版最好
MySql|5.7+|
RabbitMQ|最新版即可|
Redis|3.0+|
浏览器|IE11+，Chrome，Firefox|建议使用chrome，不支持IE8,9,10

## 2.获取代码
* 建议从http://git.jsptz.com/jsptz/framework-poc/releases 直接下载正式版本包用于项目开发。
* 可以从http://git.jsptz.com/jsptz/framework-poc.git 派生一个自己的版本库来尝试开发版，有代码愿意贡献时，可以发送代码合并请求。

## 3.初始化数据库
* 确认Redis、Apollo、RabbitMQ已经搭建并启动(这些可以一个项目组统一使用一个，也可也暂时使用金丰和地址演示)
> **确认本机或者环境已安装好了mysql数据库，本样例程序默认情况下认为数据库地址为localhost，端口为3306，schema为platform_devdb，用户名为root，密码为admin**。

- 进入\database\mysql目录，用文本编辑器打开rebuildDB.cmd进行编辑。


```
  set USER=root  
  set PASSWORD=admin  
  set SERVER_IP=localhost  
  set SCHEMA=platform_devdb
```
 

- 执行初始化数据库脚本，直接双击执行rebuildDB.cmd即可。
![image](http://git.jsptz.com/cloud/pictures/raw/master/rebuild.png)

## 4.导入项目
进入根目录下，按需要选择目录下的
    
    echo-server（简单web示例）
    robot-server（简单web+db示例）
    misc-server（典型web示例）
    gateway-server（网关）
    
这几个项目，通过Import Existing Maven Projects 方式导入Eclipse。 

## 5.启动项目
按照如下步骤启动项目。（以windows本地环境为例）
1. 搭建consul注册中心
   
    consul下载地址：https://www.consul.io/downloads.html, 启动命令 
    ```
    consul.exe agent -dev
    ```

    consul测试地址：http://localhost:8500/ui/dc1/services 。浏览器输入地址：
    
    ![consul](http://git.jsptz.com/cloud/pictures/raw/master/arch6/consul.png)
       
   
2. 启动misc-server。你可以选择在eclipse中或者本地环境启动。

      **在eclipse启动的话。**

      1）在Eclipse的VM arguments中添加如下参数(连接apollo配置中心)，以进行系统开发-Dapollo.meta=http://106.14.132.120:8001 (原值-Dapollo.meta=http://apollo.dev.jsptz.com)

      2）找到ins.platform.eureka.Application.java类，右键->Run As ->Java Application;

      **在本地文件夹启动的话**

      1）找到项目的根目录，双击startJsptz-DEV.cmd，项目启动。

3. 启动gateway-server。
        
      参考启动misc-server的方式启动gateway-server。
      
4. 配置文件修改
   
      项目启动后，配置文件会缓存在**c：/opt**目录下，如需修改配置文件信息，可在本地进行修改。
      
      ![apollo](http://git.jsptz.com/cloud/pictures/raw/master/arch6/apollo.png)


## 6.启动前端
前端是纯前端项目，使用html5+jquery+css3构建，使用jsviews作为前端数据双向绑定框架，并结合一系列的开源前端框架（例如bootstrap、requirejs、ajax、jquery-validate、z-tree等）组合而成。启动前端只需要一个nginx，因为我们已经对nginx的配置进行了定制化，详见software/nginx/conf/nginx.conf文件。
1. 在本机双击software/nginx/nginx.exe
2. 浏览器中输入地址：http://localhost:9082显示如下：
![image](http://git.jsptz.com/cloud/pictures/raw/master/login.png)
3. 输入用户名admin密码sinosoft点击登录，显示如下：![image](http://git.jsptz.com/cloud/pictures/raw/master/index.png)
4.项目至此启动完毕，具体开发指导请参考相关文档。 