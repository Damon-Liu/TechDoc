
### SSH配置 ###  
`ssh-keygen -t rsa -C "your_email@example.com"` //git-bash 生成密钥对  
`cat /tmp/id_rsa.office.pub >> /home/git/.ssh/authorized_keys` //添加客户端公钥到Git服务器（GitHub有专门页面添加公钥内容）  

### 设置 ###  
`git config --global user.name "Your Name"` 设置全局本地仓库的用户名  
`git config --global user.email "email@example.com"` 设置全局本地仓库的Email  
`git config --global color.ui true` 启用git终端样式  
`git config --global alias.<st> <status>` 配置别名  

Repository库的设置，Repository文件夹下.git文件夹内config文件  
git本机设置，用户主目录.gitconfig文件（默认隐藏）  

### 仓库 ###  
`git init` 把一个目录初始化为Repository  
`git add filename` 将一个文件添加到git Repository中。注意：第一次添加的文件和修改过的文件都要先`add`才能`commit`（本质上将修改由工作区放入暂存区）  
`git commit -m note` 将文件提交到Repository（某一分支中），`-m`是本次提交的说明  
`git reset --hard HEAD^` 或 `git reset --hard commit-id-headN` 使Repository滚动到某个版本，commit-id-headN表示版本号的前n位（回退要求没有推送到远程仓库中）  
`git reset HEAD filename` 把暂存区退回到工作区，HEAD表示使用最新版本作为工作区的文件  
`git reflog` 显示每一次提交命令  
`git checkout --filename` 撤销文件修改，如果文件还没有add，以Repository为标准还原工作区文件。如果文件已经add则将工作区还原为add之前的状态（如果本地文件被误删，可以用这个还原）  
`git rm filename` 从git中删除一个文件  

### 一般 ###  
`git diff filename` 查看文件的差异  
`git status` 显示Repository状态，如有还未提交的修改  
`git log`显示提交记录 选项`--pretty=oneline` 在一行内显示一次提交记录的精简信息 `--graph` 以图形展示git日志 `--abbrev-commit` 仅显示SHA-1的前几个字符  
`git log -1` 查看最后一次提交  

### 远程仓库 ###  

`git remote add origin git@github.com:username/repository-name.git` 建立与远程账户的关联(origin是本地表示远程仓库的默认名称)  
`git push -u origin master` 将本地master分支推送到远程，并建立本地master与远程master(即origin)的关联。`-u`用于第一次建立关联，后续提交不需要此选项直接使用`git push origin master`即可。可以把master改为本地其它分支  
`git push` 会将当前分支推送到已经建立关系的远程分支，建立关系方法见下面命令  
`git branch --set-upstream localbranch origin/<branch>` 上面命令`-u`实现的就是关联本地分支与远程分支（**命令过时**）  
`git branch --set-upstream-to origin/<branch>` 使用这个命令代替上面过时的命令（pull前也需要将本地分支与远程分支进行关联）  
`git remote remove origin` 删除远程关联  
`git clone git@github.com(host):username(repository)/repository-name.git` 克隆远程Repository到名为repository-name的文件夹(通过SSH)  
`git clone http://xxx.com/user/projname` 克隆代码到新目录，将在执行此命令的文件夹下新建名为`projname`的文件夹并将代码clone到这个文件夹内(通过http或https)  
`git remote` 查看远程Repository信息 `-v` 显示详细信息，如库的权限push还是fetch  
`git pull` 拉取远程库 `--allow-unrelated-histories` 允许两个没有共同base的分支合并  

### 分支 ###  
`git checkout -b dev` 创建名为dev的新分支并将当前分支切换为dev，等价于`git branch dev`与`git checkout dev`连用  
`git checkout -b dev origin/dev` 以远程dev分支为基础创建并切换到本地dev分支  
`git branch` 查看当前所有分支，名称前面有`*`表示当前分支  
`git checkout branch-name` 切换分支  
`git merge branch2` 将branch2合并到当前分支  
`-m` 给合并添加注释  
`--no-ff` 禁用FastForward合并（保留分支信息，可以看出以前做过的合并）  
`--allow-unrelated-histories` 允许两个没有共同base的分支合并  
`git branch -d dev` 删除名为dev的分支 `-D` 强行删除一个还没有合并的分支  
`git branch`  

###  工作区 ###  
`git stash` 把当前工作区缓存起来（隐藏当前正在进行的修改）  
`git stash list` 列出所有缓存的工作区快照  
`git stash apply <stash@{0}>` 还原工作区快照  
`git stash drop <stash@{0}>` 删除工作区快照  
`git stash pop <stash@{0}>` 等价于上两条命令连用，还原快照并在还原后删除快照  

### 标签 ###  
`git tag v1.0` 给当前分支当前状态打个标签（相当于快照）  
`git tag` 列出所有tag  
`git tag v1.0 <commit-id>` 给指定commit打标签  
`git show <tagname>` 显示一个tag的信息  
`git tag -a <tagname> -m "some note"` `-m` 添加标签说明  
`git tag -s <tagname> -m "some note"` 用PGP算法签名标签  
`git tag -d <tagname>` 删除标签  
`git push origin :refs/tags/<tagname>` 接上条命令，同时从远程删除标签  
`git push origin <tagname>` 推送标签到远程  
`git push origin --tags` 推送所有未提交到远程的标签  

**版本**  
`HEAD` 当前版本  
`HEAD^` 上一个版本  
`HEAD^^` 上上一个版本  
`HEAD~100` 前100个版本  

### 常见约定 ###  

1. 使用dev分支（名称任意）开发，开发完成后合并到master，不要直接在master上开发（方便多人合作）  
2. master分支用于发布版本（如1.0，1.2这样的版本分支应该基于master来发布）  
3. 开发新功能或修复bug，针对每个feature或bug创建新分支，完成后合并到dev（不是master）  
