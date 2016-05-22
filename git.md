add前


add后,未commit时,加入该段
add后,修改再/commit报错,需要重新add

只提交n.txt,git有修改不提供可否



# 远程
git clone


# 其它
当git Add一个文件,add后该文件又产生了修改,即缓存区与本地产生冲突时,禁止commit
本地再新增一文件,未add,缓存与本地不同,但不产生冲突,可commit
修改不add该文件,去commit其它文件也可行

那么,推测,是不是就是缓存与本地冲突时,禁止commit,
从远程拉后,也会使本地与缓存冲突




