（1）git --verison 查看版本

（2）clear 清屏

（3）git config --global user.name "chencs" 设置用户名

git config --global user.email "13545028051@163.com" 设置用邮箱

(4)本地仓库初始化 git init

cd .git/  查看本地库隐藏文件，也就是git 配置文件

本地结构：工作区--->(git add)暂存区--->（git commit）本地库

git add git.MD 添加到暂存区

git commit -m"g备注信息" 添加到本地库

git log 查看日志   空格 查看日志下一页，Q 推出查看日志继续编写其他命令

日志展示方式2 git log --pretty = oneline

日志展示方式3 git log -- oneline

日志展示方式4 git reflog 

版本后退  git reset --hard 【哈希索引值】本地库,暂存区，工作区都版本后退

git reset --mixed【哈希索引值】 本地库版本后退，暂存区也后退，工作区不动

git  reset --soft   【哈希索引值】 本地库后退，其他不动

删除工作区的文件 rm git.MD

mkdir 创建文件夹

远程库地址

git remote -v 查看    
<<<<<<< HEAD

git remote add origin (远程库地址)  这是给远程库起别名 为：origin
=======
>>>>>>> 966a74e (git集成idea)

git remote add origin (远程库地址)  

这是给远程库起别名 为：origin、javaRes

**往远程库推送  git push origin master**

生成ssh公钥ssh-keygen -t rsa -C "13545028051@163.com"



提交文件夹 git add GitResp   跟提交文件一样

<<<<<<< HEAD

=======
git clone 地址

2、抓取远程库master分支 到本地库 ，工作区看不到

git fetch origin master 

3、 拉去别人上传到远程库的东西 跟clone很像

git pull origin master

4、免密操作

ssh




>>>>>>> 966a74e (git集成idea)



# git status 出错 interactive rebase in progress； onto 796e78f

**两种解决方案**

1. 使用 `git commit --amend` 命令修订当前的提交
2. 使用 `git rebase --continue` 命令继续代码的提交(推荐),执行之后,需要重新提交,解决一下当前的代码冲突之后重新提交直至没有`rebase`提示,就可以正常提交了







最常用的git 命令操作

1）先看git status

2）提交 git add .  提交所有文件   

3）传到本地库  git commit -m [mseeage]

4) push 远程库 git push javaRes master

