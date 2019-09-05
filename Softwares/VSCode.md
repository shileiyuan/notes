webpack设置了alias时，vscode需要做相应的设置才能正确跳转到定义的地方。

添加jsconfig.json
```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "baseUrl": "./",
    "paths": {
      "~/*": [
        "src/*"
      ]
    },
    "jsx": "react",
    "target": "es6",
    "module": "commonjs"
  },
  "exclude": [
    "node_modules"
  ]
}
```

### 快捷键

#### Basic Editing
* shift + cmd + k: Delete line
* cmd + enter: Insert line below
* shift + cmd + enter: Insert line above
* option + down: Move line down
* option + up: Move line up
* option + shift + down: Copy line down
* option + shift + up: Copy line up
* cmd + [: Indent line
* cmd + ]: Outdent line
* ctrl + pageup: Scroll line up
* ctrl + pagedown: Scroll line down
* cmd + pageup: Scroll page up
* cmd + pagedown: Scroll page down
* home end pageup pagedown
* cmd + i: Select current line
* cmd + f2: Select all occurences of current word
* cmd + /: Toggle line comment
* shift + option + a: Toggle block comment
* option + shift + f: Format document
* f12: Go to definition
* option + f12: Peek definition
* shift + f12: Show references
* f2: Rename symbol
* cmd + .: Quick fix
* ctrl + j: 合并行
* ctrl + k: 删除右侧所有内容
* 




#### search
* cmd + f: Search
* option + cmd + f: Replace
* cmd + g: Find next
* cmd + shift + g: Find previous
* option + cmd + c: Toggle find case sensitive
* option + cmd + w: Toggle find whole word
* option + cmd + r: Toggle find regex



#### Cursor
* cmd + u: Undo last cursor operation
* ctrl + -: 前进
* ctrl + =: 后退
* optoin + left: Cursor word start left
* option + right: Cursor word end right
* ctrl + option + left: Cursor word part left // 跟sizeup冲突，不常用
* shift + left: Cursor left select
* shift + up: Cursor up select
* shift + pageup: Cursor page up select
* cmd + shift + left: Cursor home select
* option + shift + left: Cursor word start left select
* ctrl + shift + \\: Jump to the begin bracket that contains the cursor. Press the shortcut again jump to the end bracket.
* ctrl + 



### 面板
* cmd + /: Split editor
* cmd + b: Toggle sidebar visibility
* cmd + shift + e: Open search panel
* cmd + shift + f: Open explore panel
* cmd + left 或 home： Cursor home
* cmd + shift + left: Cursor home select


### 问题
* 折叠括号的快捷键


### 重要

option + p : 显示打开的文件列表

ctrl + r : 显示最近打开的项目

cmd + J: 切换终端面板

拆分编辑器： cmd + \
聚焦到编辑器1: cmd + 1
合并所有编辑器组： cmd + option + J

**自定义：** 

option + J：聚焦到终端
option + K: 切换活动终端
option + L: 新建终端
option + C: 关闭活动终端
option + \: 拆分终端


### 设置：

代码折叠：
Folding Strategy: indentation



### debug

