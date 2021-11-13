---
layout: post

title: 将GitBook转成PDF或者EPUB格式电子书

date: 2018-09-01 17:40:00

categories: ["工具"]

tags: 

mathjax: true
---

> GitBook 是一个基于`Node.js`的命令行工具，可以使用`Markdown`来制作电子书，并利用`Git/Github`发布。



支持输出`静态站点`、`PDF`、`eBook`、`HTML网页`、`JSON`等格式。

安装GitBook需要Node环境，具体怎么安装Node这里就不多说了。

## 安装GitBook ##

```bash
npm install -g gitbook-cli
```

检查是否安装成功

```bash
gitbook -V
```

>
>
>如果出现错误提示：
>
>```bash
>You need to install "gitbook-cli" to have access to the gitbook command anywhere on your system.
>If you've installed this package globally, you need to uninstall it.
>>> Run "npm uninstall -g gitbook" then "npm install -g gitbook-cli"
>
>```
>
>就按照提示先卸载gitbook，再重新安装gitbook-cli

或者使用 yarn安装GitBook

```bash
yarn global add gitbook-cli
```



**本篇文章只是简单介绍怎么安装GitBook和导出PDF电子书，所以就不介绍怎么使用GitBook制作电子书。**

打开到gitbook 的目录下

#### 1、输出静态网页

```bash
$ gitbook serve .
Press CTRL+C to quit ...

Starting build ...
Successfuly built !

Starting server ...
Serving book on http://localhost:4000	
```



这时候就可以打开 [http://localhost:4000](http://localhost:4000/)：进行预览



同时在项目的目录中多了一个 `_book` 的文件夹，其中的文件就是生成的静态网页的内容。

#### 2、导出PDF

在项目的目录中执行

```bash
gitbook pdf .
```

项目目录下就会生成 `book.pdf`

#### 3、导出epub

在项目目录中执行

```bash
gitbook epub .	
```

项目目录下就会生成 `book.epub`

