# ⌛️ Gocron

## 项目简介

> 该项目fork于[gaowei-space/gocron](https://github.com/gaowei-space/gocron), 源项目为[ouqiang/gocron](https://github.com/ouqiang/gocron).
- 更新点:
  - 更新部分样式;
  - 针对mac m1芯片的架构(arm64)与常用的操作系统架构(arm64)不一致, 更新了makefile文件;


## 开发
1. 安装Go1.9+, Node.js, Yarn
2. 安装前端依赖: `make install-vue`
3. 启动gocron, gocron-node: `make run`
4. 启动node server: `make run-vue`, 访问地址 http://localhost:8080
5. 访问 http://localhost:8080, API请求会转发给gocron

## 编译/打包
1. 编译: `make`
2. 编译并运行: `make run`
3. 打包: `make package`  [生成当前系统的压缩包]
4. 打包(linux系统): `make package-linux`
5. 打包(windows系统): `make package-wins`
6. 打包(macos系统[非m1]): `make package-mac`
7. 打包(macos m1系统): `make package-mac_m1`

## 配置文件
gocron系统的配置信息, 包含数据库配置及其他信息. 可预先准备好配置文件, 也可以部署好之后在页面上设置.
- 预先准备方法:
  当前路径(开发根目录/解压后可执行文件的路径)新建conf目录, conf目录下创建app.ini文件. 文件内容及注释如下:
```
[default]
db.engine         = mysql           # 存储定时任务的数据库类型
db.host           = 127.0.0.1       # host
db.port           = 3306            # 端口
db.user           = root            # 用户名
db.password       = xxx             # 密码
db.database       = xxx             # 库名
db.prefix         = cron_           # 数据表前缀
db.charset        = utf8            # charset
db.max.idle.conns = 5               # 最大连接数
db.max.open.conns = 100             # 最大打开连接数
allow_ips         =                 # 允许的ip列表
app.name          = 定时任务管理系统   # 应用名称 
api.key           = 
api.secret        = 
enable_tls        = false
concurrency.queue = 500
auth_secret       = xxx
ca_file           = 
cert_file         = 
key_file          = 
```

## 常用命令
- gocron
  - -v 查看版本
- gocron web
  - --host 默认0.0.0.0
  - -p 端口, 指定端口, 默认5920
  - -e 指定运行环境, dev|test|prod, dev模式下可查看更多日志信息, 默认prod
  - -h 查看帮助
- gocron-node
  - -allow-root *nix平台允许以root用户运行
  - -s ip:port 监听地址
  - -enable-tls 开启TLS
  - -ca-file   CA证书文件  
  - -cert-file 证书文件
  - -key-file 私钥文件
  - -h 查看帮助
  - -v 查看版本


## 备注
关于arm64架构和amd64架构的区别:
- `amd64`: 是x86架构的CPU, 常见的操作系统如`Windows、Linux、Macos(除最新的mac m1芯片)`都提供amd64版本.
- `arm64`: 是ARM架构的CPU. 常见的操作系统如`Android,IOS`,以及嵌入式系统如树莓派等,通常都采用arm架构. `苹果最新的M1芯片电脑`,也是用arm64架构.
因此在编译时要注意架构的选择.