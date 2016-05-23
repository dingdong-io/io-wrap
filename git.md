add前

git add . 所有文件
add后,未commit时,加入该段
add后,修改再/commit报错,需要重新add

只提交n.txt,git有修改不提供可否

$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
$ git config --global core.editor emacs
$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"


# 远程
git clone 
关于url的选择,会对后续操作造成影响.
https://github.com/dingdong-io/io-wrap.git
github默认的项目地址,用的https协议,每次远程操作需要输入用户名密码,哪怕没有退出命令窗口.当克隆他人项目,不会涉及后面的提交时,推荐,因为它简便.

git@github.com:dingdong-io/io-wrap.git 这种是ssh协议的,直接在命令行里push是行不通的,连用户密码都不跳.(git@github.com: xx 固定写法,特别注意这个冒号)
适用于自己项目,修改后提交较快速(https每次输入用户名密码,就算用brackets的插件,也是简化了输入,但是验证的延时免不掉).如果需要修改他人项目(只有读取权限),就需要先fork一份,再对自己fork的clone(才有写入权)

ssh方式必须做一个配置,所幸对一台电脑来说,只需配置一次.(对自己github帐户下所有项目有效)
ssh-keygen -t rsa -C "your_email@example.com"
会提示输入一些,都不输入全回车即可.
有个类似密码,输入了每次提交会让你输入密码,回到https时代去了,为了便捷都为空.

到https://github.com/settings/keys,到个人设置页,设置信任的ssh列表,随便输入title(我是用的计算机名,如我的pc就用pc,以此区分,多人项目用人名较合适),key的内容,到刚才生成的key文件中找.
C:\Users\Administrator\.ssh下生成两个文件,打开id_rsa.pub,复制其内容(公钥)到github的key.(如果文件找不到,重新做ssh-keygen的生成步骤,记得都为空)

第一次远程提交时,会有一个信任远程主机的确认
The authenticity of host 'github.com (192.30.252.129)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)?
当然yes喽.(目前必须"yes",不认"y",从https://help.github.com/articles/what-are-github-s-ssh-key-fingerprints/ 可知,github对外的公钥正是nThbg6kX...Y8)


原先的https协议转ssh
git remote -v //地址情况,可以看到https://...
git remote rm origin  //删除当前的远程仓库
git remote add origin git@github.com:dingdong-io/io-wrap.git //新增远程仓库,这个origin名字是自己起的别名


git fetch //拉取远程 ,默认摘取所有

git merge
merge 自身就会产生一条commit,
如果冲突,就要自行处理并提交

git push
第一次时失败,因为本地master与远程master还未建立跟踪关系,按提示
git push --set-upstream origin master  //这里省略了本地的master

git branch --set-upstream dev origin/dev //当前在master上,但用这种让本地dev跟踪远程dev分支 
本地分支明确了跟踪对象后,以后只需git push即可


pull或push时,冲突会报一行以"CONFLICT"开头的文字,指出冲突文件,后续可以在git diff中看到,其它命令难以定位冲突位置,可能需要借助图形化帮助找出(大量文件改变时,定位冲突成为难题)

git产生的冲突大概如下
<<<<<<< HEAD
make a ==>
=======
remote addd
>>>>>>> a7abad55680ac5789ff132ef67e05cb3b3eb714d     

修改后,再提交.问题是git不管你的修改合理不,你可以留着这些<<==>>,随便打个空格就能提交上去了.虽然不会主动这样做,但也放大了失误的机会



# 回滚
除了git reset --hard 177348 外
!! git log 在回滚后,回滚hash处之后的commit不再显示,日后想取消回滚,找回后面的修改.需用到git reflog查看,

git branch -v 看分支当前指针
git branch -vv 多了一项,跟踪的对应远程分支名称
 master                       002514b [origin/master] add n.txt second line


git checkout 177348  不要用该方法回滚
commit时总是无东西可提交,报HEAD detached from 17768f5.其它操作也可能报出"HEAD detached"的相关错误.
原因:HEAD指向分支,分支指向hash(即某条commit的位置).而这里强制让HEAD直接指向hash,HEAD的指针在这种情况下怎能跟踪分支呢.所以,尽管用它可以切到某条commit的状态,还是不要用,等于自己挖了个坑,排错麻烦.这时的git log是正常的,用git branch命令能发现异常
$ git branch
* (HEAD detached from 17768f5) //这个状态不合理
  m2
  master
  
  
# 子模块
git submodule add  git@github.com:dingdong-io/io-build.git [new]
可以到任意子文件夹下添加子模块,默认io-build建立同名文件夹,指定名字后将建立new文件夹.

git submodule 查看所有子模块名及他们的指针
git submodule init //初次clone一个父模块时,init一下,关联初始化 或者pull时发现远程新增了某子模块
git submodule update //更新,不推荐这样的方式 它适用于子项目保持某个版本,而不是最新的场景

通过commit时的修改,可以看到本地的父模块,对子模块整个文件夹的认识,为一个commit指针
Subproject commit 0c1bfbcaaf7e39a1a6ec59bcee33f28aba938ace	
Subproject commit 076222f69c8963064c2cdcde079125e08486cf44
而github...


与各自维护不同,子模块下没有.git文件夹,但有个.git文件,极小.标记了他在父模块.git中的位置,而事实上子项目更新时会从父中拿到自己的(有一种情况,先clone一个模块,然后手动添加给他的父,不在此列).git更新到github上 

node_modules 在node项目中建议加入.gitignore,所以子项目放里面是不合理的.

### 协作
子模块更新后,父模块的child文件夹会发生改变(就是指针的改变),这时若只提交子,另一台电脑在父模块下git submodule update无效,但子中git pull会更新(这点与独立项目相同)
因此,可以减少父的提交
如果要用git submodule update来所有子更新(不推荐),需要的步骤是:更新子-更新父 - 另一台电脑更新父 -另一台电脑更新子


### 批量子模块的操作
git submodule foreach
由于常遇到HEAD不在master的问题(操作子模块常见的报错:You are not currently on a branch ,并且任何一个子模块出错会打断后续的批量操作),以下几条命令特别好用.
git submodule foreach git branch //查看是否在master上
git submodule foreach git checkout master //全部切到master上 ,这意味着子模块尽量不要搞各种分支
git submodule foreach git pull  
git submodule foreach git push 

特别要提一下,git submodule foreach git pull/push  ,适用于所有子模块保持最新版,我推荐这种方式.随时修改,上传,拉取.它可以与单个子模块的git pull ,git push良好合作,但一旦用git submodule update(不推荐的方式),所有的HEAD会被切到父模块记录的各子的commit id上,又得手动去checkout master.

### 子模块总结
不论从协作,还是批量子模块角度,都推荐对子模块分别操作,批量时用git submodule foreach.
永远不要使用git submodule update(除非需求上,不希望子模块最新),如果用了,用git submodule foreach git checkout master 补救




# 其它
当git Add一个文件,add后该文件又产生了修改,即缓存区与本地产生冲突时,禁止commit
本地再新增一文件,未add,缓存与本地不同,但不产生冲突,可commit
修改不add该文件,去commit其它文件也可行
$ git reflog
440eb92 HEAD@{0}: reset: moving to 440eb
d260d77 HEAD@{1}: reset: moving to d260d77 ...
df1038c HEAD@{9}: checkout: moving from master to m2  ...
它的表示大概如下,它记录了所有产生修改的操作命令.拿到hash后,可配合git show 440eb92 来看详情. 当丢失commit后,通常用它寻找.


那么,推测,是不是就是缓存与本地冲突时,禁止commit,
从远程拉后,也会使本地与缓存冲突



