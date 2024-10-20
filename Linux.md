

# 文件

文件由**文件的内容**+**文件的属性（元数据）**组成

# 基本命令

## 用户管理

**添加用户**：`adduser 用户名`

**设置密码**：`passwd 用户名` 随后输入密码

**删除用户**：`userdel -r 用户名`

**获取ip地址**：`ip addr`

**确认路径位置**：`pwd`

**切换路径**：`cd 路径`

ls	显示当前路径下的文件或者目录名称

ls -l	显示当前路径下的文件或者目录更详细的属性信息

touch	建立普通文件

mkdir	创建目录

## ls指令

**语法**：ls [选项][目录或文件]

**功能**：对于目录，该命令列出该目录下的所有子目录与文件。对于文件，将列出文件名以及其他信息。

**常用选项**：

- -a  列出目录下的所有文件，包括以.开头的隐藏文件。
- -d  将目录像文件一样显示，而不是显示其下的文件。如：ls -d 指定目录。
- -i  输出文件的i节点的索引信息。如：ls -ai 指定文件。
- -k  以k字节的形式表示文件的大小。如ls -alk 指定文件。
- -l  列出文件的详细信息
- -n  用数字的UID，GID代替名称。（介绍UID，GID）。
- -F  在每个文件名后附上一个字符以说明该文件的类型，"*"表示可执行的普通文件；"/"表示目录；"@表示符号对接；"|" 表示FIFOS；"="表示套接字（sockets）。（目录类型识别）
- -r  对目录反向排序。
- -t  以时间排序
- -s  在文件名后输出该文件的大小。（大小排序，如何找到目录下最大的文件）
- -R 列出所有子目录下的文件。（递归）
- -1 一行只输出一个文件。

## pwd命令

**语法**：pwd

**功能**：显示用户当前所在的目录。

## cd命令

**语法**：cd 目录名

**功能**：改变工作目录。将当前目录改变到指定目录下。

## tree命令

- tree命令的安装：`yum install -y tree`

`tree 目录名`，以树状的形式显示目录。

`tree .`以当前目录为根树状显示目录。

