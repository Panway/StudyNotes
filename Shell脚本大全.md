
``` shell
//base64解码
echo aHR0cDovL3d3NC5zaW5haW1nLmNuL2xhcmdlLzNmYWIzNDBmZ3cxZjV6YWZrM21ydmoyMGdvMG04bXk3LmpwZw== | base64 --decode

一次性创建Git仓库并提交
git init &&git add . &&git commit -m '1st'

将某目录下全部文件的所有者改为本用户
eg:MacOS A、B、C...多账户登录时，/usr/local/lib/node_modules/ 此目录读写权限只能是用户A、B、C等其中的一个
sudo chown -R $(whoami) /usr/local/lib/node_modules/

将当前目录下所有文件读写权限改为可读可写
sudo chmod -R 777 ./

#打开cd到父目录的终端窗口
echo path="$(dirname "$0")"
echo $path
cd "$(dirname "$0")"
ls 
open -a Terminal "$(dirname "$0")"


#加入环境变量并刷新
vim ~/.bash_profile
加入path
export PATH="/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:${JAVA_HOME}:${JAVA_HOME}/bin"
重启profile
source ~/.bash_profile




```



#查看文件权限
可以直接通过 `man chmod` 在终端工具上查看`chmod `命令的帮助手册。

`ls -l` 命令可以查看当前目录下所有文件的访问权限，也可以查看指定文件。比如，查看 Tomcat bin 目录中的 startup.sh 文件的访问权限时：

```
yifeng:bin yifeng$ ls -l startup.sh
-rwxrwxrwx@ 1 yifeng  staff  1904  9 27 18:32 startup.sh
```
上面打印的文件信息中每一部分所代表的含义，分别对应如下解释：

```
文件类型和访问权限 文件数量 所属用户 所在群组 文件大小 修改日期（月 日 时 分） 文件名称
```
第一部分详细说明一下，就以 “-rwxrwxrwx” 为例：第一个符号代表文件类型， “-” 符号表示该文件是非目录类型，“d” 符号表示目录类型；（ 末尾的 @ 符号表示文件拓展属性，属于文件系统的一个功能。）

后面九个字母分为三组，从前到后每组分别对应所属用户（user）、所属用户所在组（group）和其他用户（other）对该文件的访问权限；

每组中的三个字符 “rwx” 分别表示对应用户对该文件拥有的可读／可写／可执行权限，没有相应权限则使用 “-” 符号替代。

#修改访问权限

根据上面查看权限部分的介绍，修改权限也应包括访问用户、添加或取消操作、具体权限和访问文件，即：


```
chmod 用户+操作+权限 文件
```

用户部分：使用字母 u 表示文件拥有者（user），g 表示拥有者所在群组（group），o 表示其他用户（other），a 表示全部用户（all，包含前面三种用户范围）；

操作部分：“+” 符号表示增加权限，“-” 符号表示取消权限，“=” 符号表示赋值权限；

权限部分：“r” 符号表示可读（read），“w” 表示可写（write），“x” 表示可执行权限（execute）；

文件部分：如不指定文件名，表示操作对象为当前目录下的所有文件。

还以前面 startup.sh 文件为例，将拥有者所在群组和其他用户改为可读可写权限、取消可执行权限的使用方式为：


```
chmod go-x startup.sh
```
然后使用 ls 命令查看权限，

```
yifeng:bin yifeng$ ls -l startup.sh
-rwxrw-rw-@ 1 yifeng  staff  1904  9 27 18:32 startup.sh
```
可以看到，文件访问权限已经按照要求发生对应变化。

如果是复杂一点操作的话，可以同时使用多种操作符添加和取消权限，并且可以使用 “,” 符号同时对不同用户范围修改权限，比如：


chmod g+x,o+x-w startup.sh
还有一种简单的写法，使用数字表示权限部分的读／写／可执行权限类型。数字和权限类型的对应关系，可以从这张图中直观地看出来：


![](http://ocq7gtgqu.bkt.clouddn.com/1508342093.png)

即，1 表示可执行，2 表示可写，4 表示可读。每种类型数字相加所得到的值表示交叉部分的公共类型。

这样的话，使用三个数字便可以分别代表三种不同用户类型的权限修改结果。比如，修改所有用户的访问权限均为可读可写可执行（rwx）的话，这样使用即可：


```
chmod 777 startup.sh
```
三个数字从前到后分别表示 u、g、o 三种用户类型的访问权限，使用时按需修改。

补充一点，有时候需要递归修改目录文件及其子目录中的文件类型，可以使用 -R 选项