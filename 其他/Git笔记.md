## 版本控制

开发过程中用于管理我们对文件、目录或工程等内容的修改历史，方便查看更改历史记录。

- 实现跨区域多人协同开发
- 并行开发

常见版本控制工具

- Git
- SVN

## Git环境配置

安装好Git之后有三个程序

- **Git Bash**：Unix和Linux风格命令行，使用最多，推荐最多
- **Git CMD**：Windows风格命令行
- **Git GUI**： 图形化Git

基本Linux命令（平时一定要多使用）：

```Linux
(1)、cd: 改变目录
(2)、cd.. 回退到上一目录
(3)、pwd 显示当前所在目录路径
(4)、ls(ll) 查看当前目录所有文件
(5)、touch 新建一个文件
(6)、rm 删除一个文件
(7)、mkdir 新建一个目录
(8)、rm -r 删除一个文件夹
(9)、mv 移动文件
(10)、reset 重新初始化终端/清屏
(11)、clear 清屏
(12)、history 查看历史命令
(13)、help 帮助
(14)、exit 退出
(15)、# 表示注释
```

**Git配置**：

查看不同级别的配置文件

```Linux
#查看系统config
git config --system --list
#查看当前用户(global)配置
git config --global --list
```

**Git相关配置文件**

(1)、Git\etc\gitconfig：Git安装目录下的gitconfig

(2)、C:\Users\朱小龙\.gitconfig：用户定义config

```Linux
git config --global user.name "ferron"
git config --gloabl user.email "2963624004@qq.com"
```

## Git基本理论

**工作区域**

Git本地有三个工作区域，工作目录(Working Directory)，暂存区(Stage/Index)，资源库(Respository或Git Directory)。如果加上远程的git仓库(Remote Directory)，其使用代码如下：

```Linux
#提交
git add files: Working Directory->Stage(Index), git add . 表示将所有文件暂存
git commit: Stage(Index)->History
git push: History->Remote Directory

#下载
git pull: Remote Directory->History
git reset: History->Stage(Index)
git checkout: Stage(Index)->Working Directory
```

**Git工作流程**

(1)、在工作目录中添加、修改文件；

(2)、将需要进行版本管理的文件放入暂存区域；

(3)、将暂存区域的文件提交到git仓库。

因此，git管理的文件有三种状态：已修改(modified)，已暂存(staged)，已提交(committed)。

## Git项目搭建

**本地仓库搭建**

```Linux
#创建全新的Git仓库
git init
#克隆全程目录：将远程服务器上的仓库完全镜像一份至本地
git clone [url]
```

## Git文件操作

**文件的四种状态**

- **Untracked**：未跟踪，此文件在文件夹找那个，但并没有加入到git库，不参与版本控制，通过<font color=red>git add</font>将状态变成**Staged**。
- **Unmodify**：文件已经入库，未修改，即版本库中的文件快照内容与文件夹中的完全一致，这种类型的文件有两种去处，如果它被修改，就变成**Modified**。如果使用<font color=red>git rm</font>移出版本库，则成为**Untracked**文件。
- **Modified**：文件已修改，仅仅是修改，并没有进行其他的操作，这个文件也有两种去处，通过<font color=red>git add</font>可进入暂存状态，而使用<font color=red>git checkout</font>则丢弃修改过，返回**Unmodify**状态，这个<font color=red>git checkout</font>即从库中取出文件，覆盖当前修改。
- **Staged**：暂存状态，执行<font color=red>git commit</font>则将修改同步到库中，这时库中的文件和本地文件又变为一致，文件为**Unmodify**状态，执行<font color=red>git reset HEAD filename</font>取消暂存，文件状态为**Modified**。

**操作代码**

```Linux
#查看所有文件状态
git status
#查看制定文件状态
git status [filename]
#添加所有文件到暂存区
git add .
#提交暂存区中的内容到本地仓库 -m:提交信息
git commit -m"提交信息"
```

**忽略文件**

有些时候我们不想把某些文件纳入版本控制，比如数据库文件，临时文件，设计文件等

在主目录下简历".gitignore"文件，此文件有如下规则：

(1)、忽略文件中的空行或以#开始的行会被忽略

(2)、可以使用Linux操作符，例如：星号(*)代表任意多个字符，问号(?)代表一个字符，方括号([abc])代表可选字符范围，大括号({string1,string2,...})代表可选的字符串等

(3)、如果名称的最前面有一个感叹号(!)，表示例外规则，将不被忽略

(4)、如果名称的最前面是一个路径分隔符(/)，表示要忽略的文件在此目录下，而子目录的文件不忽略

(5)、如果名称的最后面是一个路径分隔符(/)，表示要忽略的是此目录下该名称的子目录，而非文件(默认文件或目录都忽略)

```
#为注释
*.txt          #忽略所有.txt结尾的文件，这样的话上传时就不会被选中
!lib.txt       #但lib.txt文件除外
/temp          #仅忽略项目根目录吓得TODO文件，不包括其他目录temp
bulid/         #忽略bulid/目录下的所有文件
doc/*.txt      #忽略doc/note.text，但不包括doc/server/arch.txt
```

## 使用码云

(1)、登录注册码云

(2)、设置本机绑定SSH公钥，实现免密码登录

```Linux
#进入 C:\Users\Administrator\.ssh目录
#生成公钥
ssh-keygen
```

(3)、将公钥信息public key添加到码云账户中

## Idea中集成Git

(1)、新建项目，绑定git

- 将我们远程的git文件目录拷贝到项目中即可！
- 注意观察Idea中的变化。

(2)、修改文件，使用Idea操作git

- 添加到暂存区
- commit提交
- push到远程仓库

(3)、

## Git分支

分支在Git中算较难，如果两个分支互不打扰，那么没有影响，如果两个分支合并了，就需要处理一些问题。

**Git分支中常用命令**

```Linux
#列出所有本地分支
git branch
#列出所有远程分支
git branch -r
#新建一个分支，但仍停留在当前分支
git checkout [branch-name]
#新建一个分支，并切换到该分支
git checkout -b [branch]
#合并指定分支到当前分支
git merge [branch]
#删除分支
git branch -d [branch-name]
#删除远程分支
git push origin --delete [branch-name]
git branch -dr [remote/branch]
```

多个分支如果并行执行，就会导致我们代码不冲突，也就是同时存在多个版本。

如果冲突了，就需要协商，解决的办法就是重新提交，选择要保留他的代码还是你的代码。





