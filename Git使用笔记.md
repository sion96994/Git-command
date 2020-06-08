# Git使用笔记
git init 目录名    生成版本库  
git add  文件名    将文件添加到仓库（暂存区）  
git commit   将文件提交到仓库,一般使用 git commit -m "提交的信息"  
初始化一个Git仓库，使用git init命令。  
添加文件到Git仓库，分两步：  
第一步，使用命令git add <file>，注意，可反复多次使用，添加多个文件；  
第二步，使用命令git commit，完成。  

git status  查看变更结果  
git diff 文件名  对比文件修改内容  
要随时掌握工作区的状态，使用git status命令。  
如果git status告诉你有文件被修改过，用git diff可以查看修改内容
修改提交日志记录  git commit --amend   

#### Reset
git log 查看修改记录，一般使用 "git log --pretty=oneline" 或者“git  log --oneline”  
git log 左边显示的是commit id（版本号）  
首先，Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，也就是最新的提交3628164...882e1e0（注意我的提交ID和你的肯定不一样），上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。  
git reset --hard HEAD^  回到上一个版本  
git reset --hard 3628164 回到之前/之后的某个版本  
git reflog  显示你的每一次提交更改命令历史  
穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。  

git checkout  -- file  丢弃工作区的当前修改(  -- 很重要，没有--，就变成了“切换到另一个分支”的命令)  
git reset HEAD file  把暂存区的修改撤销掉（unstage/index），重新放回工作区  
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。  
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。  
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。  

删除文件   用命令git rm删掉，并且git commit   
误删文件   git checkout  -- file  
命令git rm用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。  

关联一远程库，使用命令git remote add origin git@server-name:path/repo-name.git；  
关联后，使用命令git push -u origin master第一次推送master分支的所有内容；  
此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；  
分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！当有网络的时候，再把本地提交推送一下就完成了同步，真是太方便了！  

要克隆一个仓库，首先必须知道仓库的地址，然后使用git clone命令克隆。  
Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。  

git checkout -b dev   创建分支  
git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：  
$ git branch dev  
$ git checkout dev  

git branch    查看当前分支  
git checkout  分支名    切换分支  
git merge  分支名   合并指定分支到当前分支  
git branch -d dev   删除dev分支  

#### Branch
Git鼓励大量使用分支：  
查看分支：git branch  
查看远程分支: git branch –a  
删除远程分支: git push origin --delete <branchName>  
创建分支：git branch <name>  
切换分支：git checkout <name>  
创建+切换分支：git checkout -b <name>  
合并某分支到当前分支：git merge <name>  
删除分支：git branch -d <name>  
当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。  
  
  
用git log --graph或git log --graph --pretty=oneline  --abbrev-commit 命令可以看到分支合并图。  
通常，合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。  
如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。  
git merge --no-ff -m "merge with no-ff" dev  使用 --no-ff合并  
合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。    

git stash  暂时储存工作区  
git stash list 列出储存的工作区  
git stash apply  恢复储存的工作区，但是恢复后，stash内容并不删除，需要用git stash drop来删除。  
git stash pop  恢复储存的工作区，恢复的同时把stash内容也删了。  

#### Branch Push
修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
当手头工作没有完成时，先把工作现场git stash一下，然后去修复bug，修复后，再git stash pop，回到工作现场。  
开发一个新feature，最好新建一个分支；  
如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name> 强行删除。  

git remote  查看远程库的信息，git remote -v显示更详细的信息
git push origin master  推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上。  
如果要推送其他分支，比如dev，就改成 git push origin dev   

多人协作的工作模式通常是这样：  
1.	首先，可以试图用git push origin branch-name推送自己的修改；  
2.	如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；  
3.	如果合并有冲突，则解决冲突，并在本地提交；  
4.	没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！  
 
 
如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream branch-name origin/branch-name。
这就是多人协作的工作模式，一旦熟悉了，就非常简单。

#### Remote
查看远程库信息，使用git remote -v；  
本地新建的分支如果不推送到远程，对其他人就是不可见的；  
从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；  
在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；  
建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；  
从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。  
git tag <name>/ commit id  给分支打一个标签  
git tag    查看所有标签  
git show <tagname>   查看标签信息  
可以创建带有说明的标签，用-a指定标签名，-m指定说明文字
格式：git tag -a v0.1 -m "version 0.1 released" 3628164  

#### Tag
命令git tag <name>用于新建一个标签，默认为HEAD，也可以指定一个commit id；  
git tag -a <tagname> -m "blablabla..."可以指定标签信息；  
git tag -s <tagname> -m "blablabla..."可以用PGP签名标签；  
命令git tag可以查看所有标签。  
git push origin <tagname>  推送某个标签到远程  
git push origin --tags   一次性推送全部尚未推送到远程的本地标签  
如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除
git tag -d <tagname>  
然后，从远程删除。删除命令也是push，但是格式如下：  
git push origin :refs/tags/<tagname> 或者git push origin --delete tag <tagname>  
要看看是否真的从远程库删除了标签，可以登陆GitHub查看  

#### Push Tag
git push origin <tagname>可以推送一个本地标签；  
git push origin --tags可以推送全部未推送过的本地标签；  
git tag -d <tagname>可以删除一个本地标签；  
git push origin :refs/tags/<tagname>可以删除一个远程标签。  
git config --global color.ui true  让git显示颜色（会让命令输出看起来更醒目）  

有些时候，你想添加一个文件到Git，但发现添加不了，原因是这个文件被.gitignore忽略了，如果你确实想添加该文件，可以用-f强制添加到Git   
git add -f  file  
忽略某些文件时，需要编写.gitignore，格式如下：  
[root@hlymlv git]# cat .gitignore  
passwd  
nfs  
.gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理！  


#### Alias  
git config --global alias.st status  现在可以使用git st 代替 git status  
git config --global alias.unstage  'reset HEAD'  
现在可以使用 git unstage  file代替 git reset HEAD file
git config --global alias.last 'log -1'  
配置一个git last，让其显示最后一次提交信息  
  
完美显示执行历史：git  lg  
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"  
配置Git的时候，加上--global是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。  
配置文件放哪了？每个仓库的Git配置文件都放在.git/config文件中， 别名就在[alias]后面，要删除别名，直接把对应的行删掉即可。
