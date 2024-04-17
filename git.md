# git
>分布式版本控制工具
# git工作机制
>工作区(写代码)git_add=>暂存区(临时存储)git commit=>本地库(历史版本)=>远程仓库

# 代码托管中心
>代码托管中心是基于网络服务器的远程代码仓库,一般我们称为远程库

>局域网:GitLab

>互联网：GitHub,Gitee
# git常用命令
### 本地操作
>git config--global user.name 用户名 :设置用户签名

>git config--global user.email 邮箱 :设置用户签名

>git int :初始化本地仓库

>git status :插卡本地库状态

>git add 文件名：添加到暂存区

>git commit-m "日志信息" 文件名 ：提交到本地库

>git reflot:查看历史记录

>git reset --hard 版本号：版本穿梭

>git reflog或log :查看版本信息

>git branch 分支名：创建分支

>git branch -v:查看分支

>git checkout 分支名:切换分支

>git merge 分支名 ：把指定的分支合并到当前分支上
### 远程仓库操作
> git remote -v :查看当前所有远程地址别名

>git remote add 别名 远程地址 ：起别名

>git clone 远程地址： 将远程仓库内容克隆到本地(1。拉取代码，2.初始化本地仓库，3.创建别名)

>git pull 远程库地址别名 远程分支名： 将远程藏库对于分支最新内容拉下来后与当前本地分支直接合并(本地跟远程不同步，拉取远程库)

>git push 远程仓库地址别名  远程分支名 ： 将本地分支推送到远程仓库

# 