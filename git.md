#GIT
## 分清git和github之间关系对理解很有帮助,主要是要理解git[原版讲解](http://wiki.jikexueyuan.com/project/git-tutorial/)下面是自己做的笔记。
git是一个分布式版本管理系统。github可以看作git上的一个远程库托管网站，并加入fork、pull Request等特色功能。
## 安装与配置
### 配置git，关联git与github。
* 注册github
* 安装git
    - 配置git
```
git config --global user.name "your name"
git config --global user.email "you@domain.com"
```
* 在github上新建一个仓库，以README.md为例，不需要自动生成该文件。

## 使用git创建repository
* 创建并CD到该目录(目录建议全英文)
```
$ mkdir learngit
$ cd learngit
$ pwd //pwd用于显示当前目录。
/user/document/learngit
```
创建完毕后会在该目录下自动创建一个`.git`目录，这个是Git用来跟踪管理repository。
* 添加文件到repository
    - 新建一个README.md文件，放入learngit根目录下。
    - 输入`git add README.md`:把文件添加到Git仓库，通知git开始追踪文件的更改
        + `git add .`:添加所有文件。
        + `git add 1.md 2.md`:可以多次add不同文件。
        + `git add -amend`:修改最后一次提交。
    - `git commit`告诉git，把文件提交到Git仓库。
        + `git commit -m "xxx"`:提交时添加修改说明，xxx为必填。
        + `git commit -a`:
* `git status`：显示当前仓库的状态，查看那些文件被修改过。
* `git diff 文件名`：显示具体修改过什么内容，git只能追踪文本文件的修改情况。

##  版本回退：
每当文件修改到一定程度时，可以使用commit保存一个快照。当文件出错时，可以选择从最近的一个commit中恢复，记住每次commit具体内容不那么容易，所有使用`git log`查看commit的历史记录。
```
git log --pretty=oneline
xxx(commit的id) “修改说明1”
xxx(commit的id) “修改说明2”
xxx(commit的id) “修改说明3”
```
* 使用`git reset`进行回退。
在git中使用`HEAD^`表示当前分支的commit，`HEAD^^`表示上个版本，`HEAD~10`表示上10个版本。
```
git reset --hard HEAD^
//或者commit id
git reset --hard commit的ID
```
此时再次使用`git log`,可以看到第3次修改已经没有了。若突然又不想修改了，在当前命令行没有关闭的情况下，找到第3次修改commit id，还是可以恢复。
```
git log --pretty=oneline
xxx(commit的id) “修改说明1”
xxx(commit的id) “修改说明2”
```
如果当前命令行已经关闭了怎么办？，当然还有一种方式恢复。使用`git reflog`该命令用来记录你的每一次命令,所以可以找到之前的`commit id`

## 工作区和暂存区
* 工作区：电脑中的一个目录，包含隐藏的`.git`文件夹。
* 暂存区:下图中的stage即为暂存区。
![工作区和暂存区](http://wiki.jikexueyuan.com/project/git-tutorial/images/git10.jpg)
    - 当我们使用`git add`把文件添加进去，就是将文件修改添加到暂存区。
    - 当我们使用`git commit`提交修改，实际上就是把暂存区的内容提交到当前分支。
例如在工作区已经add的文件，如果工作区没有任何修改则是“干净的”git status查看结果为：
```
$ git status
# On branch master
nothing to commit (working directory clean)
```
如果修改`git statu`s则显示为：
```
$ git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   readme.txt
```
工作区如果存在没有add的文件，例如1.md则，git status查看结果中有
```
$ git status
# On branch master
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
```
**如果在add之后修改的文件是不会commit到当前分支的。**

## 撤销修改
* 一种是工作区中的README.md发生了修改但还没有提交到暂存区，使用该命令就回到和版本库一模一样的状态。
使用`git checkout -- 文件名`：该命令实际上是将版本库中的文件替换工作区中的文件，这里有两种情况。
* 一种是工作区中的README.md发生了修改且添加到了暂存区。
要想撤销修改分为两步，
    - 使用`git reset HEAD 文件名`命令把暂存区中的修改撤销掉，把修改放回到工作区。
    - 使用`git checkout -- 文件名`将工作区中的修改撤销。

## 删除文件
当删除工作区的文件test.md时，此时版本库中的文件与工作区的文件不一样，使用`git status`可以看到：
```
$ git status
# On branch master
# Changes not staged for commit:
#   (use "git add/rm <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       deleted:    test.md
#
no changes added to commit (use "git add" and/or "git commit -a")
```
1.如若真的要删除该文件，使用`git rm test.md`删除版本库中的文件。
2.因操作失误导致的，使用`git checkout -- test.md`恢复文件。

## 使用和关联远程仓库
1. 在github中添加远程仓库，new repository。
2. 使用命令`git remote add origin (github上该仓库的https地址)`关联git和github。origin即为远程库的名字，是git中默认的远程库名字。
3. 使用`git push -u origin master`,将当前分支推送到`master`,`-u`参数表示将本地当前分支master与master分支关联起来，第一次推送以后，以后可以使用简化命令`git push origin master`

## 从远程库中克隆
之前是先有本地库，后有远程库。现在将顺序调换，先创建远程库，后创建本地库。
找到要克隆的地址，使用`git clone (github上仓库的https地址)`。

### 分支管理
原理：不同分支相当于为系统创建了不同的平行版本。每个分支之间相互不影响。
`HEAD`指针指向当前分支，默认有一个主分支`master`即`HEAD`指针指向`master`，每新建一个分支就会创建一个对应的指针，指向该分支。
![图解](http://wiki.jikexueyuan.com/project/git-tutorial/images/master-and-dev-ff.gif)
步骤：
1. 创建一个分支使用命令`git branch 分支名`。
2. 切换一个分支使用命令`git checkout 分支名`。
    - `git checkout -b 分支名`：表示创建并切换分支。
3. 查看当前分支使用命令`git branch`；显示所有分支，当前分支名前面以`*`号表示。
4. 对文件进行修改和提交
```
git add README.md
git commit -m "分支学习"
```
5. 合并分支
    - 切换到`master`分支，
    - 使用命令`git merge 分支名`，将新建的分支合并到`master`分支上。
删除分支：如果需要删除分支使用`git branch -d 分支名`删除该分支。

### 解决冲突
如果master分支和新建的feature1分支分别有了新的提交，这时就会产生冲突。
![图解](http://wiki.jikexueyuan.com/project/git-tutorial/images/branch6.png)
1. 这时git无法进行快速合并，会尝试合并各自文件的修改，遇到无法合并的修改需要手工进行修正。可以使用`git status`看到所不同的文件。使用`git diff 本地分支名 远程分支分`可以清晰的看见两个或多个文件的冲突在哪。
2. 在手工修改完成后，再次提交该文件，文件结构分支变为下图所示：
![图示](http://wiki.jikexueyuan.com/project/git-tutorial/images/branch7.png)
3. 使用带参数的`git log --graph`可以看到分支合并图。

### 分支关联策略
在合并分支时，使用Fast Forward模式，会丢失掉分支合并的信息，如果禁用Fast forward需要在使用`merge`的时候加上参数`--no-ff`
```
//创建并切换到dev分支
git checkout -b dev
//修改README.md文件并提交
git add readme.md
git commit -m "add merge"
//切换回master
git checkout master
//准备合并dev分支，禁用Fast Forword模式
git merge --no-ff -m "merge with no ff" dev
//使用git log 查看分支历史
git log --graph --pretty=oneline --abbrev-commit
*   7825a50 merge with no-ff
|\
| * 6224937 add merge
|/
*   59bc1cb conflict fixed 
```
![图示]（http://wiki.jikexueyuan.com/project/git-tutorial/images/branch8.png）

### bug修复分支
1. 修复bug时，通常新建一个bug分支，然后合并，最后删除；
2. 当手头工作没有完成，又不想提交时，先使用`git stash`把工作现场保存一下。
3. 使用`git stash list`查看历史记录，恢复时，有两种方案
    - 使用`git stash apply`恢复现场，但不会删除stash内容，需要用`git stash drop`进行删除
    - 使用`git stash pop`恢复现场，并删除stash内容
* 使用新分支feature开发新功能，在分支未合并时删除分支会提示错误，使用`git branch -D 分支名`；

### 多人协作
多人协作的工作模式通常为：
1. 可以试图使用`git push origin dev`推送自己的修改。
2. 如果失败，说明有人已经修改过这个分支，需要先用`git pull`尝试合并
    - `git pull`之前需要指定本地的dev与远程的 origin/dev相对应。使用命名`git branch --set-upstream dev origin/dev`
    - `–set-upstream-to` 在新版本git中已经替代了 –set-upstream， 并且后面跟随的两个参数要对调一下，如下面命令
    - 当使用git pull提示refusing to merge unrelated histories,使用`git merge <对应的分支> --allow-unrelated-histories`
    - 最好使用 `fetch`和`merge`替代`pull`

git branch --set-upstream-to origin/devtest devtest 
    
3. 如果合并有冲突，则解决后使用`git push origin dev`成功推送。
* 使用`git remote -v`可以查看远程库的信息,可以抓取和推送的origin的地址。
* 本地未经推送远程的分支，对他人就是不可见的。

## 标签管理
### 创建标签
标签也是版本库的一个快照，实际上与分支很类似，是一个commit上的一个指针，
* 使用`git tag`：默认创建标签在最新的commit中。
* `git tag -a 标签名 -m "说明文字"`。指定标签信息
* `git tag -s 标签名 -m "说明文字"`。可以用PGP签名标签
* 使用`git show 标签名`可以看到说明文字。

### 操作标签
* 删除标签`git tag -d 标签名`
* 推送标签到远程`git push origin 标签名`
* 一次性推送所有未经推送的标签`git push origin --tags`
* 删除远程标签
    1.先删除本地标签
    2.然后删除远程标签`git push origin :res/tags/标签名`

## github使用
1. fork一个项目，找到该项目，点击fork则在自己github账户上生成了对应的repository。
2. 然后将库克隆到本地需要从自己的库中克隆，不然没有推送权限。例如
`git clone https://github.com/自己账户名/bootstrap.git`
![github forking工作流](https://pic2.zhimg.com/80/dadbcef0bca3d2eb68ef6009f45361e2_hd.jpg)
3. 推送pull request给官方库作贡献。




##CRLF,LF区别
CRLF，LF是用来表示文本换行的方式。在windows下是CRLF，对应的字符是'\n';在Linux系统下是LF，对应的字符是'\r';
### 标准化和转化
* **标准化**：是指提交代码到git数据库（本地数据库） 时，将文本中的CRLF转换成LF的过程。
* **转换**：是指检出git数据库时，将文本中的LF转换成CRLF的过程。
### git中的core.autocrlf与core.safecrlf
* **core.autocrlf:**
* git提供了core.autocrlf的配置可以自动完成标准化和转换。
```
git config --global core.autocrlf  [true | input | false]  # 全局设置
git config --local core.autocrlf  [true | input | false] # 针对本项目设置
ture ：自动完成标准化和转换
input：只做标准化操作如果合并失败，则解决冲突，并在本地提交
false：提交和检出的代码都保持原有不变//git默认是false。
```
* **core.safecrlf:**
* 对于混合换行型文本文件，git提供了禁止或警告不可逆转的配置来防止错误的标准化和转换。
```
git config -- global core.safecrlf [true | input | false] #全局设置
true:禁止提交混合型换行文本文件
warn：提交时警告，但不会阻止 git add 命令
false：不禁止提交混合换行文本文件
```
### .gitattributes文件
core.autocrlf的配置依赖于每个开发者环境上的配置，这很难确保整个项目能正确配置。于是在规范项目中的换行符方面，还添加配置文件的方案。在项目的根目录下可以添加一个.gitattributes文件。它的优先级大于core.autocrlf的设置，他类似于.gitignore文件，随提交修改生效，在一个项目中维持一分相同的配置，所以能够避开每个开发人员配置不同的问题。
```
每行的基本形式：
filter atrr1 atrr2...
filter:代表匹配文件的通配符。
atrr：代表属性。[.text | .eol |.binary]
```
[svn教程](https://my.oschina.net/happyBKs/blog/375334)
[git常用命令](http://ife.baidu.com/note/detail/id/577)
