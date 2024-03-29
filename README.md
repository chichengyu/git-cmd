# git-cmd

### 个人总结
```
ls -ah 	==> 查看所有文件（横向显示）
ls -al 	==> 查看所有文件（竖向显示）

git config --list => 查看配置的git信息
git config --global user.name '名字';
git config --global user.email '邮箱';

git config --global --unset user.name 删除属性

git rm -r -f --cached . 清除对所有文件的追踪(-r表示强制，.表示所有文件，也可以是文件名)

git config --system --unset credential.helper 解决没有弹出输入用户名和密码的提示框

git config --global http.sslVerify false 永久取消ssl认证
```

##### github远程仓库

配置公钥：
```
ssh -T git@github.com  ===> 验证ssh公钥是否成功
	如果没有,就 cd ~/.ssh 进入 ls 查看是否有
		id_rsa  id_rsa.pub 这两个文件
		注： id_rsa是私钥
		     id_rsa.pub是公钥

	如果如果没有这两个文件就需要运行如下命令,生成公钥私钥
		ssh-keygen -t rsa(新版本加密方式ed25519) / ssh-keygen -t ed25519
		或
		ssh-keygen -t rsa(新版ed25519) –C “你的email地址 “ / ssh-keygen -t ed25519 –C “你的email地址 “
	进行创建公钥,然后找到C:\Users\小池\.ssh下的id_rsa.ssh文件打开,并把内容公钥添加到github上,然后再次进行上面的命令,进行验证


cat .git/config  	==> 查看当前本地仓库的远程仓库
	一般用来查看 git remote add origin git@github.com:chichengyu/two.git 北地仓库连接的远程github仓库的地址
git remote rm origin  	==> 删除远程仓库
	或手动删除 vi .git/config 打开并把[remote “origin”]及内容删掉

git remote add origin 远程仓库地址  ==> 把本地仓库与远程仓库关联起来
	如：git remote add origin git@github.com:chichengyu/one.git
		然后cat .git/config 查看origin的url是否是远程仓库的地址
	或手动修改远程仓库地址
	vi .git/config

git remote -v 		  ==> 查看远程仓库配置

git pull origin master 	  ==> 把远程仓库的文件拉到本地仓库
	或 git pull --rebase origin master
	注意：如果远程库不为空必须做这一步，否则后面的提交会失败

git push -u origin master ==> 吧本地仓库的文件提交到远程仓库
	注：加上-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
	注意：提交时,本地仓库不能为空,不然报错

git clone 仓库地址 	  ==> 克隆远程仓库整个项目
	git clone https://{用户名}:{密码}@{gitlab地址}/具体项目.git  ==> 不使用windows保存的凭据
	env GIT_SSL_NO_VERIFY=true git clone https://xxxx.git      ==> 跳过ssl证书克隆

```
##### git版本库
```
git add . 		==> 添加当前本地仓库的所有文件到暂存区
git reset 文件名    	==> 取消暂存
	或 git reset --
git status 		==> 查看暂存区的文件或查看修改了那些文件
git status -s 		==> 简易查看
git diff 		==> 查看被修改的文件(所有修改的文件)具体修改了什么内容
git diff --staged	==> 查看被修改的文件(所有修改的文件)具体修改了什么内容
	如：git diff --staged index.php 	==> 查看被修改的index.php文件具体修改了什么内容

git commit -m '备注'  	==> 提交

git log  	==> 查看当前版本之前的所有记录
git reflog	==> 查看所有记录(包括回退后的后面的记录)

git reset --hard HEAD^	==> 回退上一个版本
git reset --hard HEAD^^	==> 回退上上一个版本
	注：彻底将工作区、暂存区和版本库记录恢复到指定的版本库

git checkout -- index.php 	==> 把index.txt文件在工作区的修改全部撤销
git reset HEAD index.php 	==> 可以把暂存区的修改撤销掉（unstage）,重新放回工作区
	注：命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令

git rm -r --cache 文件名  	==> 仅仅删除暂存区里的文件
	注：-r 是递归删除 

git rm -f 文件名  		==> 删除暂存区和工作区的文件

git reset --soft 版本库ID  	==> 仅仅只是撤销已提交的版本库，不会修改暂存区和工作区

git reset --mixed 版本库ID 	==> 仅仅只是撤销已提交的版本库和暂存区，不会修改工作区
```

##### 分支
```
git checkout -b dev  ==> 创建dev分支，然后切换到dev分支
	注：git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：
			git branch dev   -> 创建分支
			git checkout dev -> 切换分支


git branch  	==> 查看所有分支，当前分支前面会标一个*号

git merge dev   ==> 把dev分支的工作成果合并到master分支上
	如：把 index 分支合并到 master 分支
	    首先 切换到 master分支 
	    git merge origin/index
	注：合并时,会出现Fast-forward信息，Git告诉我们，这次合并是“快进模式”，也就是直接把master指向dev的当前提交，所以合并速度非常快。当然，也不是每次合并都能Fast-forward

	或 git merge --no-ff -m "merge with no-ff" dev
		注意：1.--no-ff参数，表示禁用Fast forward,用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并
		     2.因为本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去


git log --graph 	==> 查看分支合并图

git branch -d dev  	==> 删除dev分支
	注：-d是delete强制删除

git branch -m 名称	==> 重命名分支
	注：-m是强制重命名

	注意：git branch --help打开官方网站的git branch帮助文档



git stash   	==> 把当前未完工作“储藏”起来，等以后恢复现场后继续工作

git stash list  ==> 查看“储藏”的工作

git stash apply stash@{0}  ==> 恢复的时候，先用git stash list查看，然后恢复指定的stash
	注：1.用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除
		2.git stash pop，恢复的同时把stash内容也删了

	再用git stash list查看，就看不到任何stash内容了



git push origin dev  ==> 推送dev分支
	注：如果推送失败，先用git pull抓取远程的新提交

git pull 	     ==> 抓取远程仓库所有分支
	注：如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>

git checkout -b branch-name origin/branch-name   ==> 在本地创建和远程分支对应的分支
	注：远程仓库必须存在branch-name名称的分支，本地仓库的分支branch-name才能创建成功
```
