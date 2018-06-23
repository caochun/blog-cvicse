---
title: Git与GitHub入门教程
date: 2018-06-23 19:42:58
tags:
---

## 第一步：克隆远程仓库
### 1.1 第一次克隆远程仓库
```
C:\blog-cvicse>git clone https://github.com/caochun/blog-cvicse```
### 1.2 以前已经克隆仓库，若文件有修改需要更新
### 1.2.1 查看当前工程状态
```
C:\blog-cvicse>git status
Changes not staged for commit:
    (use "git add<file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)```
### 1.2.2 根据提示可选择提交或者放弃修改
```
C:\blog-cvicse>git checkout package.json
C:\blog-cvicse>git checkout package-lock.json```
### 1.2.3 更新文件
```
C:\blog-cvicse>git pull```
## 第二步：修改文件并提交本地git仓库
### 2.1 添加修改文件
```
C:\blog-cvicse>git add source/```
### 2.2 提交修改到本地仓库
### 2.2.1 提交本地仓库出错
```
C:\blog-cvicse>git commit -m "blog20180623"
*** Please tell me who you are.Run
  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"
to set your account's default identity.
Omit --global to set the identity only in this repository.
fatal: unable to auto-detect email address (got 'admin@T460P.(none)')```
### 2.2.2 注意事项：未配置邮件及作者的需要配置
```
C:\blog-cvicse>git config --global user.email "yangjiyanfengyun@163.com"
C:\blog-cvicse>git config --global user.name "vncnetcat"```
### 2.2.3 提交失败后再次提交本地仓库
```
C:\blog-cvicse>git commit -m "blog20180623"
[master 43b0a17] blog20180623
 3 files changed, 69 insertions(+)
 create mode 100644 source/images/graph/companyRelationShips.png
 create mode 100644 source/images/graph/work21.jpg```
## 第三步：提交远程git仓库
```
C:\blog-cvicse>git push
remote: Permission to caochun/blog-cvicse.git denied to vncnetcat.
      fatal: unable to access 'https://github.com/caochun/blog-cvicse/': The requested URL returned error: 403```
如果无权限提交master，需要先fork master，再提交fork，通过fork的工程进行pull request，master作者通过审核，可看到更新内容
查看远程仓库列表
```
C:\blog-cvicse>git remote show
origin```
默认提交的远程仓库是origin
### 3.1 添加fork后的分支远程仓库
```
C:\blog-cvicse>git remote add myCvicseBlog https://github.com/vncnetcat/blog-cvicse```
查看远程仓库列表
```
C:\blog-cvicse>git remote
myCvicseBlog
origin```
### 3.2 提交到fork后的分支远程仓库
```
C:\blog-cvicse>git push myCvicseBlog
Counting objects: 9, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (9/9), done.
Writing objects: 100% (9/9), 390.90 KiB | 15.64 MiB/s, done.
Total 9 (delta 3), reused 0 (delta 0)
remote: Resolving deltas: 100% (3/3), completed with 3 local objects.
To https://github.com/vncnetcat/blog-cvicse
   820d005..43b0a17  master -> master```
### 3.3 在Github上发起pull request，【New pull request】
在Github上发起一个Pull Request，意思是请求工程的所有者从我fork出来的仓库合并分支