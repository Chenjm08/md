
---
title: docker搭建hexo博客
date: 2020-10-06 13:01:54
categories: 博客
---

# docker搭建hexo博客

## 安装hexo和初始化blog

```sh
# 安装hexo-cli
npm install hexo-cli -g

# 初始化blog
hexo init hexo-blog

# 进入目录
cd hexo-blog

# 安装依赖库
npm install
```

到这里，`hexo`博客已经安装完成，启动试试是否正常。

```sh
# 启动hexo blog
hexo server
```

## 目录结构

```
hexo-blog
|-- source # 存放hexo的source文件夹下的内容
|     |-- _posts # 存放md文件
|     |-- _
|-- scaffolds # hexo 手脚架
|-- themes # 存放hexo主题
|-- db.json
|-- package.json 依赖库配置文件
|-- node_modules 依赖库
|-- _config.yml 站点配置文件


```


## docker 部署

在`hexo-blog`目录， 创建`Dockerfile`文件，并进行编辑:

```
# node环境镜像
From node:latest AS build-env

# 配置淘宝镜像源
RUN npm config set registry http://registry.npm.taobao.org/

# 安装hexo脚手架
RUN npm install hexo-cli -g

# 创建hexo-blog文件夹且设置成工作文件夹
RUN mkdir -p /usr/src/hexo-blog
WORKDIR /usr/src/hexo-blog


# 拷贝hexo所有内容到容器
COPY . .

# 开发80端口
EXPOSE 80

# 容器启动的时候执行的命令
ENTRYPOINT ["hexo","clean"]
ENTRYPOINT ["hexo","s"]
```


## 添加主题

下载主题

```sh
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

配置hexo的`_config.yaml`

```
theme: next
```

## 主题设定

选择Scheme

Scheme 是 NexT 提供的一种特性，借助于 Scheme，NexT 为你提供多种不同的外观。同时，几乎所有的配置都可以 在 Scheme 之间共用。目前 NexT 支持三种 Scheme，他们是：

* Muse - 默认 Scheme，这是 NexT 最初的版本，黑白主调，大量留白
* Mist - Muse 的紧凑版本，整洁有序的单栏外观
* Pisces - 双栏 Scheme，小家碧玉似的清新

```
#scheme: Muse
#scheme: Mist
scheme: Pisces
```


## 设置语言

编辑`hexo-blog`目录下的`_config.yaml`，将 language 设置成你所需要的语言。建议明确设置你所需要的语言，例如选用简体中文，配置如下：

```
language: zh-Hans
```

目前 NexT 支持的语言如以下表格所示：

语言	| 代码 	|  设定示例
------- | --------|---------
English | en	     | language: en
简体中文  | zh-Hans | language: zh-Hans
Français | fr-FR	  | language: fr-FR
Português	| pt	  | language: pt or language: pt-BR
繁體中文   | zh-hk 或者 zh-tw | language: zh-hk
Русский язык	| ru	| language: ru
Deutsch	 | de	   | language: de
日本語	    | ja	   | language: ja
Indonesian |	id	   | language: id
Korean	     | ko	   | language: ko


## 设置菜单

菜单配置包括三个部分，第一是菜单项（名称和链接），第二是菜单项的显示文本，第三是菜单项对应的图标。 NexT 使用的是 Font Awesome 提供的图标， Font Awesome 提供了 600+ 的图标，可以满足绝大的多数的场景，同时无须担心在 Retina 屏幕下 图标模糊的问题。

设定菜单内容，对应的字段是 menu。 菜单内容的设置格式是：item name: link。其中 item name 是一个名称，这个名称并不直接显示在页面上，她将用于匹配图标以及翻译。

```
menu:
  home: /
  archives: /archives
  #about: /about
  #categories: /categories
  tags: /tags
  #commonweal: /404.html
```


NexT 默认的菜单项有（标注的项表示需要手动创建这个页面）：



主题文件夹本身也有一个_config.yaml，按需配置即可:

```
    menu:
    Home: /
    Archives: archives
    cate: cate
    page:   // 只有 archives是自动生成的,其他需要手动配置
        归档: archives
        专题: categories
        友链: friendlinks
        关于: life
        搜索: search

    sidePic: https://tva1.sinaimg.cn/large/007S8ZIlgy1ggdmi6yapmj30dw0zk0wm.jpg // 主题图
    avatar: https://tva1.sinaimg.cn/large/007S8ZIlgy1ggdmr1k3k0j305k05k74j.jpg // 头像

    excerpt_link: 继续阅读
    excerpt: 200 // 提取字数

    social:
        twitter: true
        github: true
        weibo: false
        yuque: true
        rss: true

    twitter:
        username:  用户名

    github:
        username:  用户名

    weibo:
        username: 用户名

    yuque:
        username:  用户名

    # https://www.leancloud.cn/
    valine: 如果需要评论,则需要取leancloud注册并获取以下key
        appId:
        appKey:

    # https://web.umeng.com/
    umeng: 友盟项目id
```

创建几个关键页面

搜索页

```sh
hexo new page search // 对应生成source/search/index.md
```

然后如下设置：

```sh
---
title: 站内搜索
date: 2020-07-24 14:14:06
hideDate: true
comments: false
layout: "search"
---
```

专题页

```sh
hexo new page categories // 对应生成source/categories/index.md
```

然后如下设置:

```sh
---
title: 专题阅读
date: 2020-07-05 15:59:28
layout: "cate"
comments: false
---
```

搜索页

```sh
hexo new page search // 对应生成source/search/index.md
```

然后如下设置:

```
---
title: 站内搜索
date: 2020-07-24 14:14:06
hideDate: true
comments: false
layout: "search"
---
```

404页面:

```sh
hexo new page 404 // 对应生成source/404/index.md
```

然后如下设置:

```
---
title: 404 - 没有目标页面
date: 2020-07-05 15:11:10
comments: false
permalink: /404  // 注意这里
---
<style>.meta{display:none!important;}</style>
```