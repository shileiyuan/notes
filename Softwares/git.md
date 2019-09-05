### git config

```bash
# git config 全局配置的文件位置
cat ~/.gitconfig

# 配置name和email
# 这个配置对提交到github上的名字起作用，如果不设置，默认是系统名称
git config --global user.name 'Yuan, Shilei'
git config --global user.email 'shuxingo7@163.com'

# 查看所有配置
git config -l

# 查看全局配置
git config --global --list

# 删除全局配置
git config --global --unset user.name

# 配置别名
git config --global alias.st "status"
```



***

### 基本操作
1. git add
2. git status
3. git commit
4. git log
5. git init
6. .gitignore
    > \#注释
    >   .npm
    >   /node_modules
    >   npm_debug_log
7. git reflog 用来记录你每一次commit和reset对应的版本号
8. 回退到上一个版本： git reset --hard HEAD^
9. 回退到上上个版本： git reset --hard HEAD^^
10. 工作区就是我们在电脑里看到的目录。暂存区就是git add 添加到的地方。     git commit 将暂存区的内容提交到当前分支。默认为master
11. git checkout — readme.txt 丢弃工作区的更改，让这个文件回到最近一次git commit或git add时的状态。
12. 如果你将更改add到暂存区了，可以用git reset HEAD 将版本切换到最近一次commit的版本。从而丢弃暂存区的更改，将其放回工作区，然后再git checkout — readme.txt 丢弃工作区的更改。
13. 合并dev分支到当前分支：git merge dev
14. 删除dev分支： git branch -d dev
15. 如果要删除一个没有合并的分支： git branch -D dev
16. 新建本地分支并切换： git checkout -b dev <=> git branch dev 和 git checkout dev
17. 如果在一个分支修改了文件，但是没有提交，是不能直接切换到另一个分支的。先要git stash把当前工作区保存起来，然后才能切换。
    > git stash list 查看
    >   git stash apply 恢复
    >   git stash drop 删除
    >   git stash pop 恢复加删除
18. tag 是一个容易让人记住的名字，它跟某个commit绑定在一起，默认会打到HEAD上，也可以指定commitId
    > git tag v0.9
    >   -m 可以指定说明文字
    >   git tag 可查看所有标签
    >   git tag -d v0.1 删除标签
    >   git checkout -b ditu_color R-V2.0.R14.1.20180517  // 从某个tag上新建一个分支

***
### 添加远程仓库

```bash
# 在git bash中执行
ssh-keygen -t rsa -C 'shuxingo7@163.com'

# 会在用户主目录.ssh目录下生成id_rsa（私钥）和id_rsa.pub（公钥）两个文件
# 登录github->settings-> SSH keys -> title任意，将公钥粘贴进去

# 将本地仓库和远程仓库关联起来
git remote add origin git@github.com:shileiyuan/Quiver.qvlibrary.git

# 将本地的master分支push到远程仓库： （origin为远程仓库的名称）
git push -u origin master
```



***
### 碰到过的问题
* 每次提交都需要输入用户名和密码。 
  原因：关联远程库用的是https的方式。
  解决方法：
  > git remote -v 查看远程库和使用的协议
  > 重新设置成ssh的方式：
  > git remote rm origin
  > git remote add origin git@github.com:shileiyuan/bullet.git
  > git push -u origin master

***
### 常用操作

```bash
# 新建本地dev分支，并拉取远程dev分支的代码
git checkout -b dev origin/dev

# 查看远程仓库
git remote -v

# 查看远程分支
git branch -r

# 查看所有分支
git branch -a

# 关联本地当前分支与远程分支
git branch --set-upstream-to origin/dev

# 建立本地分支和远程分支的关联
git branch --set-upstream-to=origin/<branch> dev

# 改变一个文件名字的大小写，而不修改内容
git mv --force myfile MyFile

# 查看提交图（已经提前配置好了别名）
git lg

# 文件中的每一行最后是谁修改的和哪次修改做出了变更
git blame -L 63, src/routes/AppTopology/AutoList.jsx

# 追加一部分改动到上一次提交
git add .
git commit --amend

# fork 别人的仓库

```





### fork antd

```bash
# fork https://github.com/ant-design/ant-design.git

# clone fork后的仓库
git clone git@github.com:shileiyuan/ant-design.git

# 新建一个自己的分支abc，并提交
git checkout -b abc origin/abc
git push origin abc

# 添加antd本身的远程仓库用来同步代码
git add upstream https://github.com/ant-design/ant-design.git

# 同步upstream/master分支的代码
git pull upstream master

# 把master分支的代码merge到abc分支
git checkout abc
git merge master
```



***

### git提交规范
`<type>(<scope>): <subject>`
#### type: 用于说明commit的类型
1. feat：新功能（feature）
2. fix：修补bug
3. docs：文档（documentation）
4. style： 代码格式，比如代码的缩进
5. refactor：重构、优化（既不是新增功能，也不是修改bug的代码变动）
6. test：增加测试
7. chore：构建过程或辅助工具的变动

#### scope: 用于说明commit的影响范围，比如数据层、视图层、控制层等
#### subject：是commit的简短描述，不超过50个字符
1. 以动词开头，使用第一人称一般现在时，如使用change，而不是changed或changes
2. 第一个字母小写
3. 结尾不加句号(.)

[Commit message 和 Change log 编写指南](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)