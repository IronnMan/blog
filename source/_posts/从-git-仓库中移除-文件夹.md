---
title: 从 git 仓库中移除 .* 文件夹
date: 2019-05-03 12:29:19
tags:
---
例如文件夹 `.idea`，默认分支为 `master`。


1. 配置 .gitignore 文件
```bash
echo '.idea' >> .gitignore
```


2. 将 .gitignore 文件上传到远程仓库
```bash
git pull
git add .gitignore
git commit -m "edit .gitignore"
git push origin master
```

3. 删除 git 的 .idea 文件夹
```bash
git rm --cached -r .idea
```

4. 同步到远程仓库
```bash
git commit -m "rm .idea"
git push origin master
```
