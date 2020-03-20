---
title: vue项目在同一局域网移动端无法打开解决方法
date: 2020-03-20 20:12:47
tags: [vue,webpack]
---

当局域网内手机访问电脑本地webpack服务器被拒绝时，在webpack.config.js的

```
  devServer: {
    host: '0.0.0.0', // 增加这一行
    useLocalIp: true, // 增加这一行
    disableHostCheck: true, // 增加这一行
  }

```

然后通过cmd ipconfig 获取到本地ipv4地址 如： 192.168.95.21
再拼上端口号就行了
