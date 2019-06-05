---
layout: 
title: 针对git多账号ssh-key的设置方法（github和gitlab）
date: 2019-02-02 17:54:36
tags:
---

日常使用git作为仓库使用时，会遇到以下情况，有一个github账号（私人博客的）和一个gitlab账号（公司的），提交时会遇到没有公钥权限的情况。下面详细说一下如何设置。

## 1.清除git的全局设置
```
    git config -global --list 可查看之前对 git 设置过全局的 user.name 和 user.email
```
#### 如果有，必须删除该设置
```
    git config --global --unset user.name "你的名字"
    git config --global --unset user.email "你的邮箱"

```
## 2.生成新的 SSH keys
```
	ssh-keygen -t rsa -C "heye@**.com" 名称是gitlab_id_rsa
	ssh-keygen -t rsa -C "heye@gmail.com" 名称是github_id_rsa
```
## 3.提供公钥给服务器
```
    1. 复制 ~/.ssh/gitlab_id_rsa.pub文件内容，进入gitlab / profile / SSH Keys，将公钥内容添加至 gitlab 。
    2. 复制 ~/.ssh/github_id_rsa.pub文件内容，进入github / setting / SSH and GPG keys / New SSH key 将公钥内容添加至 github 。
```
## 4.更新SSH配置文件，用户级别的配置文件~/.ssh/config
在~/.ssh/目录执行touch config，若有，先删除。
打开config，输入以下配置
```
        #default gitLab 公司账号
        Host git.**.com
        HostName git.**.com
        PreferredAuthentications publickey
        User heye@**.com
        IdentityFile ~/.ssh/gitlab_id_rsa
        
        #add gitHub 我的博客
        Host github.com
        HostName github.com
        PreferredAuthentications publickey
        User heye@gmail.com
        IdentityFile ~/.ssh/github_id_rsa
```
## 5.配置仓库用户信息
依次的加载顺序是
1.  ~/.gitconfig 中的用户信息
2. 当前项目使用的仓库的git目录中的config文件，也就是.git/config
因为不同仓库连接不同的服务器，所用的git用户信息也不同。
可以把常用的git用户信息配置到 ~/.gitconfig 中，不常用的我们在仓库中单独配置。以常用 gitlab 为例，这样就把公司账号设为了全局的默认用户信息。
```	
    git config --global user.name "heye2"
    git config --global user.email "heye@**.com"
```
进入本地 github 仓库配置 git 用户信息
```
    git config --local user.name "heye"
    git config --local user.email "heye@gmail.com"
```
不设置这一步的话，直接到第6步也会提示进行这一步的。

## 6. 测试github和gitlab账号通了没
```
	ssh -T git@git.**.com
```
连续输三次密码后，再次进行测试连接。
显示连接已经成功。
在git commit 时，提示没有用户信息
随之设置用户信息
于是就可以正常的提交代码到远程仓库了。
```
	ssh -T git@github.com
```
当看到测试得到的结果是success时，说明已经正确的设置可github和gitlab的ssh key了。
这样博客文件也可以正常的进行pull 和push 以及hero d -g 部署和发布了。
