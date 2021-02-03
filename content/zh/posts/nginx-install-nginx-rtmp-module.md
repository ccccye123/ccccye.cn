---
title: "《Nginx系列》安装Nginx+RTMP模块"
date: 2021-02-03T23:09:47+08:00
draft: false
---

# 前言
Nginx是一个高性能的HTTP和反向代理Web服务器，本文安装的是Openresty，它包含完整的Nginx并支持Lua脚本语言。
同时加入nginx-rtmp-module插件，使得nginx支持RTMP和HLS的视频推流。

# 安装环境
1. CentOS7（配置好国内源）
2. 安装git
3. 部署时的根目录为 /usr/local/src

# 安装步骤
## 安装OpenResty相关的工具依赖
```
yum install -y pcre pcre-devel gd-devel openssl openssl-devel zlib zlib-devel
```

## 下载 nginx-rtmp-module
```
git clone https://github.com/arut/nginx-rtmp-module.git
```

## 下载解压OpenResty
```
wget https://openresty.org/download/openresty-1.19.3.1.tar.gz
tar -zxvf openresty-1.19.3.1.tar.gz
cd openresty-1.19.3.1
```

## 配置OpenResty
```
./configure --add-module=../nginx-rtmp-module
```

## 编译安装OpenResty
```
make -j2 & make install  或者 gmake -j2 & gmake install
```
> -j2 参数表示用2个线程任务编译，速度更快

不出意外的话到这里OpenResty的安装就完成了，程序安装在  /usr/local/openresty