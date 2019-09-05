### commit

没有被追踪的文件不能直接提交，必须要先add到暂存区

修改已经在暂存区的文件后，可以先add再commit，也可以直接`git commit -a`进行提交



### git rm

git rm 会在版本库和工作目录中同时删除文件



### rebase

rebase 的主要目的是为了保持一个线性的项目历史。比如所，当你在feature分支工作时，master分支上取得了一些进展，要将你的feature分支整合进master分支，你有两个选择，直接merge，或者先rebase后merge。

直接merge会产生一个额外产生一个merge commit，而rebase不会。



