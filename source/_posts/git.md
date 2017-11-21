---
title: git
date: 2017-11-21 12:35:17
tags: 1 2 3
categories: 技术
---

 新的工程提交到新的git工程中去
<!-- more -->

### 第一步 添加 README.md 文件和 .gitignore文件
执行以下命令自动生成对应的空文件，可根据情况编写自己的 README.md 文件。
.gitignore 的作用是告诉Git哪些文件不需要添加到版本管理中，可参考GitHub提供的非常全的 gitignore模板 ，按需复制粘贴到刚创建的 .gitignore 文件中，非常方便。
``` bash
touch README.md;
touch .gitignore;
```
这一步可以跳过。

### 第二步 建立Git仓库
在当前项目目录中生成一个本地的Git仓库。会生成一个名字为.git的隐藏目录。
``` bash
git init
```

### 第三步 添加当前目录中的所有文件到索引中
``` bash
git add .
```

### 第四步 代码提交本地仓库
``` bash
git commit -m "first commit"  // -m 指令可以添加提交注释
```

### 第五步 在GitHub上新建一个Repository（远程仓库）
仓库地址为： https://github.com/stevewei95/example.git
``` bash
git remote add origin https://github.com/stevewei95/example.git
```

如果远程仓库存在，可以用如下指令
``` bash
git fetch https://github.com/stevewei95/example.git
```

### 第六步 更新代码
``` bash
git pull origin master
```

### 第七步 提交代码
``` bash
git push -u origin master
```
