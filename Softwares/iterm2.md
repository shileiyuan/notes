| 说明               | 快捷键                                                  |
| ------------------ | ------------------------------------------------------- |
| 新建标签           | command + t                                             |
| 切换标签           | command + 数字 或 command + 左右方向键                  |
| 切换全屏           | command + enter                                         |
| 查找               | command +f                                              |
| 垂直分屏           | command + d                                             |
| 水平分屏           | command + shift + d                                     |
| 切换屏幕           | command + option + 方向键 或 command + [ 或 command + ] |
| 移动屏幕           | command + shift + 方向键                                |
| 查看历史命令       | command + ;                                             |
| 查看剪贴板历史     | command + shift + h                                     |
| 清除当前行         | ctrl + u                                                |
| 到行首             | ctrl + a                                                |
| 到行尾             | ctrl + e                                                |
| 前进后退           | ctrl + f/b (相当于左右方向键)                           |
| 上一条命令         | ctrl + p                                                |
| 搜索命令历史       | ctrl + r                                                |
| 删除当前光标的字符 | ctrl + d                                                |
| 删除光标之前的字符 | ctrl + h                                                |
| 删除光标之前的单词 | ctrl + w                                                |
| 删除到文本末尾     | ctrl + k                                                |
| 交换光标处文本     | ctrl + t                                                |
| 清屏1              | command + r                                             |
| 清屏2              | ctrl + l                                                |


双击选中，三击选中整行，且选中即复制
点击文件或目录可以直接打开，也可以调用open命令


### 设置
https://www.jianshu.com/p/83c38271b09c

**显示/隐藏Dock图标**
```bash
隐藏：/usr/libexec/PlistBuddy  -c "Add :LSUIElement bool true" /Applications/iTerm.app/Contents/Info.plist

显示： /usr/libexec/PlistBuddy  -c "Delete :LSUIElement" /Applications/iTerm.app/Contents/Info.plist
```
然后重启 iTerm。

**开机启动**
系统账户设置中添加 iTerm 为登录启动项即可。
系统偏好设置 - 用户与群组 - 登陆项 - 新增