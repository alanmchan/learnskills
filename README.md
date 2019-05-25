gitskills
===

git command

basic
---

command|meaning|example
-|-|-
`git init`|初始化仓库
`git status`|查看仓库状态

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
git remote add origin git@github.com:mingjunior/gitskills.git | 添加远程仓库
git push -u origin master|第一次将本地主分支内容推送到远程仓库
git push origin master|将本地主分支内容推送到远程仓库
git push origin dev|将本地dev分支推送给远程
git remote|要查看远程库的信息
git remote -v|显示更详细的远程信息
git clone git@github.com:mingjunior/gitskills.git | 从远程仓库抓取内容，默认只抓取master分支
git checkout -b dev origin/dev|创建远程origin的dev分支到本地
git branch --set-upstream-to=origin/dev dev|指定本地dev分支与远程origin/dev分支的链接
git pull|将远程分支的内容抓取下来
