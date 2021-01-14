---
title: git 解决远程仓库文件大小写问题
date: 2020-12-28 15:06:36
categories: 'Diary'
tags: Git
---


## 记解决远程仓库文件大小

在React我们习惯把组件文件首字母定为大写，但是今天发现有同事弄了一个骚操作

{% asset_img caseSensitivity.png This is an image %}

大概是这个样子

- 新建一个 setting.ts 文件(大小写不敏感的状态下)，并提交
- 修改本地 setting.ts 变为 Setting.ts，文件内容无变更，无法提交
- 执行git config core.ignorecase false，修改 大小写敏感 规则，然后提交，查看结果，此时会存在 大小写 同时存在的文件
- 此时某种机缘下，再次执行 git config core.ignorecase true，大小写不敏感，
- 此时执行 git push ， 即把最新的更新都更新到了 a.ts 中
- 此时再修改 大小写敏感规则为敏感， 执行 git pull ，并不会拿到最新的更新。比如自己想要的是第一次修改后的 Setting.ts ，但是服务器有一个没有更新的 Setting.ts 和 有更新的 setting.ts,而你只能拿到前者，这可能就会出现一些问题

### 解决办法

执行git config --global core.ignorecase false，全局设置 大小写敏感 。

1. 文件变更比较少的情况

    直接使用以下命令重命名文件，在 git 中不要直接修改文件名，最好的办法是使用下面的方式，

    ``` Git

        git mv -f [你想要删掉的文件] [你想要留下的文件]
        git mv -f setting.ts Setting.ts

        等同于：

        git rm setting.ts
        git add Setting.ts
    ```

    这个命令的目的就是删除不需要的大小写同名文件，修改后 git push 提交变更即可。

    或

    ``` Git

        git rm --cached src/.../setting -r
    ```

2. 变更比较多时

   - 删除远程分支
   - 本地执行 git rm -r --cached .
   - 然后重新 git push

 mac windows 在不设置大小写敏感规则的时候默认大小写是不敏感，项目部署的机器是 Linux 的，而 Linux 是大小写敏感的。所以这样的问题平时不易发现，本地调试的时候大部分时候并不会出错误，只有在项目部署的时候问题才会显示出来
