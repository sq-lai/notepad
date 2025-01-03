
# Vim 使用总结

## 1. Vim 简介
Vim（Vi IMproved）是一个强大的**文本编辑器**，广泛用于程序开发和文本处理。它是 Vi 编辑器的增强版本，具有更多的功能和更高的可扩展性。

## 2. 启动和退出
- 启动 Vim：**在终端中输入 `vim` 或 `vim filename`。**
- 退出 Vim：
  - `:q`：退出 Vim（如果没有修改）。
  - `:q!`：强制退出，不保存修改。
  - `**:wq**` 或 `ZZ`：**保存并退出。**

## 3. 模式
Vim 主要有三种模式：
- 普通模式：用于浏览和操作文本。
- 插入模式：用于插入文本，**按 `i` 进入插入模式**，**按 `Esc` 返回普通模式。**
- 命令模式：用于执行命令，**按 `:` 进入命令模式。**

## 4. 基本操作
### 光标移动
键盘箭头移动就好

### 编辑(在普通模式下)
- `i`：进入插入模式
- `a`：在光标后插入
- `o`：在当前行下插入新行
- `x`：删除光标所在字符
- `dd`：删除整行
- `yy`：复制整行
- `p`：粘贴

### 撤销与重做
- `u`：撤销
- `Ctrl + r`：重做

## 5. 搜索与替换
- `/pattern`：向下搜索模式
- `?pattern`：向上搜索模式
- `n`：跳到下一个搜索结果
- `N`：跳到上一个搜索结果
- `:%s/old/new/g`：替换整个文件中的所有 `old` 为 `new`

## 6. 其他常用命令
- `:set number`：显示行号
- `:set nonumber`：隐藏行号
- `:syntax on`：启用语法高亮
- `:help`：打开帮助文档
- `:w filename`：另存为新文件

## 7. 插件管理
- 使用插件管理器（如 Vundle 或 vim-plug）来管理和安装插件。
- 在 `.vimrc` 文件中配置插件。

## 8. 常用配置
在 `~/.vimrc` 文件中，可以进行如下配置：
```vim
set number           " 显示行号
set tabstop=4       " 制表符宽度
set shiftwidth=4    " 自动缩进宽度
set expandtab       " 使用空格代替制表符
syntax on           " 启用语法高亮
```

## 9. 小技巧
- 使用 `Ctrl + o` 在普通模式和插入模式间切换。
- 使用 `Ctrl + f` 和 `Ctrl + b` 向前和向后翻页。
- 可以通过 `:e filename` 在 Vim 中打开新文件。

## 10. 结语
Vim 是一个功能强大的文本编辑器，熟练掌握其操作和命令可以大大提高工作效率。多加练习，并尝试不同的插件和配置，探索 Vim 的更多功能。
