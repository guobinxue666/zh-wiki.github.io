---
title: Hexo 博客搭建
toc: true
date: 2020-01-01 00:00:01
tags: Hexo
---

# 安装 Hexo
------

```bash
sudo npm install --unsafe-perm --verbose -g hexo
```

# 安装nodejs

------

```bash
curl -sL https://deb.nodesource.com/setup_15.x | sudo bash -
sudo apt install -y nodejs
```



# 同步本地图片与网络图片

------

1. 安装插件

   ```bash
   npm install https://github.com/CodeFalling/hexo-asset-image --save
   ```

2. 配置Typora

   ![image-20201005210350281](Hexo%20%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/image-20201005210350281.png)

3. 编译

   有以下log说明配置成功

   <img src="Hexo%20%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/image-20201005165756877.png"  />

# Wikitten 主题关闭自动标号

------

**修改路径文件：/themes/Wikitten/layout/common/article.ejs**

![disable toc number](Hexo%20%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/image-20210104093922296.png)