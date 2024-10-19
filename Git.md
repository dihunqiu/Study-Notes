# 安装和初始化配置

配置用户名与邮箱

- git config --global user.name "用户名"
- git config --global email 邮箱地址

|               |                            |
| :-----------: | :------------------------: |
| 省略（Local） | 本地配置，只对本地仓库有效 |
|   --global    |   全局配置，所有仓库有效   |
|   --system    |  系统配置，对所有用户生效  |

配置及其他命令

- 保存用户名及密码：git  config --global credential.helper store
- 查看git配置信息：git config --global --list

# 新建仓库

## 方式1-在本地新建仓库：git init

- git init name：在当前文件夹下创建一个名为name的文件夹并初始化为git仓库

## 方式2-从远程服务器克隆一个已经存在的仓库：git clone

# 工作区域和文件状态

## 三种工作区域

- 工作区（Working Directory）：.git所在的目录。实际操作的目录。
- 暂存区（Staging Area/Index）：.git/index。用于临时存放即将提交的修改内容。
- 本地仓库（Local Repository）：.git/objects。Git存储代码和版本信息的主要位置。

1. git add：将文件提交到暂存区。
2. git commit：将文件保存到本地仓库。

## 四种文件状态

- 未跟踪（Untrack）：新创建未被Git管理起来的文件。
- 为修改（Unmodified）：已经被Git管理但文件内容还未发生变化。
- 已修改（Modified）：已修改但还未添加到暂存区的文件。
- 已暂存（Staged）：已修改并添加到暂存区的文件。

# 添加和提交文件

1. git init 创建仓库
   - git status 查看仓库的状态
2. git add 添加到暂存区
   - git ls-files 查看暂存区中的文件。

3. git commit 提交

## 使用通配符来提交文件

- git add *.txt 将所有txt文件添加到暂存区。（其余文件同理）

- git add . 将当前目录工作区种所有文件添加到暂存区。

- git log 查看提交记录。可以使用--oneline来查看简洁的提交记录。

# git reset回退版本

| git reset后缀含义 |                                                              |
| :---------------: | :----------------------------------------------------------: |
|      --soft       |       回退到某一版本并保留工作区和暂存区的所有修改内容       |
|      --hard       |      回退到某一版本并且丢弃工作区和暂存区的所有修改内容      |
|      --mixed      | 回退到某一版本，保留工作区的所有修改内容，丢弃暂存区的工作内容 |

# 使用git diff查看差异

- git diff 查看工作区与暂存区之间的差异内容。它会显示发生更改的文件以及更改的详细信息。
- git diff HEAD 查看工作区与版本库之间的差异。
- git diff --cached 查看暂存区与版本库之间的差异。
- git diff 版本ID 版本ID 查看两个版本之间的差异。
  - git diff HEAD~ HEAD 查看当前版本与上一版本之间的差异。HEAD用来表示最新版本，HEAD~来表示上一版本。HEAD~数字，可以表示HEAD之前的几个版本。
  - git diff HEAD~ HEAD 文件名 查看两个版本种指定文件的差异。

- git diff <branch_name> <branch_name> 查看两个分支之间的差异。

# 使用git rm删除文件

- 删除方式1：rm 文件名 删除指定文件。删除文件后需要再次将文件提交到暂存区，以删除暂存区中的文件。
- 删除方式2：git rm 文件名 将指定文件从工作区和暂存区中删除。
  - git rm --cached 文件名 将指定文件将指定文件从暂存区中删除，但保留在当前工作区中。
  - git rm -r * 递归删除某个目录下的所有子目录和文件。
- 删除完成后应提交以更新版本库。

# gitignore忽略文件

## 作用

忽略掉一些不该加入版本库的文件，使得仓库体积更小，更加干净。

## 应该忽略哪些文件

- 系统或软件自动生成的文件。
- 编译结果产生的中间文件和结果文件。
- 运行时生成的日志文件、缓存文件、临时文件。
- 涉及身份、密码、口令、密钥等敏感信息的文件。

## 使用方法

- 创建.gitignore文件，将需要忽略的文件添加到文件中。例：*.log忽略所有log文件。

- 该文件无法对已添加到版本库中的文件生效，需先删除版本库中的文件才可使用。
- 该文件可以对文件夹使用，格式为“文件夹名/”。

## .gitignore文件的匹配规则

- 空行或者以#开头的行会被Git忽略。一般空行用于可读性的分割，#一般用作注释。

- 是同标准的Blob模式匹配，例如：

  - 型号*通配任意个字符
  - 问好？通配单个字符
  - 中括号[]表示匹配列表中的单个字符，比如：[abc]表示a/b/c

- 两个星号**表示匹配任意的中间目录。

- 中括号可以使用段中线连接，比如：[0-9]表示任意一位数字，[a-z]表示任意一位小写字母

- 感叹号！表示取反。

  ~~~.gitignore
  # 忽略所有的.a文件
  *.a
  
  # 跟踪所有的lib.a文件，即便你在前面忽略了.a文件
  !lib.a
  
  # 只忽略当前文件目录下的T0D0文件，而不忽略subdir/T0D0
  /T0D0
  
  # 忽略任何目录下名为build的文件夹
  build/
  
  # 忽略 doc/notes.txt,但不忽略 doc/server/arch.txt
  doc/*.txt
  
  # 忽略doc/目录及其所有子目录下的.pdf文件
  doc/**/*.pdf
  ~~~

  # SSH配置和克隆仓库
  
  ## SSH配置
  
  1. 回到根目录，进入.ssh文件夹。`cd \` -> `cd .shh`
  
  2. 使用ssh-keygen命令生成密钥，`ssh-keygen -t rsa -b 4096`， -t rsa代表指定协议为rsa
  
     - 如果为第一次使用该命令（初次配置密钥），直接回车即可自动生成id_rsa密钥文件。
     - 如果已经配置过ssh密钥，直接回车会覆盖之前的密钥文件且过程不可逆，建议输入一个新的文件名以防止密钥被覆盖。
  
  3. 输入密码完成密钥生成。
  
     - 生成的两个文件，有.pub后缀的为公钥文件，无后缀的为私钥文件。
  
  4. 回到GitHub，单击头像选择Settings，选择SSH and GPGkeys。
  
  5. 选择New SSH key，将公钥复制并命名完成添加。
  
     - 如果是新创建密钥则已经完成创建。
  
     - 如果之前已经创建过，需要指定需要使用的密钥。使用`tail -5 config`命令创建一个config文件，并将以下5行代码添加到文件中。
  
       ~~~config
       # github
       Host github.com
       HostName github.com
       PreferredAuthentications publickey
       IdentityFile ~/.ssh/密钥名
       ~~~

## 克隆仓库

使用`git clone ssh`将仓库克隆到本地仓库。

## 同步本地仓库与远程仓库

通过使用`git push`命令将本地仓库内容推送到远程仓库

- git push <remote> <branch>
- git pull <remote>

# 关联本地仓库和远程仓库

1. git remote add origin(远程仓库别名) ssh密钥	创建远程仓库
   - git remote -v 查看我们仓库当前所对应的远程仓库的别名和地址。
2. git branch -M main    指定分支的名称为main（默认分支为main时可以省略）
3. git push -u origin main[:main]    将本地仓库的main分支与远程仓库的main分支关联起来(本地分支与远程分支名称相同时可以省略只一个)。

1. git pull <远程仓库名><远程分支名>:<本地分支名> 将远程仓库的分支拉取到本地并进行合并。

# Gitee的使用和GitLab本地化的部署

# 分支简介和基本操作

- git branch    查看当前仓库的分支
- git branch 分支名    创建新的分支
- git checkout 分支名    切换到其他分支
  - git checkout命令还可以用来回复文件或目录到之前的某一状态。
  - 当分支与文件冲突时会优先切换分支
- git switch 分支名    切换分支（为了语义明确，此命令专门用来切换分支）
- git merge 分支名     将该分支合并到当前分支中
- git log --graph --oneline --decorate --all   查看分支图
- git branch -d 分支名    删除已经合并的分支
- git branch -D 分支名    删除分支

# 解决合并冲突

合并冲突：当两个分支修改了相同文件的相同内容会发生合并冲突。

- git commit -a -m    同时暂存和提交

发生冲突后，若要继续合并可以修改文件内容后合并，也可以使用git merge --abort中止合并。

# 回退和rebase

- git rebase 分支名    将分支变基到当前分支

Merge与Rebase的优缺

- Merge
  - 优：不会破坏原分支的提交历史，方便回溯和查看。
  - 缺：会产生额外的提交节点，分支图比较复杂。
- Rebase
  - 不会新增额外的提交记录，形成线性历史，比较直观和干净。
  - 会改变提交历史，改变了当前分支branch out的提交节点。应避免在共享分支时使用。

# 分支管理和工作流模型

- git tag 版本号     标记版本号
- 版本号规则
  - 主版本（Major Version）：主要的功能变化或重大更新。
  - 次版本（Minor Version）：一些新的功能、改进和更新，通常不会影响现有功能。
  - 修订版本（Patch Version）：一些小的bug修复，安全漏洞补丁等。通常不会更改现有功能和接口。
