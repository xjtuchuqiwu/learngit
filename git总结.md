##git总结
<!-- TOC -->

- [git总结](#git总结)
        - [1.git安装](#1git安装)
        - [2.创建自己的本地版本库](#2创建自己的本地版本库)
        - [2.远程仓库](#2远程仓库)
        - [3.文件操作](#3文件操作)
        - [4.分支管理](#4分支管理)
        - [5.多人协作](#5多人协作)
        - [6.rebase](#6rebase)
        - [7.标签](#7标签)
        - [8.github](#8github)
        - [9.搭建自己的git服务器](#9搭建自己的git服务器)
        - [10.IDEA中使用git](#10idea中使用git)
        - [10.git相比svn的优势](#10git相比svn的优势)

<!-- /TOC -->
#### 1.git安装
[参考网址](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137396287703354d8c6c01c904c7d9ff056ae23da865a000)

#### 2.创建自己的本地版本库
```java
mkdir gitlearn 
cd gitlearn 
pwd
git init             //初始化版本库
git add git总结.md   //上传文件
git commit -m "提交git总结到仓库"   //将文件提交到仓库,-m用于本次提交的说明,
                                   //add可以多次添加文件,commit一次可以提交多个文件
需要创建多个仓库的时候，只需要切换到本地目录，新建一个自己的本地仓库，再关联远程仓库即可
```


#### 2.远程仓库
* **2.1添加远程仓库(将自己的版本库和远程连在一块)**
```python
在github上新建一个repository，并且复制该repository的地址
git remote add origin git@github.com:xjtuchuqiwu/learngit.git  //将本地新建的仓库和远程关联起来
git push -u origin master     //将master分支上的所有内容push到远程仓库origin
git push origin master -f     //提交冲突后，出现分叉，更改文件后，强制提交
```

* **2.2 从远程仓库克隆**
```java
git clone git@github.com:xjtuchuqiwu/learngit.git    //获取远程仓库地址，就可以克隆到本地
```

#### 3.文件操作
* **3.1 版本回退** 
git在内部有个指向当前版本的HEAD指针，当你回退版本的时候，Git仅仅是把HEAD指针移动了
```java
git status                //查看仓库工作区状态，文件有无被修改过
git diff                  //如果文件被修改过，可以查看文件的更改内容, +789,表明添加了789这一行
git log pretty=oneline    //查看工作区的commit情况,看到有几次提交
git reset --hard  5d6f1a3919bb3f2c3add53f305bb195435ca595a  //退回到该版本,HEAD指向当前版本,版本号不用写全，git自动查找
git reset --hard HEAD^      //退回到上一个版本，HEAD^ 上一个，HEAD^^ 上两个

git push oringin master   //这里会报错，Updates were rejected because the tip of your current branch is behind ，说明远程仓库的版本要比本地的新
git push origin master   //强制提交，不可一开始就强制提交！
git pull origin master   //先将远程仓库文件pull下来，然后再回退版本，再强制提交

//如果想往前去找版本
git reflog        //找到commitID,就可以回到前面的版本
```

* **3.2 工作区和暂存区**
* 工作区:电脑上能看到的目录
* 版本库：.git就是版本库，里面存放了git最重要的暂存区
* **管理修改**
```java
git add 命令就是将提交的修改放到暂存区
git commit 就是将暂存区的所有修改一次性提交到分支
如果不用git add 添加到暂存区，则commit不会提交到分支

vi README.md            //不小心修改了这个文件
git checkout README.md  //撤销修改 

vi README.md        //不小心修改了
git add README.md   //还不小心放到缓存区了
git reset HEAD README.md   //git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本。
git reset README.md       //退回了工作区，再checkout即可

//如果已经提交不适合的修改到版本库的时候，就可以考虑版本回退了,前提是没有推送到远程库
//如果推送到了远程库，先pull下来，修改到自己需要的把呢不能，再强制提交
``` 

* **删除文件**
```java
rm README.md        //直接删除文件
git checkout -- README.md    //不小心删除了，找回来，使用版本库里的文件替换，但是这样会丢失最后一次的对文件的提交修改
git rm README.md   //直接从版本库里也删除文件,这样就checkout不回来了,想回来的话只能版本回退
```

#### 4.分支管理
* 创建一个分支的过程:
    * <font size=2>原来没有创建分支时,HEAD指针指向master,master指向当前最新的版本
    * 创建dev分支，dev分支指向当前最新的版本，HEAD指针指向dev分支，表名切换到了dev分支
    * 使用dev分支进行文件的修改，dev指向最新的版本，HEAD指向dev，但是master仍然指向之前的版本
    * 把dev合并到master上，这样master就指向了最新的分支,然后删除dev分支即可
</font>
```python
git checkout -b dev       //创建并切换dev分支
git branch                //查看当前分支
vi read.txt               //创建一个新的文件
git add read.txt          //提交到缓存区
git commit -m "add read.txt"   //提交read.txt
git checkout master            //切回到master分支
git master merge dev           //将分支的修改结果合并到master
git branch -d dev              //删除分支
git branch                     //查看只有mater一个分支了 ,Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在master分支上工作效果是一样的，但过程更安全 
```
* 解决冲突
```python
git checkout -b dev //创建并切换到新的分支dev
vi read.txt //修改read.txt文件
git add read.txt //在dev分支上提交文件修改
git commit -m "modify read.txt" //提交修改
git checkout master //切换到master分支
vi read.txt //对read.txt修改
git add read.txt //在master分支进行提交
git commit -m "modify read.txt" //在master分支提交修改
git merge dev //合并分支时，就会产生了冲突
git status //也可以查看产生冲突的文件
vi read.txt //这里就会在文件里出现两种版本，分别是两个分支修改的部分，修改一下
git add read.txt
git commit -m "modify last"
git branch -d dev //删除分支
git push -u origin master //提交到远程github
git log --graph //看到分支合并图

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交
```
* 分支管理策略
<font size=2>
合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。
如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。</font>
```php
git checkout -b dev //创建分支dev
vi read.txt //在Dev分支下修改文件
git add read.txt
git commit -m "modify read.txt 1" //在dev分支下修改并提交read.txt
git checkout master //切换回master分支
git merge --no-ff -m "merge with no-ff" dev //master利用--no-ff 模式合并dev  因为本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去。
git log //查看分支历史

分支管理原则：
首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
干活都在dev分支上，你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往master分支上合并就可以了。
合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。
```

* bug分支
<font size=2>在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。
突然来了个bug修复任务，但是当前任务还没有做完,先把工作现场git stash一下，然后去修复bug，修复后，再git stash pop，回到工作现场。</font>
```java
git stash      //把当前工作现场"储藏起来",等以后恢复现场后继续工作
git status     //查看工作区,干净了
首先确定要在哪个分支上修复bug，假定需要在master分支上修复，就从master创建临时分支：
git checkout master //切换到master分支
git checkout -b issue-101 //创建修复bug的分支
vi read.txt         //bug在这里，修复了
git add read.txt
git commit -m "修复了bug" //修复完成后提交
git checkout master      //切换到master主分支
git merge --no-ff -m "origin/master" issue-101 //合并修复bug的分支

//修复好了bug，继续回到之前的分支进行工作
git checkout dev  //切换到工作的分支dev
git status        //工作区是干净的，需要找回之前保存的现场
git stash list    //查看工作区保存的现场
```
<font size=2>
一是用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除；
另一种方式是用git stash pop，恢复的同时把stash内容也删了：
</font>
```python
git stash pop               //恢复现场，并且把stash的内容删除了
git stash list              //再次查看工作现场，已经没有内容了
git stash apply stash@{0}   //恢复指定的stash现场
```

* feature分支
<font size=2>软件开发，添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。</font>
```java
git checkout -b feature-vulcan    //切换到功能分支
git add vulcan.py                //开发完毕，添加到缓存区
git status                       //查看提交状态
git commit -m "modify vulcan.py" //提交修改
git checkout dev                 //切回dev

//突然要求该功能分支不要了
git branch -d feature-vulcan //删除失败,Git友情提醒，feature-vulcan分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用大写的-D参数。。
git branch -D feature-vulcan //删除分支成功
```

#### 5.多人协作
* <font size=2>当你从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称是origin。
```
git remote      //查看远程库的信息
git remote -v   //显示更详细的信息
git push origin dev //向远程推送其他分支
``` 
* 哪些分支需要推送，哪些不需要呢？
    * master分支是主分支，因此要时刻与远程同步；
    * dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
    * bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
    * feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发
* 当从远程库clone时,本地就只有一个master分支，如何直接调用远程的dev分支进行开发，然后合并到origin呢？
</font>
```python
git clone git@github.com:xjtuchuqiwu/spark_learn.git   //先切换到本地的仓库，git init，之后关联远程库，然后从远程库里clone下来代码
git branch            //克隆完后，查看分支。只有mater分支
git checkout -b dev origin/dev     //创建远程origin的dev分支到本地
git add read.txt
git commit -m "modify read.txt"    //完成修改后提交 
git push origin dev                //将修改的分支push到origin


//你自己也对一个文件进行了修改时，如果想push到origin是不行的，因为你的小伙伴的最新提交和你试图推送的提交有冲突
//解决办法也很简单，Git已经提示我们，先用git pull把最新的提交从origin/dev抓下来，然后，在本地合并，解决冲突，再推送：
git pull      //git pull也失败了，原因是没有指定本地dev分支与远程origin/dev分支的链接，根据提示，设置dev和origin/dev的链接：
git branch --set-upstream-to=origin/<branch> dev //提示中得到
git pull             //再次pull
git commit -m "123"  //此时合并会有冲突，这个时候需要解决冲突后再提交
cat read.txt
git commit -m "13"   //解决冲突后再次提交
git push origin dev  //push到远程仓库
```

* <font size=2>多人协作的工作模式
    * 首先，可以试图用git push origin <branch-name>推送自己的修改；
    * 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
    * 如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>。然后再git pull 
    * 如果合并有冲突，则解决冲突，并在本地提交
    * 没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！
</font>


#### 6.rebase
```python
git log --graph --pretty=oneline --abbrev-commit //查看版本迭代的情况
git rebase      //输出了一大堆操作
git log --graph --pretty=oneline --abbrev-commit //再次查看,原本分叉的提交变成了一条直线

rebase操作前后，最终的提交内容是一致的
rebase操作的特点：把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了

git push origin master //提交到远程
```


#### 7.标签
<font size=2>tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。
“请把上周一的那个版本打包发布，版本号是v1.2” “按照tag v1.2查找commit就行！”</font>
```java
git branch
git checkout master //先切换到需要打标签的分支
git tag v1.0 //直接打上标签即可,默认标签是打在最新的commit上的,即HEAD
git tag //查看所有标签

//给之前忘记打标签的commit打上标签
git log --pretty=oneline --abbrev-commmit 
//找到之前提交的commit_id
git tag v2.0 01016e0 //根据commit_id 打上标签
git tag //查看标签
git show v1.0 //查看某一个标签的信息
git tag -a v1.0 -m "this is the description of this tag" 01016e0 //-a表示只当标签名，-m表示添加描述信息
git show v1.0 //可以看到添加的描述信息

注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。

git tag -d v1.0 //删除标签 , 创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。
git push origin v1.0 //推送某个标签到远程
git push origin --tags //一次性推送全部尚未推送到远程的本地标签

//删除远程标签
git tag -d v1.0 //先删除本地标签
git push origin:refs/tags/v1.0 //从远程删除，但是格式如下
```
#### 8.github
<font size=2>

* 在GitHub上，可以任意Fork开源仓库;
* 然后从自己的账号下clone该仓库到本地仓库;
* fork后,自己拥有仓库的读写权限s
* 可以在github上推送 pull request给官方仓库贡献代码
* 如果github访问速度太慢，可以使用[码云](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00150154460073692d151e784de4d718c67ce836f72c7c4000)
</font>

#### 9.搭建自己的git服务器
<font size=2>

* 远程仓库实际上和本地仓库没啥不同，纯粹为了7x24小时开机并交换大家的修改。
</font>
* [廖雪峰搭建git](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000) 
* [git搭建(较详细)](https://www.cnblogs.com/whthomas/p/3643974.html)


#### 10.IDEA中使用git
* [IDEA中安装git,并且导入项目](https://www.cnblogs.com/zbw911/p/6206689.html)
* [IDEA中使用git](https://blog.csdn.net/weixin_39274753/article/details/79722522)
* 

####10.git相比svn的优势
<font size=2>
* 当你使用git指令建立了本地仓库并从远程仓库将代码拷贝下来时,本地仓库就完全属于你一个人，基于你的本地仓库的操作，不会受到其他任何人的影响。
在SVN中如果我们提交代码可能会与其他人的代码发生冲突，我们回滚代码又可能会把其他人提交的代码回滚进来,这些都会打乱我们的节奏。

* 缓冲区的好处:
    *  1.多次提交到缓冲区的变动内容可以一次提交到本地仓库  
    *  2.缓冲区为我们提供了简单的反悔操作，在使用SVN进行平常的开发过程中，我们经常会遇到这样一种场景，我们修改了一段代码，然后提交了它，过了一段时间我们发现上次提交的内容有问题，应该被还原，此时只能进行回滚操作，并且在此之前这段不正确的代码可能已经被其他人检出了....。而在GIT中，当我们的代码提交到缓冲区后，缓冲区会为我们临时保存我们改动文件的快照，但此时并不会应用到本地仓库，当你觉得之前对某个文件的修改操作应该被取消时，直接git reset 撤回缓冲区add,即可
</font>








