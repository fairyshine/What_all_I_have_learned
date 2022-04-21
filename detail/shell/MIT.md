https://missing-semester-cn.github.io/

### L1 shell概览：基础命令

#### which  🌟

查询程序地址

```shell
which echo
which java
which python
```

#### man  🌟

查看命令的帮助手册manual

```shell
bash中还可用
XXX --help
help XXX

zsh中可以run-help XXX 和man一样
```

#### date

查看系统日期

#### echo

```shell
echo hello
打印hello

echo $PATH
打印环境变量
```

#### 文件操作 pwd、cd、ls等等

```shell
pwd 打印当前目录

cd 切换到某目录下
cd ../ 返回上级目录
cd - 在最近浏览的两个目录之前切换

ls 列出当前目录下所有文件
ls -l 显示详细信息
ll 其上的简写

mv 移动文件
cp 复制文件
rm 删除文件
mkdir 创建文件夹
touch 创建文件

cat 显示文件内容
```

#### 重定向符号和管道符号

##### 输出重定向 >,>>

```shell
echo hello > hello.txt 将hello打印输出在hello.txt文件中
cat hello.txt 	显示hello文件内容

>> append 在原有基础上增加，不覆盖
```

##### 输入重定向 <,<<

```shell
cat < hello.txt 读取hello.txt内容作为输入
```

##### 混合使用

```shell
cat < hello.txt > hello2.txt
```

##### tee

```shell
tee 双向覆盖重定向，相当于：屏幕输出｜文本输入
```



##### 管道（pipe）运算符 ｜

```shell
A ｜ B
程序A的输出，作为程序B的输入
ls -l / | tail -n1

```

#### sudo

以管理员的身份执行命令

```shell
sudo su 进入root模式
exit 退出

更改亮度例子：
sudo echo 500 > brightness  ❌ 经历重定向，左边仅输入文本，程序主体在右边 >,没给sudo权限
echo 500 | sudo tee brightness ✅
```

### L2 shell脚本(script)和工具

#### 变量、赋值

```shell
foo=bar  使用空格会出错，解释器会调用foo，认为= bar是参数
echo $foo

''原生字符串
""变量会被转义
echo "Value is $foo."
```

#### 函数

```shell
vim mcd.sh

MCD(){
	mkdir -p "$1"
	cd "$1"
}

source mcd.sh 执行并装载脚本
```

- `$0` - 脚本名
- `$1` 到 `$9` - 脚本的参数。 `$1` 是第一个参数，依此类推。
- `$@` - 所有参数
- `$#` - 参数个数
- `$?` - 前一个命令的返回值
- `$$` - 当前脚本的进程识别码
- `!!` - 完整的上一条命令，包括参数。常见应用：当你因为权限不足执行命令失败时，可以使用 `sudo !!`再尝试一次。
- `$_` - 上一条命令的最后一个参数。如果你正在使用的是交互式 shell，你可以通过按下 `Esc` 之后键入 . 来获取这个值。

##### 退出码exit status/exit code （返回码）

程序 `true` 的返回码永远是`0`，`false` 的返回码永远是`1`。

退出码可以搭配 `&&`（与操作符）和 `||`（或操作符）使用，用来进行条件判断，决定是否执行其他程序。它们都属于短路运算符（short-circuiting） 同一行的多个命令可以用` ; `分隔。

```shell
命令1 || 命令2 
	命令1执行成功，则执行命令2
命令1 && 命令2
	命令1未能执行成功，则执行命令2
	
命令1 ; 命令2
多条命令用分号分隔
```

##### 命令替换

```shell
$(CMD) 以CMD的输出结果作为变量值

例：
echo "We are in $(pwd)"

<(CMD) 将CMD的输出结果以文件形式临时存储,<(CMD)为临时文件名

例：
cat <(ls)
diff <(ls foo) <(ls bar) 比较文件夹foo和bar中文件的区别


```

##### 举例

example.sh

```shell
echo "Running program $0 with $# arguments with pid $$"
echo "Starting program at $(date)"

for file in "$@"; do
    grep foobar "$file" > /dev/null 2> /dev/null
    # 如果模式没有找到，则grep退出状态为 1
    # 我们将标准输出流和标准错误流重定向到Null，因为我们并不关心这些信息
    if [[ $? -ne 0 ]]; then
        echo "File $file does not have any foobar, adding one"
        echo "# foobar" >> "$file"
    fi
done
```

grep:使用正则表达式搜索文本

##### 通配符

？和*匹配一个或任意个字符

##### {}

 当你有一系列的指令，其中包含一段公共子串时，可以用花括号来自动展开这些命令。

```shell
convert image.{png,jpg}
# 会展开为
convert image.png image.jpg

cp /path/to/project/{foo,bar,baz}.sh /newpath
# 会展开为
cp /path/to/project/foo.sh /path/to/project/bar.sh /path/to/project/baz.sh /newpath

# 下面命令会创建foo/a, foo/b, ... foo/h, bar/a, bar/b, ... bar/h这些文件
touch {foo,bar}/{a..h}

# 也可以结合通配使用
mv *{.py,.sh} folder
# 会移动所有 *.py 和 *.sh 文件
```

convert：图片格式转换

使用shellcheck 检查脚本中的错误

##### 其他语言的脚本

注意，脚本并不一定只有用 bash 写才能在终端里调用。比如说，这是一段 Python 脚本，作用是将输入的参数倒序输出：

```python
#!/usr/local/bin/python 或者 #!/usr/bin/env python
import sys
for arg in reversed(sys.argv[1:]):
    print(arg)
```

内核知道去用 python 解释器而不是 shell 命令来运行这段脚本，是因为脚本的开头第一行的 shebang。

🌟在 `shebang` 行中使用 `env` 命令是一种好的实践，它会利用环境变量中的程序来解析该脚本，这样就提高来您的脚本的可移植性。`env` 会利用我们第一节讲座中介绍过的`PATH` 环境变量来进行定位。 例如，使用了`env`的shebang看上去时这样的`#!/usr/bin/env python`。

##### shell内函数与脚本的区别

shell函数和脚本有如下一些不同点：

- 函数只能与shell使用相同的语言，脚本可以使用任意语言。因此在脚本中包含 `shebang` 是很重要的。
- 函数仅在定义时被加载，脚本会在每次被执行时加载。这让函数的加载比脚本略快一些，但每次修改函数定义，都要重新加载一次。
- 函数会在当前的shell环境中执行，脚本会在单独的进程中执行。因此，函数可以对环境变量进行更改，比如改变当前工作目录，脚本则不行。脚本需要使用 `export`将环境变量导出，并将值传递给环境变量。
- 与其他程序语言一样，函数可以提高代码模块性、代码复用性并创建清晰性的结构。shell脚本中往往也会包含它们自己的函数定义。

#### shell 工具

##### 查找文件

```shell
find -name -type -path -size -exec

fd 即 find . -name '*PATTERN*'

locate
```

##### 查找代码

```shell
grep

ack
ag
rg(ripgrep)
```

##### 查找shell命令

```shell
history
```

##### 文件夹导航

```shell
fasd

autojump


tree,broot,nnn,ranger
```

### L3 编辑器（vim）

model editor 多模态编辑器

- 正常模式 normal mode ==esc键==退出下列模式

- 插入模式 insert mode  ==i键==进入

- 替换模式 replace mode  ==r键==进入

- 可视模式 visual mode ==v键==进入

  ​						-line  < s-v > shift+V

  ​						-block < c-v > ctrl+V 或 ^V

- 命令模式 command-line mode ==:键==进入

#### 基本操作

##### 插入文本

i键进入插入模式，esc键返回正常模式。

##### 缓存(buffer)，标签页(tab)，窗口

一个vim会话包含一系列标签页，

每个标签页包含一系列窗口，

每个窗口显示一个缓存。

缓存：打开的文件

一个缓存可以在多个窗口打开，方便查看文件的不同位置。

##### 命令行

```
q 退出（关闭窗口） qa 退出所有
w 保存（写）
wq 保存然后退出
e {文件名} 打开要编辑的文件
ls 显示打开的缓存
help {标题} 打开帮助文档
help :w 打开 :w 命令的帮助文档
help w 打开 w 移动的帮助文档

sp 上下分割窗口
tabnew 新建标签页
```

#### 设计思想：接口亦是编程语言

vim的界面本身是一个程序语言，键入操作本身即是命令。

这些命令可以组合使用，以提高效率。

##### 移动

- 基本移动: `hjkl` （左， 下， 上， 右）
- 词： `w` （下一个词）， `b` （词初）， `e` （词尾）
- 行： `0` （行初）， `^` （第一个非空格字符）， `$` （行尾）
- 屏幕： `H` （屏幕首行）， `M` （屏幕中间）， `L` （屏幕底部）
- 翻页： `Ctrl-u` （上翻）， `Ctrl-d` （下翻）
- 文件： `gg` （文件头）， `G` （文件尾）
- 行数： `:{行数}<CR>` 或者 `{行数}G` ({行数}为行数)
- 杂项： `%` （找到配对，比如括号或者 /* */ 之类的注释对）
- 查找： f{字符}，t{字符}， F{字符}，T{字符}
  - 查找/到 向前/向后 在本行的{字符}
  - `,` / `;` 用于导航匹配
- 搜索: `/{正则表达式}`, `n` / `N` 用于导航匹配

##### 选择

可视化模式:

- 可视化：`v`
- 可视化行： `V`
- 可视化块：`Ctrl+v`

可以用移动命令来选中。

##### 编辑

所有你需要用鼠标做的事， 你现在都可以用键盘：采用编辑命令和移动命令的组合来完成。 这就是 Vim 的界面开始看起来像一个程序语言的时候。Vim 的编辑命令也被称为 “动词”， 因为动词可以施动于名词。

- `i` 进入插入模式
  - 但是对于操纵/编辑文本，不单想用退格键完成
- `O` / `o` 在之上/之下插入行
- d{移动命令}删除 {移动命令}
  - 例如， `dw` 删除词, `d$` 删除到行尾, `d0` 删除到行头。
- c{移动命令}改变 {移动命令}
  - 例如， `cw` 改变词
  - 比如 `d{移动命令}` 再 `i`
- `x` 删除字符（等同于 `dl`）
- `s` 替换字符（等同于 `xi`）
- 可视化模式 + 操作
  - 选中文字, `d` 删除 或者 `c` 改变
- `u` 撤销, `<C-r>` 重做
- `y` 复制 / “yank” （其他一些命令比如 `d` 也会复制）
- `p` 粘贴
- 更多值得学习的: 比如 `~` 改变字符的大小写

##### 计数

你可以用一个计数来结合“名词”和“动词”，这会执行指定操作若干次。

- `3w` 向前移动三个词
- `5j` 向下移动5行
- `7dw` 删除7个词

##### 修饰语modifiers

你可以用修饰语改变“名词”的意义。修饰语有 `i`，表示“内部”或者“在内“，和 `a`， 表示”周围“。

- `ci(` 改变当前括号内的内容
- `ci[` 改变当前方括号内的内容
- `da'` 删除一个单引号字符串， 包括周围的单引号

#### 自定义vim

Vim 由一个位于 `~/.vimrc` 的文本配置文件（包含 Vim 脚本命令）。 

#### 扩展vim

以下是一些我们最爱的插件：

- [ctrlp.vim](https://github.com/ctrlpvim/ctrlp.vim): 模糊文件查找
- [ack.vim](https://github.com/mileszs/ack.vim): 代码搜索
- [nerdtree](https://github.com/scrooloose/nerdtree): 文件浏览器
- [vim-easymotion](https://github.com/easymotion/vim-easymotion): 魔术操作

#### 其他程序的vim模式

##### shell

如果你是一个 Bash 用户，用 `set -o vi`。如果你用 Zsh：`bindkey -v`。Fish 用 `fish_vi_key_bindings`。另外，不管利用什么 shell，你可以`export EDITOR=vim`。 这是一个用来决定当一个程序需要启动编辑时启动哪个的环境变量。 例如，`git` 会使用这个编辑器来编辑 commit 信息。

##### Readline

很多程序使用 [GNU Readline](https://tiswww.case.edu/php/chet/readline/rltop.html) 库来作为 它们的命令控制行界面。Readline 也支持基本的 Vim 模式， 可以通过在 `~/.inputrc` 添加如下行开启：

```
set editing-mode vi
```

比如，在这个设置下，Python REPL 会支持 Vim 快捷键。

#### vim进阶

##### 搜索和替换

`:s` （替换）命令（[文档](http://vim.wikia.com/wiki/Search_and_replace)）。

- %s/foo/bar/g
  - 在整个文件中将 foo 全局替换成 bar
- %s/\[.*\](\(.*\))/\1/g
  - 将有命名的 Markdown 链接替换成简单 URLs

##### 宏

- `q{字符}` 来开始在寄存器`{字符}`中录制宏

- `q`停止录制

- `@{字符}` 重放宏

- 宏的执行遇错误会停止

- `{计数}@{字符}`执行一个宏{计数}次

- 宏可以递归

  - 首先用`q{字符}q`清除宏
  - 录制该宏，用 `@{字符}` 来递归调用该宏 （在录制完成之前不会有任何操作）

- 例子：将 xml 转成 json

  - 一个有 “name” / “email” 键对象的数组

  - 用一个 Python 程序？

    用 sed / 正则表达式

    - `g/people/d`
    - `%s/<person>/{/g`
    - `%s/<name>\(.*\)<\/name>/"name": "\1",/g`
    - …

  - Vim 命令 / 宏

    - `Gdd`, `ggdd` 删除第一行和最后一行

    - 格式化最后一个元素的宏 （寄存器e）

      - 跳转到有 `<name>` 的行
      - `qe^r"f>s": "<ESC>f<C"<ESC>q`

    - 格式化一个的宏

      - 跳转到有 `<person>` 的行
      - `qpS{<ESC>j@eA,<ESC>j@ejS},<ESC>q`

    - 格式化一个

      标签然后转到另外一个的宏

      - 跳转到有 `<person>` 的行
      - `qq@pjq`

    - 执行宏到文件尾

      - `999@q`

    - 手动移除最后的 `,` 然后加上 `[` 和 `]` 分隔符

### L4 数据整理 Data Wrangling

转换数据的存储格式

#### 正则表达式

- `.` 除换行符之外的”任意单个字符”
- `*` 匹配前面字符零次或多次
- `+` 匹配前面字符一次或多次
- `[abc]` 匹配 `a`, `b` 和 `c` 中的任意一个
- `(RX1|RX2)` 任何能够匹配`RX1` 或 `RX2`的结果
- `^` 行首
- `$` 行尾

#### sed—“流编辑器”

在 `sed` 中，您基本上是利用一些简短的命令来修改文件，而不是直接操作文件的内容（尽管您也可以选择这样做）。相关的命令行非常多，但是最常用的是 `s`，即*替换*命令，例如我们可以这样写：

```shell
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed 's/.*Disconnected from //'
```

`s` 命令的语法如下：`s/REGEX/SUBSTITUTION/`, 其中 `REGEX` 部分是我们需要使用的正则表达式，而 `SUBSTITUTION` 是用于替换匹配结果的文本。

`sed` 还可以做很多各种各样有趣的事情，例如文本注入：(使用 `i` 命令)，打印特定的行 (使用 `p`命令)，基于索引选择特定行等等。详情请见`man sed`!

#### 过滤

`sort` 会对其输入数据进行排序。`uniq -c` 会把连续出现的行折叠为一行并使用出现次数作为前缀。

#### awk 

一种编程语言，善于处理文本

#### 分析数据

##### bc  计算器

##### R

##### gnuplot

##### xargs

##### fmepg

### L5 命令行环境 command-line environment

#### 任务控制 job control

##### 结束进程

ctrl+c 终止进程：shell发送`SIGINT`信号 (UNIX提供的信号机制)

ctrl+\ 退出进程：发送`SIGQUIT`信号

man signal 查看各种信号

下面这个 Python 程序向您展示了捕获信号`SIGINT` 并忽略它的基本操作，它并不会让程序停止。为了停止这个程序，我们需要使用`SIGQUIT` 信号，通过输入`Ctrl-\`可以发送该信号。

```python
#!/usr/bin/env python
import signal, time

def handler(signum, time):
    print("\nI got a SIGINT, but I am not stopping")

signal.signal(signal.SIGINT, handler)
i = 0
while True:
    time.sleep(.1)
    print("\r{}".format(i), end="")
    i += 1
```

尽管 `SIGINT` 和 `SIGQUIT` 都常常用来发出和终止程序相关的请求。`SIGTERM` 则是一个更加通用的、也更加优雅地退出信号。为了发出这个信号我们需要使用 [`kill`](https://www.man7.org/linux/man-pages/man1/kill.1.html) 命令, 它的语法是： `kill -TERM <PID>`。

##### 暂停和后台执行进程

17    SIGSTOP          stop process         stop (cannot be caught or ignored)
18    SIGTSTP          stop process         stop signal generated from keyboard

信号可以让进程做其他的事情，而不仅仅是终止它们。例如，`SIGSTOP` 会让进程暂停。在终端中，键入 `Ctrl-Z` 会让 shell 发送 `SIGTSTP` 信号。

我们可以使用 `fg` 或 `bg`命令恢复暂停的工作。它们分别表示在前台继续或在后台继续。命令中的 `&` 后缀可以让命令在直接在后台运行，这使得您可以直接在 shell 中继续做其他操作，不过它此时还是会使用 shell 的标准输出，这一点有时会比较恼人（这种情况可以使用 shell 重定向处理）。

---

jobs命令列出终端中尚未完成的全部任务

使用pid或%任务编号来选取该任务，最近的任务：$!

nohup:终端关闭时发送信号`SIGHUP`终止所有后台进程，使用nohup来忽略`SIGHUP`，针对已运行的程序，使用`disown`

kill -STOP %任务编号 （发送信号SIGSTOP给进程并终止）

`SIGKILL` 是一个特殊的信号，它不能被进程捕获并且它会马上结束该进程。不过这样做会有一些副作用，例如留下孤儿进程。

#### 终端多路复用 Terminal Multiplexers

🌟tmux

sessions会话->windows窗口->panes面板

`tmux` 的快捷键需要我们掌握，它们都是类似 `<C-b> x` 这样的组合，即需要先按下`Ctrl+b`，松开后再按下 `x`。`tmux` 中对象的继承结构如下：

- 会话\- 每个会话都是一个独立的工作区，其中包含一个或多个窗口
  - `tmux` 开始一个新的会话
  - `tmux new -s NAME` 以指定名称开始一个新的会话
  - `tmux ls` 列出当前所有会话
  - 在 `tmux` 中输入 `<C-b> d` ，将当前会话分离
  - `tmux a` 重新连接最后一个会话。您也可以通过 `-t` 来指定具体的会话
- 窗口\- 相当于编辑器或是浏览器中的标签页，从视觉上将一个会话分割为多个部分
  - `<C-b> c` 创建一个新的窗口，使用 `<C-d>`关闭
  - `<C-b> N` 跳转到第 *N* 个窗口，注意每个窗口都是有编号的
  - `<C-b> p` 切换到前一个窗口
  - `<C-b> n` 切换到下一个窗口
  - `<C-b> ,` 重命名当前窗口
  - `<C-b> w` 列出当前所有窗口
- 面板\- 像 vim 中的分屏一样，面板使我们可以在一个屏幕里显示多个 shell
  - `<C-b> "` 水平分割
  - `<C-b> %` 垂直分割
  - `<C-b> <方向>` 切换到指定方向的面板，<方向> 指的是键盘上的方向键
  - `<C-b> z` 切换当前面板的缩放
  - `<C-b> [` 开始往回卷动屏幕。您可以按下空格键来开始选择，回车键复制选中的部分
  - `<C-b> <空格>` 在不同的面板排布间切换

#### 别名

输入一长串包含许多选项的命令会非常麻烦。因此，大多数 shell 都支持设置别名。shell 的别名相当于一个长命令的缩写，shell 会自动将其替换成原本的命令。例如，bash 中的别名语法如下：

```
alias alias_name="command_to_alias arg1 arg2"
```

注意， `=`两边是没有空格的，因为 `alias`是一个 shell 命令，它只接受一个参数。

别名有许多很方便的特性:

```
# 创建常用命令的缩写
alias ll="ls -lh"

# 能够少输入很多
alias gs="git status"
alias gc="git commit"
alias v="vim"

# 手误打错命令也没关系
alias sl=ls

# 重新定义一些命令行的默认行为
alias mv="mv -i"           # -i prompts before overwrite
alias mkdir="mkdir -p"     # -p make parent dirs as needed
alias df="df -h"           # -h prints human readable format

# 别名可以组合使用
alias la="ls -A"
alias lla="la -l"

# 在忽略某个别名
\ls
# 或者禁用别名
unalias la

# 获取别名的定义
alias ll
# 会打印 ll='ls -lh'
```

值得注意的是，在默认情况下 shell 并不会保存别名。为了让别名持续生效，您需要将配置放进 shell 的启动文件里，像是`.bashrc` 或 `.zshrc`

#### 配置文件 Dotfiles

使用符号链接symlink来管理（stow）

#### 远端设备 Remote Machine

SSH （secure shell）

使用SSH keys 密钥，免密码

Scp 传输文件

#### Shell & 框架

#### 终端模拟器

### L6 Git(版本控制，version control)

#### git的数据模型

##### 快照snapshot

文件：Blob对象（数据对象）

目录/文件夹：树，将其名字和Blob对象或树对象进行映射（使得目录中可以包含其他目录）

快照：被追踪的最顶层的树

```
<root> (tree)
|
+- foo (tree)
|  |
|  + bar.txt (blob, contents = "hello world")
|
+- baz.txt (blob, contents = "git is wonderful")
```

这个顶层的树包含了两个元素，一个名为 “foo” 的树（它本身包含了一个blob对象 “bar.txt”），以及一个 blob 对象 “baz.txt”。

##### 历史记录建模：关联快照

在 Git 中，历史记录是一个由快照组成的有向无环图。这代表 Git 中的每个快照都有一系列的“父辈”，也就是其之前的一系列快照。注意，快照具有多个“父辈”而非一个，因为某个快照可能由多个父辈而来。例如，经过合并后的两条分支。

在 Git 中，这些快照被称为“提交”。通过可视化的方式来表示这些历史提交记录时，看起来差不多是这样的：

```
o <-- o <-- o <-- o
            ^  
             \
              --- o <-- o
```

##### 数据模型及其伪代码表示

以伪代码的形式来学习 Git 的数据模型，可能更加清晰：

```
// 文件就是一组数据
type blob = array<byte>

// 一个包含文件和目录的目录
type tree = map<string, tree | blob>

// 每个提交都包含一个父辈，元数据和顶层树
type commit = struct {
    parent: array<commit>
    author: string
    message: string
    snapshot: tree
}
```

这是一种简洁的历史模型。

##### 对象和内存寻址

Git 中的对象可以是 blob、树或提交：

```
type object = blob | tree | commit
```

Git 在储存数据时，所有的对象都会基于它们的 [SHA-1 哈希](https://en.wikipedia.org/wiki/SHA-1) 进行寻址。

```
objects = map<string, object>

def store(object):
    id = sha1(object)
    objects[id] = object

def load(id):
    return objects[id]
```

Blobs、树和提交都一样，它们都是对象。当它们引用其他对象时，它们并没有真正的在硬盘上保存这些对象，而是仅仅保存了它们的哈希值作为引用。

例如，[上面](https://missing-semester-cn.github.io/2020/version-control/#snapshots)例子中的树（可以通过 `git cat-file -p 698281bc680d1995c5f4caaf3359721a5a58d48d` 来进行可视化），看上去是这样的：

```
100644 blob 4448adbf7ecd394f42ae135bbeed9676e894af85    baz.txt
040000 tree c68d233a33c5c06e0340e4c224f0afca87c8ce87    foo
```

树本身会包含一些指向其他内容的指针，例如 `baz.txt` (blob) 和 `foo`(树)。如果我们用 `git cat-file -p 4448adbf7ecd394f42ae135bbeed9676e894af85`，即通过哈希值查看 baz.txt 的内容，会得到以下信息：

```
git is wonderful
```

##### 引用

现在，所有的快照都可以通过它们的 SHA-1 哈希值来标记了。但这也太不方便了，谁也记不住一串 40 位的十六进制字符。

针对这一问题，Git 的解决方法是给这些哈希值赋予人类可读的名字，也就是引用（references）。引用是指向提交的指针。与对象不同的是，它是可变的（引用可以被更新，指向新的提交）。例如，`master` 引用通常会指向主分支的最新一次提交。

```
references = map<string, string>

def update_reference(name, id):
    references[name] = id

def read_reference(name):
    return references[name]

def load_reference(name_or_id):
    if name_or_id in references:
        return load(references[name_or_id])
    else:
        return load(name_or_id)
```

这样，Git 就可以使用诸如 “master” 这样人类可读的名称来表示历史记录中某个特定的提交，而不需要在使用一长串十六进制字符了。

有一个细节需要我们注意， 通常情况下，我们会想要知道“我们当前所在位置”，并将其标记下来。这样当我们创建新的快照的时候，我们就可以知道它的相对位置（如何设置它的“父辈”）。在 Git 中，==我们当前的位置==有一个特殊的索引，它就是 ==“HEAD”==。

##### 仓库

最后，我们可以粗略地给出 Git 仓库的定义了：`对象` 和 `引用`。

在硬盘上，Git 仅存储对象和引用：因为其数据模型仅包含这些东西。所有的 `git` 命令都对应着对提交树的操作，例如增加对象，增加或删除引用。

当您输入某个指令时，请思考一下这条命令是如何对底层的图数据结构进行操作的。另一方面，如果您希望修改提交树，例如“丢弃未提交的修改和将 ‘master’ 引用指向提交 `5d83f9e` 时，有什么命令可以完成该操作（针对这个具体问题，您可以使用 `git checkout master; git reset --hard 5d83f9e`）

#### 暂存区

Git 中还包括一个和数据模型完全不相关的概念，但它确是创建提交的接口的一部分。

就上面介绍的快照系统来说，您也许会期望它的实现里包括一个 “创建快照” 的命令，该命令能够基于当前工作目录的当前状态创建一个全新的快照。有些版本控制系统确实是这样工作的，但 Git 不是。我们希望简洁的快照，而且每次从当前状态创建快照可能效果并不理想。例如，考虑如下场景，您开发了两个独立的特性，然后您希望创建两个独立的提交，其中第一个提交仅包含第一个特性，而第二个提交仅包含第二个特性。或者，假设您在调试代码时添加了很多打印语句，然后您仅仅希望提交和修复 bug 相关的代码而丢弃所有的打印语句。

Git 处理这些场景的方法是使用一种叫做 “暂存区（staging area）”的机制，它允许您指定下次快照中要包括那些改动。

#### git的命令行窗口

##### 基础

- `git help <command>`: 获取 git 命令的帮助信息

- `git init`: 创建一个新的 git 仓库，其数据会存放在一个名为 `.git` 的目录下

- `git status`: 显示当前的仓库状态

- `git add <filename>`: 添加文件到暂存区

- `git commit`: 创建一个新的提交

  - 如何编写 [良好的提交信息](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)!
  - 为何要 [编写良好的提交信息](https://chris.beams.io/posts/git-commit/)

- `git log`: 显示历史日志

- `git log --all --graph --decorate`: 可视化历史记录（有向无环图）

- `git diff <filename>`: 显示与暂存区文件的差异

- `git diff <revision> <filename>`: 显示某个文件两个版本之间的差异

- `git checkout <revision>`: 更新 HEAD 和目前的分支

##### 分支和合并

- `git branch`: 显示分支

- `git branch <name>`: 创建分支

- `git checkout -b <name>` : 创建分支并切换到该分支

  - 相当于 `git branch <name>; git checkout <name>`

- `git merge <revision>`: 合并到当前分支

- `git mergetool`: 使用工具来处理合并冲突

- `git rebase`: 将一系列补丁变基（rebase）为新的基线

##### 远端操作

- `git remote`: 列出远端
- `git remote add <name> <url>`: 添加一个远端
- `git push <remote> <local branch>:<remote branch>`: 将对象传送至远端并更新远端引用
- `git branch --set-upstream-to=<remote>/<remote branch>`: 创建本地和远端分支的关联关系
- `git fetch`: 从远端获取对象/索引
- `git pull`: 相当于 `git fetch; git merge`
- `git clone`: 从远端下载仓库

##### 撤销

- `git commit --amend`: 编辑提交的内容或信息
- `git reset HEAD <file>`: 恢复暂存的文件
- `git checkout -- <file>`: 丢弃修改

#### git 高级操作

- `git config`: Git 是一个 [高度可定制的](https://git-scm.com/docs/git-config) 工具
- `git clone --depth=1`: 浅克隆（shallow clone），不包括完整的版本历史信息
- `git add -p`: 交互式暂存
- `git rebase -i`: 交互式变基
- `git blame`: 查看最后修改某行的人
- `git stash`: 暂时移除工作目录下的修改内容
- `git bisect`: 通过二分查找搜索历史记录
- `.gitignore`: [指定](https://git-scm.com/docs/gitignore) 故意不追踪的文件

#### 杂项

插件:fugitive.vim

### L7 调试和性能分析

### L8 元编程 Metaprogramming

#### 构建系统

make 

#### 依赖管理

#### 持续集成系统

### L9 安全和密码学

#### 熵

度量不确定性

$log_2x$ ，扔一次硬币的熵是1比特

#### 散列函数 hash function

```
hash(value: array<byte>) -> vector<byte, N>  (N对于该函数固定)
```

[SHA-1](https://en.wikipedia.org/wiki/SHA-1)是Git中使用的一种散列函数， 它可以将任意大小的输入映射为一个160比特（可被40位十六进制数表示）的输出。

抽象地讲，散列函数可以被认为是一个不可逆，且看上去随机（但具确定性）的函数。

- 确定性：对于不变的输入永远有相同的输出。
- 不可逆性：对于`hash(m) = h`，难以通过已知的输出`h`来计算出原始输入`m`。
- 目标碰撞抵抗性/弱无碰撞：对于一个给定输入`m_1`，难以找到`m_2 != m_1`且`hash(m_1) = hash(m_2)`。
- 碰撞抵抗性/强无碰撞：难以找到一组满足`hash(m_1) = hash(m_2)`的输入`m_1, m_2`（该性质严格强于目标碰撞抵抗性）。

##### 密码散列函数的应用

Git中的内容寻址存储

文件的信息摘要

承诺机制

#### 密钥生成函数

#### 对称加密

#### 非对称加密

公钥，私钥

#### 案例分析

### L10 大杂烩

#### 修改键位映射

#### 守护进程

#### FUSE

FUSE 可以用于实现如：一个将所有文件系统操作都使用 SSH 转发到远程主机，由远程主机处理后返回结果到本地计算机的虚拟文件系统。这个文件系统里的文件虽然存储在远程主机，对于本地计算机上的软件而言和存储在本地别无二致。`sshfs`就是一个实现了这种功能的 FUSE 文件系统。

#### 备份

#### API（应用程序接口）

#### 常见命令行标志参数及模式

#### 窗口管理器

#### VPN

#### Markdown

#### Hammerspoon （mac桌面自动化）

#### 开机引导

#### 虚拟机、容器化

#### 交互式记事本编程

#### Github



### L11 问答QA

文件结构

- `/bin` - 基本命令二进制文件
- `/sbin` - 基本的系统二进制文件，通常是root运行的
- `/dev` - 设备文件，通常是硬件设备接口文件
- `/etc` - 主机特定的系统配置文件
- `/home` - 系统用户的主目录
- `/lib` - 系统软件通用库
- `/opt` - 可选的应用软件
- `/sys` - 包含系统的信息和配置([第一堂课](https://missing-semester-cn.github.io/2020/course-shell/)介绍的)
- `/tmp` - 临时文件( `/var/tmp` ) 通常重启时删除
- `/usr/`-只读的用户数据
  - `/usr/bin` - 非必须的命令二进制文件
  - `/usr/sbin` - 非必须的系统二进制文件，通常是由root运行的
  - `/usr/local/bin` - 用户编译程序的二进制文件
- `/var` -变量文件 像日志或缓存
