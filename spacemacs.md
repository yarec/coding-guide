# 基本操作
  | 按键               | 说明       |
  |--------------------|------------|
  | SPC SPC / M-x      | call func  |
  | C-g                | break      |
  | SPC qq  /  C-x C-c | quit emacs |
  | C-x                | 字符扩展   |
  |                    |            |
  
## 文件操作

  | 按键     | 说明                 | SPC |
  |----------|----------------------|:---:|
  | ff  / pf | 查找文件             | √  |
  | TAB      | last buffer          | √  |
  | bb       | buffer 列表（c-x b） | √  |
  | bd       | kill buffer（c-x k） | √  |
  | bx       | kill buffer & window | √  |
  | fed      | open .spacemacs      | √  |
  | feR      | sybc .spaces         | √  |
  | fL       | locate               | √  |

## 编辑

### 注释

  | 按键 | 说明 | SPC |
  |------|------|:---:|
  | ;    | 注释 | √  |

### 折叠

  | 按键 | 说明        | SPC |
  |------|-------------|:---:|
  | zm   | close folds | ×  |
  | zr   | open folds  | ×  |
  | zc   | close folds | ×  |
  | zo   | open folds  | ×  |
   
### 跳转

  | 按键 | 说明        | SPC | mode |
  |------|-------------|:---:|------|
  | sj   | fn list     | √  |      |
  | gd   | goto define | √  |      |
  | jj   | avy char    | √  |      |
  | jw   | avy word    | √  |      |
  | jb   | avy back    | √  |      |

### Search
  | 按键     | 说明                | SPC | mode |
  |----------|---------------------|:---:|------|
  | sad      | ag dir              | √  |      |
  | sap      | ag project          | √  |      |
  | saf      | ag files            | √  |      |
  | saa      | ag this file        | √  |      |
  | sab      | ag buffers          | √  |      |
  | s[ADBFP] | ag rengin or symbol | √  |      |

### Replace
  | 按键    | 说明         | SPC | mode |
  |---------|--------------|:---:|------|
  | /       | ag dir       | √  |      |
  | C-c C-e | helm-ag-edit | x   |      |
  | se      | iedit        | √  |      |
  | C-c C-c | finish       | x   |      |
  | C-c C-k | discard      | x   |      |
   


# Document
 doc

## Org-Mode
  https://www.cnblogs.com/holbrook/archive/2012/04/12/2444992.html

  | 按键    | 说明         |
  |---------|--------------|
  | C-c C-c | 格式化表格   |
  | Tab     | 下一个 Cell  |
  | S-Tab   | 折叠状态     |
  | Tab     | 当前大纲折叠 |
  |         |              |

## Markdown

# Developement
 dev

## Project
  | 按键 | 说明            |
  |------|-----------------|
  | pc   | make            |
  | pp   | switch project  |
  | pb   | prj buffers     |
  | pr   | prj recent      |
  | pd   | prj files       |
  | pG   | re gen tags     |
  | py   | goto def(gtags) |

## Version Controll

  | 按键    | 说明            | SPC | mode   |
  |---------|-----------------|:---:|--------|
  | pv      | vc-dir          | √  |        |
  | +       | update          | ×  | vc-dir |
  | =       | diff            | ×  | vc-dir |
  | m       | mark            | ×  | vc-dir |
  | u       | unmark          | ×  | vc-dir |
  | i       | register(add)   | ×  | vc-dir |
  | v       | next action(ci) | ×  | vc-dir |
  | x       | hide-up-to-date | ×  | vc-dir |
  | C-c C-c | commit          | ×  |        |
  | P       | push            | ×  | vc-dir |
  | <       | gg              | ×  | vc-dir |
  | >       | G               | ×  | vc-dir |
  | SPC     | next line       | ×  | vc-dir |
  | C-x vv  | next action     | ×  |        |
  | C-x v=  | diff            | ×  |        |
  | C-x v+  | update          | ×  |        |
  | C-x vd  | vc-dir          | ×  |        |
  | C-x vu  | revert          | ×  |        |
  | C-x vP  | push            | x   |        |

  next action:
** add file
** commit msg,
** resolve

## Meghanada
   | 按键        | 说明            |
   |-------------|-----------------|
   | C-c C-c C-c | Compile file    |
   | C-c C-c c   | Compile project |
   | C-c C-r i   | import all      |
   | C-c C-r o   | optimize import |
   | C-c C-r r   | local variable  |
   |             |                 |

## Treemacs
  
  | 按键        | 说明          | SPC |
  |-------------|---------------|-----|
  | ft / 0 / pt | Treemacs      | √  |
  | cf          | create file   |     |
  | cd          | create dir    |     |
  | yf          | copy file     |     |
  | ya          | absolute path |     |
  | yr          | relative path |     |
  | m           | move file     |     |
  | d           | del file      |     |
  | R           | rename        |     |
  | q           | hide          |     |
  | b           | add bookmark  |     |
  | P           | peek          |     |
  | \!          | node shell    |     |
  | M-!         | root shell    |     |
  |             |               |     |


## Window

  | 按键 | 说明         |
  |------|--------------|
  | ws   | 水平分割     |
  | wv   | 垂直分割     |
  | wc   | 关闭当前窗口 |

## Help
  
  | 按键      | 说明                |   |
  |-----------|---------------------|---|
  | C-h t     | tutorial            |   |
  | C-h k     | desc key            |   |
  | SPC h SPC | helm-spacemacs-help |   |

## Tutorial
  
  | 按键    | 说明          |        |
  |---------|---------------|--------|
  | C-h m   | cur mode doc  |        |
  | C-f     | page down     |        |
  | C-b     | page up       |        |
  | C-p     | pre line      | no vim |
  | C-n     | next line     | no vim |
  | C-u     | repeat        |        |
  | C-l     | cursor status |        |
  | C-k     | del           |        |
  | C-y     | paste         | no vim |
  | C-/     | undo          |        |
  | C-x C-f | find file     |        |
  | C-x C-s | save file     |        |
  | C-x b   | buffer list   |        |
  | C-x s   | save all      |        |
  | C-h     | help          |        |
  | C-x C-z | 挂起->shell   |        |
  | fg      | shell->emacs  |        |
  | C-x o   | other win     |        |
  | C-x 1   | max win       |        |
  | C-x 2   | 2 win         |        |

## Elisp
  
  | 按键    | 说明        | -        |
  |---------|-------------|----------|
  | C-x C-e | exec        |          |
  | C-h f   | func doc    |          |
  | C-c C-t | toggle todo | org-mode |
  | fwef    |             |          |

# 在线资源
  https://github.com/zilongshanren/spacemacs-private

  https://github.com/emacs-china/Spacemacs-rocks

  https://github.com/emacs-eaf/emacs-application-framework

  
