#  Git 学习笔记

时间：2018.11.15

---
**（一）git提交文件到仓库——多次add,一次commit**

为什么Git添加文件需要add，commit一共两步呢？因为commit可以一次提交很多文件，所以你可以多次add不同的文件，比如：

```
git add file1.txt
git add file2.txt file3.txt
git commit -m "add 3 files."
```


**小结**

现在总结一下今天学的两点内容：

初始化一个Git仓库，使用git init命令。

添加文件到Git仓库，分两步：

使用命令git add <file>，注意，可反复多次使用，添加多个文件；

使用命令git commit -m <message>，完成。

---


**（二）当本地修改了文件时候，如何提交与版本控制**
```
git status #查看当前状态
git diff readme.txt # 查看文件readme.txt哪几行发生了增减
```
**小结**

要随时掌握工作区的状态，使用git status命令。

如果git status告诉你有文件被修改过，用git diff可以查看修改内容。

---

**（三）穿梭未来--回到过去**


```HEAD```指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令```git reset --hard commit_id```。

穿梭前，用```git log```可以查看提交历史，以便确定要回退到哪个版本。

要重返未来，用```git reflog```查看命令历史，以便确定要回到未来的哪个版本。

---
**（四）git工作区与缓存区**


工作区是当前的目录，缓冲区是可以保存多次文件修改的地方，使用```git add <file>```将工作区的修改内容提交到缓冲区。

版本库是```.git```文件，利用```git commit -m "comment"```可以将缓冲区的所有修改一次性全部提交到版本库```.git```中。

在Git中，用```HEAD```表示版本库当前版本，也就是最新的提交```1094adb...```
(**实际上```HEAD```指向的```master```分支，```master```指向最新的一次提交```1094adb...```**)，注意我的提交ID和你的肯定不一样，上一个版本就是```HEAD^```，上上一个版本就是```HEAD^^```，当然往上100个版本写100个^比较容易数不过来，所以写成```HEAD~100```。

---
**（五）管理修改**

用```git diff HEAD -- readme.txt```命令可以查看版本库里面最新版本和工作区的区别

**小结**

第一次修改 -> ```git add``` -> 第二次修改 -> ```git add``` -> ```git commit```
好，现在，把第二次修改提交了，

然后开始小结。
理解了Git是如何跟踪修改的，每次修改，如果不用git add到暂存区，那就不会加入到commit中。
---

**（六）撤销修改**

命令```git checkout -- readme.txt```意思就是，把```readme.txt```文件在工作区的修改全部撤销，这里有两种情况：

一种是```readme.txt```自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

一种是```readme.txt```已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次```git commit```或```git add```时的状态。

**小结**

```git checkout -- file```命令中的```--```很重要，没有```--```，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到git ```checkout```命令。

---

**（七）删除本地文件后如何与版本库同步**

现在你有两个选择，一是确实要从版本库中删除该文件，那就用命令```git rm```删掉，并且```git commit```：

```
$ git rm test.txt
rm 'test.txt'

$ git commit -m "remove test.txt"
[master d46f35e] remove test.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt
 ```
 **小结**

命令```git rm <file>```用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。

---


**（八）远程仓库**

**1.添加远程库**

要将**本地的版本库**关联一个**远程库(一般用origin表示远程库)**，使用命令```git remote add origin git@server-name:path/repo-name.git```；

关联后，使用命令```git push -u origin master```第一次推送本地版本库```master```分支的所有内容；

说明：我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

此后，每次本地提交后，只要有必要，就可以使用命令```git push origin master```推送最新修改。

**2.从远程库克隆**

Git支持多种协议，包括```https```，但通过```ssh```支持的原生```git```协议速度最快。

如：```git clone git@github.com:michaelliao/gitskills.git```即可。

---
**（九）分支管理**

**1.创建与合并分支**

首先，我们创建dev分支，然后切换到dev分支：

```
git checkout -b dev

Switched to a new branch 'dev'

git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：

$ git branch dev
$ git checkout dev
Switched to branch 'dev'
然后，用git branch命令查看当前分支：

$ git branch
* dev
  master
git branch命令会列出所有分支，当前分支前面会标一个*号。

然后，我们就可以在dev分支上正常提交，比如对readme.txt做个修改，加上一行：

Creating a new branch is quick.
然后提交：

$ git add readme.txt 
$ git commit -m "branch test"
[dev b17d20e] branch test
 1 file changed, 1 insertion(+)
现在，dev分支的工作完成，我们就可以切换回master分支：

$ git checkout master
Switched to branch 'master'
切换回master分支后，再查看一个readme.txt文件，刚才添加的内容不见了！因为那个提交是在dev分支上，而master分支此刻的提交点并没有变：


现在，我们把dev分支的工作成果合并到master分支上：

$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
git merge命令用于合并指定分支到当前分支。合并后，再查看readme.txt的内容，就可以看到，和dev分支的最新提交是完全一样的。

注意到上面的Fast-forward信息，Git告诉我们，这次合并是“快进模式”，也就是直接把master指向dev的当前提交，所以合并速度非常快。

当然，也不是每次合并都能Fast-forward，我们后面会讲其他方式的合并。

合并完成后，就可以放心地删除dev分支了：

$ git branch -d dev
Deleted branch dev (was b17d20e).
删除后，查看branch，就只剩下master分支了：

$ git branch
* master
因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在master分支上工作效果是一样的，但过程更安全。
```

**小结**

Git鼓励大量使用分支：

查看分支：```git branch```

创建分支：```git branch <name>```

切换分支：```git checkout <name>```

创建+切换分支：```git checkout -b <name>```

合并某分支到当前分支：```git merge <name>```

删除分支：```git branch -d <name>```

---

**2.解决冲突**

我们可以直接查看readme.txt的内容：
```

Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```
Git用```<<<<<<<，=======，>>>>>>>```标记出不同分支的内容，我们修改如下后保存：

```Creating a new branch is quick and simple.```

再提交：
```
$ git add readme.txt 
$ git commit -m "conflict fixed"
[master cf810e4] conflict fixed
```
现在，master分支和feature1分支使用下面命令查看：
``` git log --graph --pretty=oneline --abbrev-commit```

或者是``` git log --graph```查看解决矛盾图。

---

**3.分支合并策略**

```--no-ff```表示普通合并，合并后可以看得到分支，而 fast-farwaef模式看不到分支，

格式：```git merge --no-ff -m "merge with no-ff" dev```

---

**4.bug分支**
```
Bug分支
软件开发中，bug就像家常便饭一样。有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

当你接到一个修复一个代号101的bug的任务时，很自然地，你想创建一个分支issue-101来修复它，但是，等等，当前正在dev上进行的工作还没有提交：

$ git status
On branch dev
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   hello.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   readme.txt
并不是你不想提交，而是工作只进行到一半，还没法提交，预计完成还需1天时间。但是，必须在两个小时内修复该bug，怎么办？

幸好，Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：

$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
现在，用git status查看工作区，就是干净的（除非有没有被Git管理的文件），因此可以放心地创建分支来修复bug。

首先确定要在哪个分支上修复bug，假定需要在master分支上修复，就从master创建临时分支：

$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git checkout -b issue-101
Switched to a new branch 'issue-101'
现在修复bug，需要把“Git is free software ...”改为“Git is a free software ...”，然后提交：

$ git add readme.txt 
$ git commit -m "fix bug 101"
[issue-101 4c805e2] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
修复完成后，切换到master分支，并完成合并，最后删除issue-101分支：

$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git merge --no-ff -m "merged bug fix 101" issue-101
Merge made by the 'recursive' strategy.
 readme.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
太棒了，原计划两个小时的bug修复只花了5分钟！现在，是时候接着回到dev分支干活了！

$ git checkout dev
Switched to branch 'dev'

$ git status
On branch dev
nothing to commit, working tree clean
工作区是干净的，刚才的工作现场存到哪去了？用git stash list命令看看：

$ git stash list
stash@{0}: WIP on dev: f52c633 add merge
工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：

```
```
一是用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除；

另一种方式是用git stash pop，恢复的同时把stash内容也删了：

$ git stash pop
On branch dev
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   hello.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   readme.txt

Dropped refs/stash@{0} (5d677e2ee266f39ea296182fb2354265b91b3b2a)
再用git stash list查看，就看不到任何stash内容了：

$ git stash list
你可以多次stash，恢复的时候，先用git stash list查看，然后恢复指定的stash，用命令：

$ git stash apply stash@{0}
```

**小结**

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场```git stash```一下，然后去修复bug，修复后，再```git stash pop```，

---

**5.feature 分支**

```
添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。

现在，你终于接到了一个新任务：开发代号为Vulcan的新功能，该功能计划用于下一代星际飞船。

于是准备开发：~~++*此时我们是在dev分支上进行的开发*++~~。

$ git checkout -b feature-vulcan
Switched to a new branch 'feature-vulcan'
5分钟后，开发完毕：

$ git add vulcan.py

$ git status
On branch feature-vulcan
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   vulcan.py

$ git commit -m "add feature vulcan"
[feature-vulcan 287773e] add feature vulcan
 1 file changed, 2 insertions(+)
 create mode 100644 vulcan.py
切回dev，准备合并：

$ git checkout dev
一切顺利的话，feature分支和bug分支是类似的，合并，然后删除。

但是！

就在此时，接到上级命令，因经费不足，新功能必须取消！

虽然白干了，但是这个包含机密资料的分支还是必须就地销毁：

$ git branch -d feature-vulcan
error: The branch 'feature-vulcan' is not fully merged.
If you are sure you want to delete it, run 'git branch -D feature-vulcan'.
销毁失败。Git友情提醒，feature-vulcan分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用大写的-D参数。。

现在我们强行删除：

$ git branch -D feature-vulcan
Deleted branch feature-vulcan (was 287773e).
终于删除成功！
```

**小结**

开发一个新```feature```，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过```git branch -D <name>```强行删除。

---

**6.多人协作**

多人协作的工作模式通常是这样：

1.首先，可以试图用```git push origin <branch-name>```推送自己的修改；

2如果推送失败，则因为远程分支比你的本地更新，需要先用```git pull```试图合并；

3如果合并有冲突，则解决冲突，并在本地提交；

4没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！

如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

**小结**

1查看远程库信息，使用```git remote -v```；

2本地新建的分支如果不推送到远程，对其他人就是不可见的；

3从本地推送分支，使用```git push origin branch-name```，如果推送失败，先用```git pull```抓取远程的新提交；

4在本地创建和远程分支对应的分支，使用```git checkout -b branch-name origin/branch-name```，本地和远程分支的名称最好一致；

5建立本地分支和远程分支的关联，使用```git branch --set-upstream branch-name origin/branch-name```；

6从远程抓取分支，使用```git pull```，如果有冲突，要先处理冲突。

---

**7.多人协作-rebase**

[link](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0015266568413773c73cdc8b4ab4f9aa9be10ef3078be3f000)

**小结**

rebase操作可以把本地未push的分叉提交历史整理成直线；

rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比

---

**8.打tag以及修改tag**

**1.打标签tag**

命令```git tag <tagname>```用于新建一个标签，默认为HEAD，也可以指定一个```commit id```；

命令```git tag -a <tagname> -m "blablabla..."```可以指定标签信息；

命令```git tag```可以查看所有标签。

标签不是按时间顺序列出，而是按字母排序的。可以用```git show <tagname>```查看标签信息。

注意：标签总是和某个```commit```挂钩。如果这个```commit```既出现在````master```分支，又出现```在dev```分支，那么在这两个分支上都可以看到这个标签.

**2.修改tag**

删除标签 ```git tag -d v0.1```

如果要推送某个标签到远程，使用命令```git push origin <tagname>```：

```
$ git push origin v1.0
Total 0 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
 * [new tag]         v1.0 -> v1.0
或者，一次性推送全部尚未推送到远程的本地标签：

$ git push origin --tags
Total 0 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
 * [new tag]         v0.9 -> v0.9
如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：

$ git tag -d v0.9
Deleted tag 'v0.9' (was f52c633)
然后，从远程删除。删除命令也是push，但是格式如下：

$ git push origin :refs/tags/v0.9
To github.com:michaelliao/learngit.git
 - [deleted]         v0.9
 ```
要看看是否真的从远程库删除了标签，可以登陆GitHub查看。

**小结**

命令```git push origin <tagname>```可以推送一个本地标签；

命令```git push origin --tags```可以推送全部未推送过的本地标签；

命令```git tag -d <tagname>```可以删除一个本地标签；

命令```git push origin :refs/tags/<tagname>```可以删除一个远程标签。

---

**九.github使用**

在GitHub上，可以任意Fork开源仓库；

自己拥有Fork后的仓库的读写权限；

可以推送pull request给官方仓库来贡献代码

---
参考：[廖雪峰的博客](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137586810169600f39e17409a4358b1ac0d3621356287000)