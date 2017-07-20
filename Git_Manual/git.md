# Git
## 1.Git安装
## 2.初始设置
### 设置姓名和邮箱
````shell
git config --global user.name ""
git config --global user.email "your_email@example.com"
````
设置的配置文件````~/.gitconfig````
````
[user]
	name = Firstname Lastname"
	email = your_email@example.com
	ui = auto	//让命令输出更具可读性
````
## 3.基本操作
### git init----初始化仓库
````shell
mkdir git-tutorial
cd git-tutorial
git init
````
### git status----查看仓库的状态
````
git status
````
### git add----向暂存区中添加文件
````shell
git add file
git status
````
* 用Git仓库的工作树创建了文件，不会被计入Git仓库的版本管理对象中，文件的状态是Untracked files
* 使用````git add````命令将其加入暂存区，文件状态是Change to be committed
### git commit------保存仓库的历史记录
````shell
git commit -m "     "	//记述一行信息进行提交
git commit		//记述详细提交信息
````
* 在编辑器中记述提交信息的格式如下：
	* 第一行：用一行文字简述提交的更改内容
	* 第二行：空行
	* 第三行以后：记述更改的原因和详细内容
* 中止提交
	* 若编译器启动后想中止提交，可将提交的信息留空并直接关闭编辑器
* 查看提交后的状态
### git log----查看提交日志
````shell
git log
		//commit:提交哈希值
		//Author:提交者信息
		//Date：提交执行的日期和时间，提交信息
git log --pretty=short	//只显示提交信息的第一行
git log dir/file	//只显示指定目录、文件的日志
git log -p		//显示文件的改动信息
git log -p file		//显示指定文件的改动信息
````
### git diff----查看更改前后的差别
````shell
git diff	//查看当前工作树和暂存区的差别，若暂存区没有东西则显示工作树与最新提交状态之间的差别
git diff HEAD	//查看工作树和最新提交的差别，一般建议在git commit命令前执行
````
## 4.分支操作
### git branch----显示分支一览表
````shell
git branch
	//*当前分支
	//其余分支
````
### git checkout -b----创建、切换分支
````shell
git checkout -b BranchName	//创建并切换到BranchName
	-git branch BranchName
	-git checkout BranchName
````
* 不断对一个分支进行提交操作，称为“培育分支”。
* ````git checkout -````切回上一个分支
### 特性分支和主干分支
* 特性分支：集中实现单一特性的分支。往往创建数个特性分支，同时保留一个可以随时发布软件的稳定分支，稳定分支一般是master。
* 主干分支是特性分支的原点和合并的终点，通常以master作为主干分支。
### git merge----合并分支
````shell
git checkout master
git merge --no-ff BranchName
````
* 首先切换到master分支，再合并特性分支，填写合并信息后即完成合并。特性分支的内容就合并到master分支中。
### git log --graph----以图表形式查看分支
````shell
git logs --graph
````
* ````git log --graph````命令以图表形式输出提交日志
## 5.更改提交的操作
### git reset----回溯历史版本
````shell
git reset --hard 哈希值	//提供目标时间点的哈希值（在log中获取），完全恢复至该时间点的状态
````
* 回溯至过去的某个时间点的状态，也可使用````git reset --hard 哈希值````向前推进历史，即可以任意穿梭于已有的历史
### 消除冲突
* 在进行分支合并时有可能会产生冲突内容，查看冲突内容并将其解决，提交解决后的结果
### git commit --amend----修改提交信息
````shell
git commit --amend
````
* 执行上述命令，编辑器就会启动，修改其中的提交信息，保存退出编辑器即可
### git rebase -i----压缩历史
````shell
git commit -am ""	//添加和提交目录树，只针对小部分修改
//修正错误，使用 git diff 查看修改
git commit -am "Fix typo"	//错字漏字等失误称typo
git rebase -i HEAD-2	//选定当前分支中包含HEAD（最新提交）在内的两个最新历史记录为对象，并在编辑器中打开
//将“Fix typo”左侧的pick部分删除，改写为fixup，保存并退出编辑器。再查看日志时，Fix typo就从历史中抹去
````
* 压缩历史的意义在于去除拼写错误的历史记录不被需要，将其抹去，算作一种良性历史修改
## 6.推送至远程仓库
### 远程创建仓库
* 在github上新建一个仓库，仓库名与本地仓库名保持一致，即git-tutorial。创建时不要勾选Initialize this repository with a README选项。
### git remote add----添加远程仓库
````shell
git remote add origin git@github.com:xxx.git	//Git自动将远程仓库的名称设为 origin（标识符）。
````
### git push----推送至远程仓库
````shell
git push -u origin master
````
* ````git push````:当前分支的内容就会被推送给远程仓库 origin 的 master 分支
* ````-u````:将 origin 仓库master分支设置为本地仓库的 upstream（上游）。将来运行````git pull````命令时就可直接从 origin 的 master 分支获取内容。
````shell
git push -u origin BranchName
````
* 将BranchName分支 push 至远程仓库。
## 7.从远程仓库获取（多名开发者共同开发）
### git clone----获取远程仓库
````shell
git clone git@github.com:xxx.git
git branch -a	//查看当前分支的相关信息。添加 -a 可同时显示本地仓库和远程仓库的分支信息
````
* 执行````git clone````后，默认处于master分之下，系统自动将origin设置成远程仓库的标识符。
````shell 
git checkout -b BranchName origin/BranchName
````
* -b 后面是本地仓库中新建分支的名称，与远程仓库同名，便于理解。
* 新建分支名称之后是获取来源的分支名称。
````shell
//修改内容
git diff
git commit -am " "
git push
````
* 假定另一名开发者从远程获取BranchName分支，在本地仓库提交更改，再将BranchName分支推送回远程仓库，可以与其它开发者共同培育BranchName分支。
### git pull----获取最新的远程仓库分支
````shell
git pull origin BranchName
````
* 加入其他开发者更新了远程仓库中BranchName分支，所以本地的BranchName分支就需要更新。
* 若多个人同时修改同一部分的源代码，push 时就很容易发生冲突。建议频繁进行 push 和 pull 操作。