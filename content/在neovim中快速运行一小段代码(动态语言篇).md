---
title: "在neovim中快速运行一小段代码(动态语言篇)"
date: 2023-11-06T11:24:46+08:00
draft: false
tag: "vim"
---

在编程的过程中,如何检验一断逻辑或者一个函数是否按照预期的来运行,一般情况下都是通过编写单元测试来实现.这在一个稍微长期一点的项目中
是有较大收益的,但是有些情况下并不是很适用.例如,短期的外包项目,又或者是在一些游戏项目中,demo期间和上线之前,策划的需求变化会非常的
频繁,这个时候单元测试就显得有些鸡肋.这个时候如何运行一个大工程中的一小段逻辑,目前业界并无通用的方法.


## 编辑器插件

如果对于只用到了标准库的动态语言而言,这个还算是很简单的一件事情,核心的思想就是把光标选择到的代码切割出来,把它写到一个临时文件
中,然后运行这个临时文件,假如是bash脚本,则运行bash temp.sh, 如果是python脚本, 则运行python temp.py.通过这个原理,vscode和
neovim中都有插件来完成这个事情.


vscode中出名的插件是微软工程师编写的[code_runner](https://github.com/formulahendry/vscode-code-runner),neovim中我觉得不错的插件是[sniprun](https://github.com/michaelb/sniprun)

## 当前这些插件的缺陷

这些插件都有一些缺陷,他们对于运行某一个文件支持的还不错,而对于运行一小段代码支持的不太行,他们都没有提供好点的办法支持函数的
调用,
比如在python脚本中a函数调用了b函数,即使这两个函数在一个文件,如果我只用光标框起a函数,这两个插件默认这个b函数是不会被选择进文件的,运行起来毫无疑问会报错.


对写法非常灵活的动态语言而言,那些插件似乎也只能做成这样,但是这样的话它的适用情况很狭隘,基本只能用于学习编程语言,是没有办法
在实际的工程中使用来满足运行一段逻辑的需求的.

## 少即是多,限制代码的写法来解决上面提到的问题

我一般写bash脚本的时候都是一个文件就搞定了,并且文件中会用main作为主函数,并且在最后一行调用main函数.这样可以保证所有的逻辑都
写在某一个函数中.我发现这种写法天生就能搞定上面的a函数调用b函数的问题, 只需要把文件中最后一行main函数调用的地方给去掉,
然后把光标所选择的代码放置到文件的最后, 即可. 或者将这些代码放在一个特定名字的函数,如___main___中,然后调用这个
___main___函数即可完美解决bash中运行光标所选代码的问题,光标运行的代码和在实际的文件中运行的代码的作用会完全一样.


这个思路无论对vscode或者是neovim或者其他的编辑器都是适用的.因为我现在写代码主要使用的编辑器是neovim,vscode主要用在网页上阅读github
上的开源代码而不用将其clone下来.所以我后面的具体实现上都以neovim的lua代码作为描述,vscode应该也能通过同样思路来实现.


还有一个情况,通常而言所有的语言虽然不是必须要格式化以后才能运行,但是对于运行一小段代码而言,一个通用的前提条件是整个工程的代码
都已经格式化过了, 这个在所有的编辑器中都很容易配置成自动完成,这也属于限制代码写法的一部分.


所以,对于解释性的脚本语言而言,如果我们限制所有代码必须写函数中并且会通过main函数作为唯一的入口来调用的话,那么
把光标所在文件的所有内容除去main函数调用的地方全部克隆过去并将光标所选择的代码拷贝到___main___函数中,并最后调用___main___
函数是一个通用的做法,他能保证对于所有单文件脚本,不管它有没有调用外部库,都能够做到完美的运行大工程中的一小段代码.

### bash

下面的代码是对bash脚本的实现,
通过`line:match("^main$")`来过滤掉main这一行调用,并且把选择的代码包裹进___main___函数中,然后调用.

```lua
local function createBashCodeString()
  local lines = vim.api.nvim_buf_get_lines(0, 0, -1, false)
  local filtered_content = {}
  for _, line in ipairs(lines) do
    if line:match("^main$") then
      goto continue
    end
    table.insert(filtered_content, line)
    ::continue::
  end
  table.insert(filtered_content, "___main___(){")
  local selectLines = util.get_visual_selection_content()
  for i, line in ipairs(selectLines) do
    if i == 1 then
      if line:sub(-1) == "{" then
        goto continue
      end
    end
    if i == #selectLines then
      if line:sub(1, 1) == "}" then
        goto continue
      end
    end
    table.insert(filtered_content, line)
    ::continue::
  end
  table.insert(filtered_content, "}")
  table.insert(filtered_content, "___main___")
  local codestring = table.concat(filtered_content, "\n")
  return codestring
end
```

### python

python也类似,与上面不同的有两点,一是main函数的调用检测,python是带括号来调用,
二是需要限制python写代码需要用tab作为缩进.

```lua
local function createPythonCodeString()
  local lines = vim.api.nvim_buf_get_lines(0, 0, -1, false)
  local filtered_content = {}
  for _, line in ipairs(lines) do
    if string.len(line) >= 5 and string.sub(line, 1, 5) == "main(" then
      goto continue
    end
    table.insert(filtered_content, line)
    ::continue::
  end
  table.insert(filtered_content, "def ___main___():")
  local selectLines = util.get_visual_selection_content()
  local fix = 1
  for i, line in ipairs(selectLines) do
    if i == 1 then
      local count = tabCount(line)
      fix = fix - count
      if string.len(line) >= count + 4 and string.sub(line, count + 1, count + 4) == "def " then
        fix = fix - 1
        goto continue
      end
    end
    if fix > 0 then
      table.insert(filtered_content, "\t" .. line)
    elseif fix < 0 then
      table.insert(filtered_content, string.sub(line, -fix))
    else
      table.insert(filtered_content, line)
    end
    ::continue::
  end
  table.insert(filtered_content, "___main___()")
  local codestring = table.concat(filtered_content, "\n")
  return codestring
end
```

### javascript和typescript

js和python类似的,都是通过前5个字符为main(来判断main函数的调用.

```lua
local function createJsCodeString()
  local lines = vim.api.nvim_buf_get_lines(0, 0, -1, false)
  local filtered_content = {}
  for _, line in ipairs(lines) do
    if string.len(line) >= 5 and string.sub(line, 1, 5) == "main(" then
      goto continue
    end
    table.insert(filtered_content, line)
    ::continue::
  end
  table.insert(filtered_content, "function ___main___(){")
  local selectLines = util.get_visual_selection_content()
  for i, line in ipairs(selectLines) do
    if i == 1 then
      if string.find(line, "function ") then
        goto continue
      end
    end
    if i == #selectLines then
      if line:sub(1, 1) == "}" then
        goto continue
      end
    end
    table.insert(filtered_content, line)
    ::continue::
  end
  table.insert(filtered_content, "}")
  table.insert(filtered_content, "___main___()")
  local codestring = table.concat(filtered_content, "\n")
  return codestring
end

```
### lua

lua有一点点不一样, 我目前写lua主要在neovim中写, 不用它作为通用的编程语言, 除了去掉main函数的调用以外,
还需要去掉在lua的模块中的return语句. 否则添加到后面的代码都被return掉了.

```lua
local function createLuaCodeString()
  local filtered_content = {}
  table.insert(filtered_content, "-- vim")
  local lines = vim.api.nvim_buf_get_lines(0, 0, -1, false)
  for _, line in ipairs(lines) do
    if string.len(line) >= 5 and string.sub(line, 1, 5) == "main(" then
      goto continue
    end
    if string.len(line) >= 6 and string.sub(line, 1, 6) == "return" then
      break
    end
    table.insert(filtered_content, line)
    ::continue::
  end
  table.insert(filtered_content, "local function ___main___()")
  local selectLines = util.get_visual_selection_content()
  for i, line in ipairs(selectLines) do
    if i == 1 then
      if string.find(line, "function ") then
        goto continue
      end
    end
    if i == #selectLines then
      if line:sub(1, 3) == "end" then
        goto continue
      end
    end
    table.insert(filtered_content, line)
    ::continue::
  end
  table.insert(filtered_content, "end")
  table.insert(filtered_content, "___main___()")
  local codestring = table.concat(filtered_content, "\n")
  return codestring
end
```

## 其他

上面的几个语言是我最经常用的动态语言,因为动态语言写法千奇百怪, 所以可能有一些情况没法覆盖到, 一个常见的就是运行闭包内的函数.
很还有一些我不常用的lisp系语言如haskell.

