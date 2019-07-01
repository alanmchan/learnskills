gitskills
===

git command

config
---

command|meaning|example
-|-|-
`git config --system user.name "Your Name"`|配置系统中所有用户的所有Git仓库用户名
`git config --global user.email "email@example.com"`|配置当前用户的所有Git仓库用户邮箱
`git config core.editor emacs`|配置当前项目的编辑器为emacs
`git config --global color.ui true`|让Git显示颜色，会让命令输出看起来更醒目


basic
---

command|meaning|example
-|-|-
`git init`|初始化仓库
`git status`|查看仓库状态
`git add <filename>`|把文件加到git仓库暂存区|`git add readme.txt`
`git commit -m <message>`|提交文件到版本库|`git commit -m "wrote a readme file"`
`git diff`|比较比较工作区和暂存区（最后一次add）的区别
`git diff --cached`| 是只比较暂存区和版本库的区别
`git diff HEAD -- filename`| 是只比较工作区和版本库（最后一次commit）的区别
`git log`|查看提交历史，以便确定要回退到哪个版本
`git log --pretty=oneline`|查看简略的提交历史
`git reflog`|查看命令历史，以便确定要回到未来的哪个版本
`git reset --hard HEAD^`|回到上一个版本
`git reset --hard HEAD^^`|回到上两个版本
`git reset --hard HEAD~100`|回到上100个版本
`git reset --hard <commit id>`|回到某个指定版本
`git checkout -- readme.txt`|可以丢弃工作区的修改:a)一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；b)一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态
`git reset HEAD <file>`|可以把暂存区的修改撤销掉,重新放回工作区
`git rm <filename>`|删除文件,接下来只需commit即可
`git checkout -- test.txt`|误删文件后,从版本库里恢复到工作区,git checkout其实是用版本库里的版本替换工作区的版本

branch
---

command|meaning|example
-|-|-
`git branch`|查看分支
`git branch <name>`|创建分支
`git checkout <name>`|切换分支
`git checkout -b <name>`|创建+切换分支
`git merge <name>`|合并某分支到当前分支
`git branch -d <name>`|删除分支
`git branch -D <name>`|强行删除一个没有被合并过的分支
`git log --graph --pretty=oneline --abbrev-commit`|查看分支合并情况
`git merge --no-ff -m "merge with no-ff" dev`|强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息

stash
---

command|meaning|example
-|-|-
`git stash`|将工作区内容封存
`git stash list`|查看封存的工作现场记录
`git stash pop`|恢复工作现场的同时把stash内容也删了
`git stash apply`|恢复工作现场的同时不会删除stash内容，需要利用`stash drop`手动删除
`stash drop`|删除工作现场
`git stash apply stash@{0}`|恢复指定的工作现场

remote
---

command|meaning|example
-|-|-
`ssh-keygen -t rsa -C "youremail@example.com"`|创建SSH Key
`git remote add origin git@github.com:mingjunior/gitskills.git` | 添加远程仓库,并命名为origin
`git push -u origin master`|第一次将本地主分支内容推送到远程仓库主分支,并将master与origin/master关联
`git push origin master`|将本地主分支内容推送到远程仓库
`git push origin dev`|将本地dev分支推送给远程
`git remote`|要查看远程库的信息
`git remote -v`|显示更详细的远程信息
`git clone git@github.com:mingjunior/gitskills.git` | 从远程仓库抓取内容，默认只抓取master分支
`git checkout -b dev origin/dev`|创建远程origin的dev分支到本地dev分支
`git branch --set-upstream-to=origin/dev dev`|指定本地dev分支与远程origin/dev分支的链接
`git pull`|将远程分支的内容抓取下来,以便在本地解决冲突,没有冲突自行合并
`git pull <远程主机名> <远程分支名>:<本地分支名>`|抓取远程主机的某个分支的内容与本地某个分支合并
`git pull <远程主机名> <远程分支名>`|抓取远程主机的某个分支的内容与当前分支合并.如果当前分支与远程分支存在追踪关系,则可以省略远程分支,即`git pull <远程主机>`.如果当前分支只有一个追踪分支,则远程主机也可以省略
`git fetch`|将远程分支的内容抓取下来,但是不会自行合并.git pull = git fetch + git merge
`git fetch origin master`|将远程主分支内容抓取到本地,随后再手动决定是否合并
`git fetch origin master:tmp` --> `git diff tmp` --> `git merge tmp`|将远程主分支内容抓取到本地临时分支,查看比较区别,手动合并
`git pull origin master --allow-unrelated-histories`|把远程仓库和本地仓库同步，消除差异. 在`git push`报`! [rejected] master -> master (non-fast-forward)`错误时使用，再add, commit相应文件，再push就可以了
`git remote rm origin`|删除已有的远程库origin
`git rebase origin`|你的本地当前分支里的每个提交(commit)取消掉，并且把它们临时 保存为补丁(patch)(这些补丁放到".git/rebase"目录中),然后把本地当前分支更新 到最新的"origin"分支，最后把保存的这些补丁应用到本地当前分支上。从而把本地未push的分叉提交历史整理成直线

tag
---

command|meaning|example
-|-|-
`git tag`|查看所有标签,标签不是按时间顺序列出，而是按字母排序的
`git show <tagname>`|查看某标签详细信息
`git tag <name>`|在当前分支上打标签,默认标签是打在最新提交的commit上的|`git tag v1.0`
`git tag <tagname> <commit id>`|在指定的commit id上打标签|`git tag v0.9 f52c633`
`git tag -a <tagname> -m <message> <commit id>`|可以创建带有说明的标签，用-a指定标签名，-m指定说明文字|`git tag -a v0.1 -m "version 0.1 released" 1094adb`
`git push origin <tagname>`|推送某个标签到远程
`git push origin --tags`|一次性推送全部尚未推送到远程的本地标签
`git tag -d <tagname>`|删除标签|`git tag -d v0.1`
`git push origin :refs/tags/<tagname>`|删除一个远程标签:先删除本地标签,再使用此命令删除远程相应标签

ignore
---

* Git工作区的根目录下创建一个特殊的 __`.gitignore`__ 文件
* __`.gitignore`__ 文件也要像其它文件一样加到项目仓库里进行版本管理( 直接用 `git add .gitignore` 和 `git commit`等命令), 这样项目里的其它开发者也能共享同一套忽略 文件规则

```git
# 以'#' 开始的行，被视为注释.
# 忽略掉所有文件名是 foo.txt 的文件.
foo.txt
# 忽略所有生成的 html 文件,
*.html
# foo.html是手工维护的，所以例外.
!foo.html
#  忽略所有.o 和 .a文件.
*.[oa]
```

忽略文件的原则是：

* 忽略操作系统自动生成的文件，比如缩略图等；
* 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
* 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。*

参考资料
---

* [Git文档](https://git-scm.com/book/zh/v2/)
* [Git Community Book 中文版](http://gitbook.liuhui998.com/index.html)
