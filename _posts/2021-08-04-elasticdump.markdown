---
title: elasticdump安装
author: not_you
date: 2021-08-20 00:00:00 +0800
categories: ["日常学习","日常"]
tags: [日常,日常]
---

Elasticseach目前作为查询搜索平台，一些导入导出不是很方便，可以借助elasticdump

以ubuntu为例

```shell
apt update
apt upgrade
apt install nodejs npm
npm install elasticdump -g
```

可是最后一步会报这种错，很难受

![截图]({{site.url}}/assets/img/2020_08_20/1.png)

```shell
npm uninstall -g angular-cli
npm cache clear --force
```



**参考**

[解决报错](https://github.com/apigee/apigeetool-node/issues/190)

