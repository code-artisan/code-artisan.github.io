---
title: 'Laravel Cast 教程总结：精通 Sublime【转载】'
date: 2016-12-27 17:22:37
tags: [sublime,text]
published: true
hideInList: false
feature: 
---

Package Control 是 Sublime 的包管理插件，可用来管理其他插件。安装[地址](https://packagecontrol.io/installation)。

<!-- more -->

安装完之后，调出命令面板 ( cmd/ctrl + shift + p )，输入 Package Control : Install Package，再搜索 Material Theme 即可。该主题作者所推荐，详情请查看 [主题链接](https://github.com/equinusocio/material-theme)

### 1.2 主题配置

安装主题之后，还需要对主题进行配置，Settings - User 里面保存了用户的自定义配置信息，将会覆盖默认的信息。所有的配置文件都以 json 的格式定义，我们按照主题作者的建议进行配置:

```js
{
  "ignored_packages":
  [
    "Vintage"
  ],

  // 主题信息
  "theme": "Material-Theme.sublime-theme",
  "color_scheme": "Packages/Material Theme/schemes/Material-Theme.tmTheme",
  "overlay_scroll_bars": "enabled", // 水平垂直滚动条
  "line_padding_top": 3,  // 每行顶部行间距:像素
  "line_padding_bottom": 3, // 每行底部行间距:像素
  "always_show_minimap_viewport": true, // 总是选择迷你预览

  // Mac Retina 推荐配置
  "bold_folder_labels": true, // 侧边栏文件夹名字加粗
  "indent_guide_options": [ "draw_normal", "draw_active" ],   // 制表位的对齐线高亮
  "font_options": [ "gray_antialias" ]
}
```

### 1.3 快速切换配色风格

通过 Package Control 安装插件 Colorsublime，可用于快速切换主题的颜配色风格，使用[教程](https://packagecontrol.io/packages/Colorsublime)。作者使用的主题为 Facebook，本人则更习惯用 Monokai。

### 1.4 去除行数显示

如果不喜欢显示行数，可以在用户配置中将 line_numbers 设置为 false，去掉行数的话，可以将外边距设置为 0，避免间距过大。

```js
{
  "line_numbers": false,
  "margin":0
}
```

### 1.5 更改主题文件

对于不满足于主题配置的人来说，可以自定义修改主题的文件。首先，需要安装插件 Package​Resource​Viewer，参考[教程](https://packagecontrol.io/packages/PackageResourceViewer)。

使用：

在面板中输入 PackageResourceViewer: Extract Package;
输入 Material Theme;
Preferences → Brower Packages，就可以直接编辑主题文件;
### 1.6 更换字体

首先，需要先下载对应字体，比如作者使用的 [Fira Code](https://github.com/tonsky/FiraCode)。下载完字体并安装之后，就可以在用户设置里面进行配置了，顺便定一下字体的大小：

```js
  // 字体与字号选择
  "font_face": "Fira Code",
  "font_size": 15
```
字体的话，本人更喜欢用 Microsoft YaHei Mono。

1.7 更改全屏模式

进入全屏的快捷键为 ctrl + cmd + f，默认的全屏模式为 Lion 风格，如果想要修改成简单的全屏模式，可以进行配置:
```js
  "use_simple_full_screen": true,
````
该设置需要重启 Sublime 软件后才能生效。

## 2. 文件查找

### 2.1 cmd + p 快速定位文件

如果要查找某个文件，可以在左侧的文件目录中一个个查找，不过这种方式效率极低。推荐使用 cmd + p 来快速搜索并定位到某个文件。

### 2.2 cmd + r 快速定位某个函数

如果要查找文件中的某个函数，或者列出函数列表，可以使用 cmd + r 来快速搜索并定位，而不是在文件中一个个查找。

### 2.3 快速定位某个文件的某个函数

文件的定位与某个函数的定位可以结合在一起使用，先使用 cmd + p 调出命令面板，然后输入 文件名(模糊匹配即可)@函数名 即可快速定位到某个文件的某个块。

### 2.4 不知道文件名的情况下定位某个函数

在不知道某个函数所属哪个文件时，可以直接使用 ↑ + cmd + r 在项目里面搜索并定位某个函数片段，显然，这种方式的查找效率会比较低。

## 3. 文件操作

### 3.1 创建新文件

首先需要安装插件 [AdvancedNewFile](https://github.com/skuroda/Sublime-AdvancedNewFile)。

先按 cmd + alt + n ，然后输入要创建的文件，比如 app/test.php，就可以在相应的目录下创建文件，而且目录可以用 tab 键进行补齐，方便快捷。

### 3.2 在当前目录下创建新文件

上述创建文件的方式还是略麻烦，也可以创建于某个选中的文件的同目录文件。比如先左侧目录中的 test.php 文件，按 cmd + alt + n，然后输入冒号加要创建的文件名，比如 :a.html，这样就可以在 test.php 所在目录下创建 a.html 文件了。

### 3.3 删除或重命名文件

先用 cmd + shift + p 调出面板；
输入 ANF, 就可以看到相应的文件操作选项
使用该方法的时候，会出现一个问题，重命名文件时，新的文件目录会自动变为项目根目录。有两种解决方案：

在重命名文件时，在新的文件名前面加符号 :；
在 AdvancedNewFile 的自定义设置里面添加 "rename_default": "<filenpath>"，这样重命名时，就会显示该文件的完整路径。

## 4. 快速分屏.md

最简单的分屏是使用 Sublime 自带的分屏，快捷键为 cmd + option + 数字，不过这里推荐一款更为方便的插件 Origami，安装完之后，只需要在 Origami 的用户快捷键绑定中输入:

```js
[
  { "keys": [":","v","s","p"], "command": "create_pane", "args": {"direction": "right","give_focus":true} },
  { "keys": [":","s","p"], "command": "create_pane", "args": {"direction": "down","give_focus":true} },
  { "keys": [":","b","d"], "command": "destroy_pane", "args": {"direction": "self"} },
]
```
第一条代表在右边新建一个屏幕，第二条代表在下方新建一个屏幕，第三条代表删除当前屏幕。也就是说，只要在编辑器中输入 :vsp 就可以自动在右边新建一个屏幕，其他操作类似，都是通过输入来完成。

## 5. Vim 模式

Vim 模式的开启很简单，只需要改这个地方
```js
 "ignored_packages": ["Vintage"]
 ```
 改为
 ```js
  "ignored_packages": [""]
```
要进入 Vim 模式，只需要按 esc 键即可，Vim 的教程，可以参考其他地方，这里列出 Vim 入门教程的介绍：

第一讲
- 光标的基本移动：hjkl
- 退出
  - 不保存退出 ：q！
  - 保存并退出 ：wq
  - 回到教程 vimtutor

第二讲
- 删除 x
- 插入 i
- 删除一整个单词 dw
- 删除一整行 d$
- 删除一整个单词，不包括空格 de
- 删除一整行 dd
- 撤销 u
- 撤销一整行的改动 U
- 撤销以前的命令 ctrl +R

第三讲
- dd + p
- 单个字的替换 r
- 替换整个词并进入插入状态 cw
- 替换一整行并进入插入状态 c$

第四讲
- ctrl+g显示行号
- shift +g 跳到某一行
- 查找：/查找内容
- 同向查找 n
- 反向查找 shift +n
- 查找配对的括号：%
- 替换一个单词 :s/old/new
- 替换一整行 :s/old/new/g
- 替换某两行之间 :#,#s/old/new
- 替换全部:%s/old/new/g
- 替换全部并需要确认 :%s/old/new/new/g

第五讲
- 输入外部命令 :!外部命令 (dir，rm）
- 保存 :w 文件名
- 选择性保存 :#,#w 文件名
- 提取文件并插入 :r 文件名

第六讲
- 下行插入 o
- 上行插入 O 或者 shift +o
- 字符后插入 a
- 行末插入 A
- 逐一替换 R
- 忽略大小写 :set ic

第七讲：帮助
- 按下 F1 或者输入 :help
- :q 关闭帮助界面
- 查找主题 :help 命令

第八讲：创建启动脚本
1. 开始编辑vimrc文件，这取决于您所使用的操作系统∶
:edit ~/.vimrc 这是Unix系统所使用的命令
:edit $VIM/_vimrc 这是Windows系统所使用的命令

2. 接着导入vimrc范例文件∶
:read $VIMRUNTIME/vimrc_example.vim

3. 保存文件，命令为∶
:write

## 6. 多光标操作

多光标操作的意味着你可以同时编辑多个单词。比如一个文件中，有一个多次出现的变量 $personName，如果要将其改为 $name，一种方法是使用查找/替换操作。也可以使用快捷键来完成操作：

- cmd + d 选中该单词，然后可以进行编辑
- cmd + d 持续按 d 键，可以同时选择多个同名单词
- ctrl + cmd + g 同时选择该页的所有同名单词
使用之后就会发现，该操作比常用的查找替换要更为灵活和方便。

## 7. 优化 PHP 工作流

### 7.1 使用 snippet

优化 PHP 工作流的第一种方法是自己创建 snippet，通过 snippet，将实现以下几个功能：

- met + tab : 生成一个 PHP 函数
- class + tab : 生成一个 PHP 类
- _c + tab : 生成一个 PHP 构造函数

首先，选择 Tools -> Developer -> New Snippet，创建第一个条 snippet，保存为 PHP Methods.sublime-snippet
```html
<snippet>
  <content><![CDATA[
${1:public} function ${2}()
{
  ${3}
}
]]></content>
  <tabTrigger>met</tabTrigger>
  <scope>source.php</scope>
</snippet>
```
该 snippet 对应以下模板，$ 键为编辑的占位符，按 tab 可以进一步切换:

```php
public function ()
{

}
```
为了方便使用，可以用 met 来代表 public 方法，用 pmet 来代表 protected 方法：

PHP Methods.sublime-snippet:
```html
<snippet>
  <content><![CDATA[
public function ${1}()
{
  ${2}
}
]]></content>
  <!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
  <tabTrigger>met</tabTrigger>
  <!-- Optional: Set a scope to limit where the snippet will trigger -->
  <scope>source.php</scope>
</snippet>
```
PHP Protected Methods.sublime-snippet:
```html
<snippet>
  <content><![CDATA[
protected function ${1}()
{
  ${2}
}
]]></content>
  <!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
  <tabTrigger>pmet</tabTrigger>
  <!-- Optional: Set a scope to limit where the snippet will trigger -->
  <scope>source.php</scope>
</snippet>
```
最后在创建类和构造函数对应的 snippet:

New Class.sublime-snippet:
```html
<snippet>
  <content><![CDATA[
class ${1:Person}
{
  ${2}
}
]]></content>
  <tabTrigger>class</tabTrigger>
  <scope>source.php</scope>
</snippet>
Constructor.sublime-snippet:
<snippet>
  <content><![CDATA[
public function __construct(${1})
{
  ${2}
}
]]></content>
  <tabTrigger>_c</tabTrigger>
  <scope>source.php</scope>
</snippet>
```
7.2 使用插件

另一种方式就是直接安装插件，推荐两款：

- PHP Getters and Setters
- PHP Companion

PHP Getters and Setters 的使用较为简单，安装完之后，重启。然后调出面板输入 get 或 set 关键字就可以针对不同变量生成函数了。

另外一个插件为 PHP Companion，默认不提供任何快捷键，需要自己设置。默认设置可以参考:
```js
[
  { "keys": ["f6"], "command": "expand_fqcn" },
  { "keys": ["shift+f6"], "command": "expand_fqcn", "args": {"leading_separator": true} },
  { "keys": ["f5"], "command": "find_use" },
  { "keys": ["f4"], "command": "import_namespace" },
  { "keys": ["f3"], "command": "implement" },
  { "keys": ["shift+f12"], "command": "goto_definition_scope" },
  { "keys": ["f7"], "command": "insert_php_constructor_property" }
]
```
例如，开头要输入 use Symfony\Component\Finder\Finder，现在只需要输入 use Finder，然后按 f6，会自动补全。而如果是在类中使用了某个类，要在开头直接添加一行 use Symfony\Component\Finder\Finder 的话，只需要在输入 Finder 的时候直接按 f5 即可，开头会自动添加完整的引用。按 f7 会自动插入构造函数，而且可以连续按多次，对应插入多个属性:

private $PROPERTY;
```php
  public function __construct($PROPERTY)
  {
    $this->PROPERTY = $PROPERTY;
  }
```
## 8. 代码检查、规范化

### 8.1 从 Sublime 启动 Laravel Artisan

操作比较简单，只需要安装插件 Laravel 5 Artisan ，然后调出面板就可以搜索各种操作。

### 8.2 自动格式化代码

第一步，安装 [PHP CS Fixer](https://github.com/FriendsOfPhp/PHP-CS-Fixer)，该工具可以再命令行模式下对所选择的代码进行修复以满足规范，简单流程:

安装 composer global require fabpot/php-cs-fixer
指定修复文件 php-cs-fixer fix test.php --level=psr2
接下来要将第二步的功能放在 sublime 里面:

选择 Tools->Build System->New Build System;
编辑文件，并保存为 PSR-2.sublime-build;
```js
{
  "shell_cmd": "php-cs-fixer fix $file --level=psr2"
}
```
3. 打开 Tools->Build System，选择 PSR-2;
4. 要进行格式化的文件，只需要选择 Tools-> Build，或者使用 cmd+b 即可。
5. 默认会出现控制台用来显示格式化的基本信息，如果要设置成不显示的话，只需要在用户设置里面添加 "show_panel_on_build": false 即可。

### 8.3 代码检查工具

最后，推荐 Sublime 的代码检查工具 SublimeLinter，装完之后还需要安装对应的语言插件，比如 SublimeLinter-php。

> 转自：http://forum.laravelacademy.org/d/153-laravel-cast-sublime
