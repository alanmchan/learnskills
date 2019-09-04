# SVN

[TOC]

## 概述

SVN的全程是subversion，SVN版本控制软件可以解决 __协助开发__ 、__远程开发__ 和 __版本回退__ 三个问题。
SVN是输入C/S服务类软件，有客户端和服务端。

## SVN服务端
### 安装
```sh
# ubuntu
apt-get install subversion
# centos
yum install subversion
```
### 创建项目
```sh
# 创建svn版本库
$ mkdir /home/zhangsan/svnreporsity
# 创建项目
$ svnadmin create /home/zhangsan/svnreporsity/project
# 查看
$ ls
conf  db  format  hooks  locks  README.txt
```
### 配置
进入文件夹conf下，进行配置
- authz 是权限控制，可以设置哪些用户可以访问哪些目录
- passwd是设置用户和密码的
- svnserve是设置svn相关的操作，生成的文件中都有英文注释说明

#### 配置 svnserv.conf
```vim
anon-access = none     # 使非授权用户无法访问
auth-access = write    # 使授权用户有写权限
password-db = password   # 用户密码文件
authz-db = authz       # 访问控制文件
realm = /home/zhangsan/svn/project # 认证命名空间，版本库所在目录
# 采用默认配置，以上语句都必须顶格写，左侧不能留空格，否则会出错
```

#### 配置passwd(格式：帐号=密码)
```vim
[users]
# harry = harryssecret
# sally = sallyssecret
zhangsan = 123456
```
#### 配置authz权限
```vim
[/]             //仓库下所有文件
zhangsan=rw          //可读可写
lisi=r           //只读
*=              //其他用户无任何权限

# 分组
[groups]
zhang = zhangsan
li = lisi, liwu
[/]
@zhang= rw
@li= r
* =
```

### 启动与停止服务
```sh
svnserve -d -r /home/zhangsan/svnreporsity
# -d 表示守护进程， -r 表示在后台执行

killall svnserve
```

### 使用
```sh
svn checkout svn://IP/project
# 默认端口3690
svn checkout svn://127.0.0.1/project
```
其他使用见客户端

## SVN客户端
### checkout 检出
```sh
# 将文件从版本库检出到本地
svn checkout svn://192.168.199.224/demo
# checkout 到指定文件夹
svn checkout svn://192.168.199.224/demo test_svn

# 简写
svn co
```

### add 添加
```sh
# 向版本库添加新文件
svn add a.txt
svn add *.txt

# 递归添加文件夹下所有内容
svn add b

# 已经添加的文件修改后无法添加,只能用commit
```

### commit 提交
```sh
svn commit -m '注释内容' [-N] [--no-unlock] PATH

# 提交文件
svn commit -m 'add a.txt' a.txt
# 提交文件夹
svn commit -m 'add b' b

# 简写 
svn ci
```

### update 更新
```sh
# 更新到最新版本
svn update
# 只更新某个特定文件
svn update a.txt

# 还原到指定版本
svn update -r 1
# 还原某个文件到指定版本
svn update -r 1 c.txt

# 简写
svn up
```

### lock 锁
```sh
svn lock -m '加锁注释内容' [--force] PATH
# 加锁
svn lock -m "锁定文件" a.txt
# 解锁
svn unlock a.txt
```

### status 状态
```sh
# 查看或目录状态
svn status
svn st

# 显示文件及子目录的状态，正常不显示
# ? 不在svn的控制中
# M 内容被修改
# C 发生冲突
# A 预订加入到版本库
# K 被锁定
```
### delete 删除
```sh
# 删除版本库中文件
svn delete svn://127.0.0.1/demo/c.txt -m 'delete c.txt'
# 然后执行更新
svn update

# 推荐使用
svn delete c.txt
svn commit -m 'delete c.txt'

# 简写
svn (del, remove, rm)
```

### revert 撤销
```sh
svn revert a.txt
```

### log 日志
```sh
# 显示这个文件的修改记录，及版本号的变化
svn log a.txt
```

### info 信息
```sh
# 显示当前目录信息
svn info

# 显示某文件信息
svn info a.txt
```

### diff 比较差异
```sh
# 比较当前修改和版本库中的差异
svn diff c.txt

# 版本之间对比
svn diff -r 1:2 a.txt

# 简写
svn di
```

### merge 合并
```sh
# 将3版本和4版本合并到当前文件
svn merge -r 3:4 c.txt
# 但是一般都会产生冲突，需要处理一下
```

### help 帮助
```sh
svn help
svn help update
```

### switch URL变更
```sh
svn switch URL
```

### resolve 解决冲突
```sh
# 移除工作副本的目录或文件的“冲突”状态
svn resolved c.txt
# 注意: 本子命令不会依语法来解决冲突或是移除冲突标记；它只是移除冲突的
相关文件，然后让 PATH 可以再次提交
```

### 提交时遇到本地下载版本 和 服务器版本 不一致问题
==先更新(update)，解决冲突之后，再提交==
```sh
Select: (p) postpone, (df) diff-full, (e) edit,
        (mc) mine-conflict, (tc) theirs-conflict,
        (s) show all options:
解析:
(p) postpone 把服务器代码和自己的代码都显示出来，供我们解决
(df) diff-full 命令行显示冲突内容，不好看
(e) edit 修改，不要这么做
(mc) mine-conflict 只使用自己的代码，删除服务器的
(tc) theirs-conflict 只使用服务器的，删除自己的代码
(s) show all options 再重新打印一下这个选择日志
注意：选择 p 之后会多几个文件
1.m      多了 1.m.mine   1.m.r8     1.m.r9
1.m.r8 ：版本是8的时候的文件内容
1.m.r9 ：版本是9的时候的文件内容
1.m.mine ：当前自己文件的内容
```
```sh
打开1.m文件，根据冲突内容进行修改即可.
重点 ： 一定要删除这几行
<<<<<<< .mine
=======
>>>>>>> .r9


<<<<<<< .mine  自己本地文件中的内容
//修改了第1行
//第二行
=======  分割线:下边的是服务器上的内容
//修改了第1行
//第2行
>>>>>>> .r9
```
