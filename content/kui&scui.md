---
title: "kui&scui"
date: 2023-11-06T14:50:00+08:00
draft: false
tag: "kui,vim"
---

目前主流的电脑上的人机交互接口有cli(command line interface)和gui(graphic user interface).


一般认为cli主要是键盘来操作,而gui主要是鼠标来操作.对这两种主要的操作方式,操作系统都会提供一些便捷的操作,比如快捷键和鼠标手势.


我喜欢使用快捷键操作, 但是目前所有操作系统的快捷键都有几个硬伤.


kui(keyboard user interface)或者scui(short cut user interface)是我提出的一个概念,志在对快捷键的操作进行一定的规范以屏蔽
掉主流操作系统之间的差异.它的设计目标如下:

1. 对最常用的快捷键操作进行定义
2. 最小化记忆负担
3. 快捷键之间没有冲突
4. 快捷键使用自然
5. 尽量只用主键盘区域,60%的键盘也能轻松使用

## 目前的快捷键体系存在的问题

- 快捷键十分有限,主要通过修饰键(ctrl, alt, meta)和一个字符a-z组成
- 快捷键十分容易冲突,有限的快捷键就必然导致快捷键的冲突, 一般而言, 管理软件的软件, 如窗口管理器或者terminal multiplexer,
又或者是shell等软件的快捷键可能会和它们内部软件的快捷键冲突,而让快捷键变得不可用.
- 记忆负担重,基本上快捷键多起来会发现记忆毫无规律和逻辑.

## 设计一套好用的kui所面临的问题

kui设计的过程中面临着很多的难点, 但是最主要的难点是键盘它本身的多样性,即使去掉那些奇怪的双手键盘那种,同样是ansi的键盘,也可能有不同的键位布局如colemak或者我现在在用的normal布局.这会使得对一个人而言按着很舒服的快捷键对另一个人按起来也就没那么舒服.还有一点是
人的手舒适指头有一定区别,不止有左利手和右利手这种,有些人的弱指如小指头一天打字久了会疼,而有些人食指打字久了会疼.


所以设计一个对所有情况下都适用的kui其实是一件很困难的事情.
假如放弃掉那些奇怪布局的键盘,使用目前绝大多数的ansi的键盘.复杂度稍微小了一些.


还有一点是按键冲突的存在,在gui中,一个软件中如果包含另一个软件,鼠标所点击的地方永远是最上层的软件做响应,这使得gui中很自然不会有按钮
穿透的问题,但是放到快捷键上,如果窗口管理器设置ctrl+a打开一个新窗口,此时焦点在终端,终端又设置了ctrl+a做别的事情, 那么则会
使得终端中的设置变的毫无用处,这对于终端多路复用器和shell也同样适用.


下面所有的kui的设计都是基于ansi键盘和常用布局.


## kui中的软件分类

为了消除软件的快捷键冲突,我们需要对电脑中的软件进行分类,一种类型的软件使用一个按键作为快捷键的修饰,这样就解决了快捷键冲突的问题.

| 软件类型                                       | 功能键         |
| --------------                                 | -------------- |
| 窗口管理器                                     | ctrl           |
| gui中普通软件(除了有vim模式的文本编辑器)       | alt            |
| 可以连接ssh管理终端的软件(主要包括终端,浏览器) | ctrl+alt       |
| 终端多路复用器                                 | alt            |
| cli中普通软件                                  | ,或者无功能键  |


以上5种类型基本可以覆盖到所有机器的所有软件.不管是访问哪台机器.


## vim的启示和不足

vim是一个非常经典的文本编辑器,可以认为它在cli时代其实提供了一个十分经典的kui,导致很多软件的模仿,一些软件会标榜自己是vim like的
keybinding. 甚至现在任意一个文本编辑器如果没有vim快捷键的插件,可以认为它不是一个成熟的文本编辑器.


vim快捷键最大的优点是其在普通模式下leader键的设计, 导致它可以有无数的快捷键选择,并且这些快捷键还是有一些助记.在现在的gui环境下,
绝大多数软件的按键如果是普通的可视字符的话,都是会将其直接输入到输入框,或者如果没有输入框,则按键无意义.它们都是可以利用ctrl或者alt键
组合一个字母键来作为快捷键. 有些只能接受一个快捷键按键,有些可以接受快捷键序列, 例如ctrl+a
a这种属于按下一个快捷键ctrl+a,然后马上按下a.  这个时候ctrl+a可以认为是vim中的leader键. 或者叫做快捷键的前缀.


vim的不足之处在于它仍然还有很多的以ctrl或者alt为开头的快捷键设计,按照kui的应用划分,终端中的软件应该尽量避免使用ctrl,alt
这些控制键作为快捷键,他们应该使用普通的按键例如逗号作为快捷键.

## [spacefn][1]和按键的方

其实传统的键盘快捷键中,有一个绝大多数软件都遵守的,就是上下左右键还有pageup,pagedown,home和end按键,这些按键的作用在大多数软件
中的作用都是一致的, 但是这些按键都脱离了主键盘. [spacefn][1]是一个非常好用的补充,可以说是kui设计的基础.


[spacefn][1]的主要思想就是用空格键作为功能按键,用空格加一个字母键来模拟键盘上其它的按键例如方向键.它极大的减少了kui需要手
离开主键盘的情况.


按ctrl键,如果是一手鼠标,一手键盘的话,有很多人使用的是小拇指, 所以一般用左手的小拇指来按左边ctrl,因为小拇指是弱指,使用太过
频繁容易引发一些手指疼痛.所以在kui中,推荐使用左手手掌根来按ctrl键, 用右手的手掌根来按右边的功能键,通过修改键盘,让右边的ctrl键和menu键都改成super键.当然掌根按键不是必须的,它对一般的键盘都非常好用, 但是对笔记本上的键盘就不能用了.


## 按键描述

在下面的按键接口描述中,特殊按键用小括号包起来,例如(ctrl),(up),(home)等, (ctrl+a)代表同时按下ctrl键和a键,(ctrl+a)b代表先按下
ctrl+a,再按下b键,一般这两个按键的间隔时间不超过一秒.
(leader)在不同分类的软件中是不一样的,(leader+a)如果在窗口管理器中是代表(ctrl+a),在普通gui软件中代表(alt+a),在终端和浏览器中代表
(ctrl+alt+a),在终端多路复用器如tmux或者zellij里面代表(alt+a),在vim或者其他cli软件中代表(,a)或者a

| 功能描述       | norman布局     | qwert布局      |
| -------------- | -------------- | -------------- |
| ...            | ...            | ...            |


## 接口描述和分类

有一种按键在pc上实在是太过常见并且几乎所有软件都支持它, 那就是"copy和paste", 在windows上就是ctrl+c和ctrl+v. 这两个按键我
建议保留原有用法,除非是在某些独特的布局中. 在设计接口时,尽量不使用c和v包括(功能键+c)这种情况用做第一个按键.


剩下其它的接口我按照功能进行了一个分类.spacefn是所有按键的基础,通过space组合字母按键来模拟方向键对kui的设计和记忆起到很大
的作用,假如在spacefn中, 设置了(space+d)来模拟(pageup), 那么在其它的按键中, 假如是(ctrl+pageup), 这时需要按下的是(ctrl+space+d),
通过左手掌根按ctrl,大拇指自然按space,中止按d,即可完成这个按键,熟练以后会感觉相当自然.


- spacefn接口
- 鼠标控制接口
- 备用快速接口
- 光标/焦点移动接口
- 窗口和tab的修改接口
- 创建资源接口
- 删除资源接口
- 拷贝资源接口
- coding专有接口

### spacefn接口

这个接口主要是在左手和右手的标准指法上,用于模拟之前存在的上下左右键以及home,end,pageup和pagedown.其中pageup,pagedown,home和end可以看成更高一级的上下左右,我有时候会将他们称为(big)上下左右.假设在norman布局中当按下space+r键时将其映射为up键,那么我们可以称r键为norman布局下的smallup键,而将d键称为smallbigup因为(space+d)会在norman布局中映射为(bigup)也就是(pageup).
其它的可以没有,主要是用来模拟vim中经常用到的一些符号,例如:, %, @等.


| 按键模拟           | 是否必须 | norman布局(rinodest)                   | qwert布局(ikjledsf) |
| --------------     | -------- | --------------                         | --------------      |
| (up)               | 是       | (space+r)                              | (space+i)           |
| (down)             | 是       | (space+i)                              | (space+k)           |
| (left)             | 是       | (space+n)                              | (space+j)           |
| (right)            | 是       | (space+o)                              | (space+l)           |
| (pageup)           | 是       | (space+d)                              | (space+e)           |
| (pagedown)         | 是       | (space+e)                              | (space+d)           |
| (home)             | 是       | (space+s)                              | (space+s)           |
| (end)              | 是       | (space+t)                              | (space+f)           |
| (esc)              | 是       | (capslock)                             | (capslock)          |
| (enter)            | 否       | (space+y)                              | (space+h)           |
| (backspace)        | 否       | (space+l)                              | (space+o)           |
| (ctrl+alt)         | 否       | (rightctrl) and (menu or win or super) |                     |
| :                  | 最好     | (space+p)                              |                     |
| %                  | 最好     | (space+m)                              |                     |
| @                  | 最好     | (space+w)                              |                     |
| _                  | 否       | (space+u)                              |                     |
| +                  | 否       | (space+a)                              |                     |
| ~                  | 否       | (space+c)                              |                     |
| `                  | 否       | (space+v)                              |                     |
| (shift+1234567890) | 否       | (space+1234567890)                     |                     |

### 鼠标控制接口

由于现状是有很多的gui软件目前不支持快捷键自定义或者无法把快捷键定义成想要的样子, 例如聊天中经常使用的qq. 鼠标控制接口就是通过
键盘来模拟鼠标的功能,让一些不太常用的功能,也可以通过简单的通过键盘完成.


在鼠标控制接口中, 所有的功能,除了进入鼠标模式的功能以为, 其它的功能都需要在进入鼠标模式以后才能生效.

| 功能描述         | 是否必须 | norman布局     | qwert布局      |
| --------------   | -------- | -------------- | -------------- |
| 进入鼠标控制模式 | 是       | (ctrl+p)       |                |
| 退出鼠标控制模式 | 是       | (esc or q)     |                |
| 鼠标上移         | 是       | r              |                |
| 鼠标下移         | 是       | i              |                |
| 鼠标左移         | 是       | n              |                |
| 鼠标右移         | 是       | o              |                |
| 鼠标左键点击     | 是       | s              |                |
| 鼠标右键点击     | 是       | t              |                |
| 鼠标中键点击     | 是       | (s+t) or x     |                |
| 鼠标滚轮上       | 是       | d              |                |
| 鼠标滚轮下       | 是       | e              |                |

### 备用快速接口

尽管鼠标控制接口可以处理一些边界情况,它在很多时候都不够快速,有一些情况是其它接口都不生效,但是用鼠标又感觉有点慢,比如vscode的
vim插件在某些情况下无法使用,或者是浏览器的插件在404的页面中无法被加载出来使用.
这个时候只需要快速的关闭页面或者用命令的方式来执行想要做的操作即可,此时可以用备用快速接口来实现.

| 功能描述         | 是否必须 | norman布局     | qwert布局      |
| --------------   | -------- | -------------- | -------------- |
| 关闭当前窗口     | 是       | (ctrl+b)q      |                |
| 重新加载当前窗口 | 是       | (ctrl+b)r      |                |
| 弹出命令的菜单   | 是       | (ctrl+b).      |                |

### 行编辑接口

很多情况下都需要对一行的内容进行编辑,因为任何软件都有可能需要用户输入一些内容,甚至在shell中,按行编辑输入是唯一个的交互方式.
在vim的普通模式中对行编辑有着丰富无比的命令,虽然很多情况下对一行的编辑没有这么多功能需求,但是有一些功能必不可少,例如快速清除
该行的内容.

| 功能描述                                          | 是否必须          | norman布局(rinodest)    | qwert布局      |
| --------------                                    | --------          | --------------          | -------------- |
| 移动到行首                                        | 是                | (home)                  |                |
| 移动到行尾                                        | 是                | (end)                   |                |
| 弹出历史记录 or 在弹出的列表中上移                | 是                | (pageup)                |                |
| 在弹出的列表中下移                                | 是                | (pagedown)              |                |
| 删除一行                                          | 是                | (ctrl+e)d               |                |
| 删除光标前单词                                    | 是                | (ctrl+e)(smallleft)     |                |
| 删除光标后单词                                    | 是                | (ctrl+e)(smallright)    |                |
| 删除光标前所有内容                                | 是                | (ctrl+e)(smallbigleft)  |                |
| 删除光标后所有内容                                | 是                | (ctrl+e)(smallbigright) |                |
| 光标左移一个单词                                  | 是                | (ctrl+e)(left)          |                |
| 光标右移一个单词                                  | 是                | (ctrl+e)(right)         |                |
| 拷贝整行                                          | 是                | (ctrl+e)y               |                |
| 粘贴内容                                          | 是                | (ctrl+e)p               |                |
| 内容补全                                          | 否                | (tab)                   |                |
| 撤销刚才的操作                                    | 否(最好在shell中) | (ctrl+e)u               |                |
| 取消撤销刚才的操作                                | 否(最好在shell中) | (ctrl+e)U               |                |
| 历史记录列表                                      | 否(最好在shell中) | (ctrl+e)e               |                |
| 文件列表                                          | 否(最好在shell中) | (ctrl+e)f               |                |
| 其它shell快捷键功能(前面全都加一个ctrl+e作为前缀) | 否(最好在shell中) | (ctrl+e)(任意键)        |                |

### 光标/焦点移动接口

有些软件有窗口(window),有些软件有标签页面(tab),或者是类似的概念来将其管理的资源或者是软件的功能做一个物理上的划分,在gui中,
通过移动鼠标和点击左键,一般能够把焦点或者光标切换到那些窗口,但是如果通过快捷键来操作,则没有一个统一的操作模式,其实(small)上下左右
和上下左右能够很好的进行表意.还有一个按键.也是是否好按也好表意, 它代表任意的,需要搜索或者输入内容的.


这里对window和tab下一个定义,有些软件中把tab叫做workspace,有些软件中把window叫做pane或者其他的.window把软件所控制的屏幕切割成几个部分,
tab是window的集合,几个window加起来合成一个tab,统一时间软件控制的可视区域同时只能有一个tab.窗口分成浮动和固定两种,浮动窗口可以
属于一个tab或者不属于任意的一个tab. tab一般只有左右移动,没有上下移动的需求.tab有可能会有编号代表顺序.


我们可以给tab或者window起名字, 比如在窗口管理器中,可以给把某个tab分配给文本编辑器并取名为editor,那么可以后面通过e来快捷的访问它.


所有窗口和tab的集合叫做一个session,软件同一时刻只能存在一个session.session也可以起名字,软件启动时可以启动一个默认的session.


| 功能描述                                                                  | 是否必须 | norman布局(rinodest)                 | qwert布局      |
| --------------                                                            | -------- | --------------                       | -------------- |
| 焦点移动到上边窗口(有些情况切换窗口无法用leader键做到,比如vscode中有时候) | 是       | (leader+smallup) or (shift+up)       |                |
| 焦点移动到下边窗口                                                        | 是       | (leader+smalldown) or (shift+down)   |                |
| 焦点移动到左边窗口                                                        | 是       | (leader+smallleft) or (shift+left)   |                |
| 焦点移动到右边窗口                                                        | 是       | (leader+smallright) or (shift+right) |                |
| 焦点移动到上边(浮动)tab                                                   | 是       | (leader+up)                          |                |
| 焦点移动到下边(固定)tab                                                   | 是       | (leader+down)                        |                |
| 焦点移动到左边tab                                                         | 是       | (leader+left)                        |                |
| 焦点移动到右边tab                                                         | 是       | (leader+right)                       |                |
| 弹出所有tab的列表                                                         | 是       | (leader+t).                          |                |
| 访问特定名字的tab                                                         | 是       | (leader+t)(字母键)                   |                |
| 访问特定编号的tab                                                         | 是       | (leader+t)(数字键,0代表最后的)       |                |
| 弹出所有window的列表(当前tab或者所有tab这个看自己兴趣和不同的软件不同)    | 是       | (leader+w).                          |                |
| 访问特定名字的window                                                      | 是       | (leader+w)(字母键)                   |                |
| 焦点移动到上一个历史位置                                                  | 是       | (leader+g)(smallleft)                |                |
| 焦点移动到下一个历史位置                                                  | 是       | (leader+g)(smallright)               |                |
| 搜索历史焦点位置并移动                                                    | 是       | (leader+g).                          |                |
| 焦点移动到自定义的位置                                                    | 是       | (leader+g)(t可不按)(任意键)          |                |
| 查找切换session                                                           | 否       | (leader+s).                          |                |

### 窗口和tab的修改接口

窗口和tab有些可以修改名字, 都可以修改相对的位置, 窗口还可以修改大小和方向, 甚至可以变成浮动或者固定的属性,这些接口通过
上下左右和(small)(big)上下左右可以轻松实现.当然在有些软件中没有提供这么丰富的窗口和tab的操作,所以这些都是可选的.不提供对应操作时
kui最好能够提供一个提示来指示该操作为实现.这里可以这样记忆, 移动(move),移动到(arrive to),大小(size)

| 功能描述                                 | 是否必须 | norman布局(rinodest)   | qwert布局      |
| --------------                           | -------- | --------------         | -------------- |
| 修改窗口位置(上移)                       | 是       | (leader+m)(smallup)    |                |
| 修改窗口位置(下移)                       | 是       | (leader+m)(smallup)    |                |
| 修改窗口位置(左移)                       | 是       | (leader+m)(smallleft)  |                |
| 修改窗口位置(右移)                       | 是       | (leader+m)(smallright) |                |
| 修改窗口位置(浮动)(向上层移动)           | 是       | (leader+m)(up)         |                |
| 修改窗口位置(固定)(向下层移动)           | 是       | (leader+m)(down)       |                |
| 修改tab位置(左移)                        | 是       | (leader+m)(left)       |                |
| 修改tab位置(右移)                        | 是       | (leader+m)(right)      |                |
| 切换窗口浮动状态                         | 是       | (leader+m)f            |                |
| 修改窗口大小(上移变大)                   | 是       | (leader+s)(smallup)    |                |
| 修改窗口大小(下移变小)                   | 是       | (leader+s)(smallup)    |                |
| 修改窗口大小(左移变小)                   | 是       | (leader+s)(smallleft)  |                |
| 修改窗口大小(右移变大)                   | 是       | (leader+s)(smallright) |                |
| 修改窗口大小(最大化)(maxmize)            | 是       | (leader+s)m            |                |
| 修改窗口大小(占满)(zen)                  | 是       | (leader+s)z            |                |
| 修改窗口大小(最小化)(hidden)             | 是       | (leader+s)h            |                |
| 修改窗口名字                             | 是       | (leader+s)w            |                |
| 修改tab名字                              | 是       | (leader+s)t            |                |
| 修改字体大小(大)                         | 否       | (leader+s)u            |                |
| 修改字体大小(小)                         | 否       | (leader+s)d            |                |
| 窗口移动到特定tab(左边的tab)             | 是       | (leader+a)(left)       |                |
| 窗口移动到特定tab(右边的tab)             | 是       | (leader+a)(right)      |                |
| 窗口移动到特定tab并且焦点不变(左边的tab) | 是       | (leader+a)(home)       |                |
| 窗口移动到特定tab并且焦点不变(右边的tab) | 是       | (leader+a)(end)        |                |
| 窗口移动到特定tab(按tab名字)             | 是       | (leader+a)(字母键)     |                |
| 窗口移动到特定tab并且焦点不变(按tab名字) | 是       | (leader+a)(大写字母键) |                |
| 窗口移动到特定tab(按tab编号)             | 是       | (leader+a)(数字键)     |                |
| 窗口移动到tab(通过查找tab名字)           | 是       | (leader+a).            |                |

### 创建资源接口

很多软件都可以创建一个它能管理的新的资源,比如窗口管理器可以窗口新的窗口,文件编辑器可以创建新文件等.

| 功能描述                                 | 是否必须 | norman布局(rinodest) | qwert布局      |
| --------------                           | -------- | --------------       | -------------- |
| 新建垂直窗口打开资源                     | 是       | (leader+l)v          |                |
| 新建水平窗口打开资源                     | 是       | (leader+l)h          |                |
| 新建浮动窗口打开资源                     | 是       | (leader+l)f          |                |
| 新建窗口打开资源(可以输入要打开什么资源) | 是       | (leader+l)w          |                |
| 新建tab打开资源                          | 是       | (leader+l)l          |                |
| 新建tab打开资源(可以输入要打开什么资源)  | 是       | (leader+l)t          |                |
| 刷新当前窗口(重新加载窗口)               | 是       | (leader+l)r          |                |
| 强制重新加载窗口(无需确认)               | 否       | (leader+l)R          |                |
| 重新加载配置                             | 是       | (leader+l)c          |                |
| 打开命令菜单                             | 是       | (leader+l).          |                |
| 其它快捷操作(自己定义并记忆一些操作)     | 是       | (leader+.)(字母键)   |                |
| 保存当前session到默认session             | 否       | (leader+l)s          |                |
| 保存当前session,可以输入session的名字    | 否       | (leader+l)S          |                |

### 删除资源接口

删除的接口里面没有对session进行删除的, 因为session不会常常修改,最常用的也是保存当前app状态到默认session,或者自动保存session,所以session只提供增和查的接口,没有删和改.


| 功能描述                                                           | 是否必须 | norman布局(rinodest) | qwert布局      |
| --------------                                                     | -------- | --------------       | -------------- |
| 关闭光标所在窗口(这个最常见,所以安排一个最容易按的)                | 是       | (leader+q)q          |                |
| 关闭光标所在窗口                                                   | 是       | (leader+q)w          |                |
| 关闭光标所在 tab                                                   | 是       | (leader+q)t          |                |
| 关闭除了光标所在的窗口以外的窗口(only)                             | 是       | (leader+q)o          |                |
| 关闭除了光标所在的 tab 以外的 tab (launch的时候也用了l)            | 是       | (leader+q)l          |                |
| 关闭所有,退出程序                                                  | 是       | (leader+q)a          |                |
| 和对应的小写字母的功能一样, 只不过即使后台有未完成的工作也不用确认 | 是       | (leader+q)(QWTOLA)   |                |

### 拷贝资源接口

拷贝一个已经存在的资源到系统剪切板是一个非常非常常见的操作, 比如文本编辑器可以拷贝里面的名字或者拷贝内容,
文件管理器需要拷贝它所管理文件的路径等, 又或者不太常见的, 在窗口管理器中,截图其实就是一种拷贝操作, 窗口管理器
所管理的资源其实就是视觉上的一张张图片.这个操作的接口需要分类来实现,不同类型的软件不能用同一个.在vim里面这个叫做yank,
因为copy这个我希望留给cv大法使用,所以这个使用yank来辅助记忆, 和vim里面的拷贝一致

| 功能描述           | 软件类型   | norman布局(rinodest)                           | qwert布局      |
| --------------     | --------   | --------------                                 | -------------- |
| 拷贝窗口名字       | 所有       | (leader+y)w 在窗口管理器中设为W 相比截图不常用 |                |
| 拷贝tab名字        | 所有       | (leader+y)t                                    |                |
| 截取全屏幕         | 窗口管理器 | (leader+y)y                                    |                |
| 截取当前窗口的内容 | 窗口管理器 | (leader+y)w                                    |                |
| 截取矩形区域的内容 | 窗口管理器 | (leader+y)r                                    |                |
| 录制gif            | 窗口管理器 | (leader+y)g                                    |                |
| 录制video          | 窗口管理器 | (leader+y)v                                    |                |
| 拷贝当前url        | 浏览器     | yy                                             |                |
| 拷贝输入框         | 浏览器     | yi                                             |                |
| 拷贝页面中小段内容 | 浏览器     | yv                                             |                |

### coding专有接口

vim提供了很多coding中很棒的接口,但是由于年度太久,加上有些快捷键和kui的理念有点冲突,比例kui中,vim不能使用ctrl或者alt作为组合键,
所有的功能必须用,作为leader.而且,vim的移动模式和选择模式缺少一个指哪打哪的功能,这个在目前流行的vim的插件flash中搞定.

| 功能描述                   | 是否必须 | norman布局(rinodest) | qwert布局      |
| --------------             | -------- | --------------       | -------------- |
| 光标跳转(搜索字符跳转)     | 否       | s                    |                |
| 选择一段内容(搜索选择)     | 否       | l                    |                |
| 运行一段代码               | 否       | ,dr                  |                |
| 运行当前函数               | 否       | ,df                  |                |
| 运行当前工程               | 否       | ,dp                  |                |
| 跳转到当前光标所在文件,url | 否       | gf                   |                |

## 跨桌面操作系统

在windows实现kui的过程中, 我遇到了很多很多的困难,有一些软件有着奇怪的bug, 但是通过autohotkey这个改键的工具和github上的自由软件,大部分的都被绕过去了.


下面列出我在每个系统中使用的实现kui理念的软件.十分感谢这些软件的创作者.

| 操作系统  | 桌面管理器 | 键盘修改                | 鼠标模拟  | 终端      | 浏览器                    | 文件管理器 | 表格浏览器 | 终端multiplexer |
| --------- | --------   | --------                | --------- | --------- | ------                    | ---        | ---        | ---             |
| windows   | glazewm    | autohotkey和touchcursor | mouseable | wezterm   | chromium和插件surfingkeys | lf         | visidata   | zellij          |

还有docker, 在coding中有很多cli的软件, 有些跨系统, 有些没有, 有了docker以后, 对cli软件而言我只用考虑docker内的ubuntu系统
有没有就可以了, 而不用考虑windows中cli软件糟糕的生态,这极大降低了kui在不同操作系统的迁移成本,例如lf和visidata这些cli的软件在
windows上总是会有那么一些奇奇怪怪的bug,甚至有些不支持windows,例如zelij,在linux中就好用多了.不用wsl2主要是我还有一个mac的
笔记本,用docker迁移到macos更方便.


[1]: https://geekhack.org/index.php?topic=51069.0