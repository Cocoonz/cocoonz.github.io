---
layout: post
title: Git学习笔记
category: 学习
tags: Git
description: 初学git的笔记
---

### Git-Cheat-Sheet

![](https://www.git-tower.com/blog/content/posts/54-git-cheat-sheet/git-cheat-sheet-large01.png)

### 一些基础指令

- $ mkdir xixixi  
  建立名为xixixi的文件夹

- $ cd xixixi 
  进入xixixi的文件夹

- $ pwd 
  查看当前文件夹的目录

- $ git init
  Git就把仓库建好了，告诉你是一个空的仓库（empty Git repository），可以发现当前目录下多了一个.git的目录，这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。这个目录是隐藏的，用ls-ah能看见

- $ git add lalala.txt
  这一步是把文件添加到仓库里面。

- $ git status
  查看当前仓库的状态，比如你已经修改过后，还没提交的话，它是会显示的。

- $ git diff lalala.txt
  顾名思义是different,这个指令可以查看修改后的文件和当前文件的区别

- $ git commit -m "你提交的文件的说明"
  这一步是把文件提交到本地仓库里面。提交完后就是一个版本了。 

- $ git log
  $ git log --pretty=oneline
  可以用这个来查看你提交的版本信息，并且可以看到提交的时间，和提交的版本号。第二个功能是一样的，只是减少一些显示的信息，只显示一行

- $ git reset --hard HEAD^
  HEAD是当前的版本，HEAD^是回退到上一个版本，HEAD^^是回退到上上一个版本，HEAD~n是回退到前n个版本

- $ cat lalala.txt
  查看文件lalala.txt内容

- $ git reset --hard commit ID
  commit id是你提交的版本的ID，可以通过这个来回到未来的版本，那么如何找到这个ID呢？如果你没关掉当前的窗口的话，上面如果有git log的话，是可以找到你以前的版本号的。如果你关掉了，那也没事。

- $ git reflog

  这个记录了你的历史的指令，显然可以通过这个找到那个未来的版本号。然后用11的方法就可以回到未来的版本了。然后可以根据10继续回退= =此时的git reflog显示的指令就又多了一个。

  ​

### 遇到的问题及解决办法

- 无法push

  错误信息如下：![](C:\Users\aqyjz\Desktop\微信图片_20190517232946.png)

  解决方法1：强制push

  ` git push -u origin master -f `

  这样会使远程修改丢失，一般是不可取的

  解决方法2：push前先将远程repository修改pull下来

  ` gitgit pull origin master` 

  `git push -u origin master`

  （尝试过此方法但没有解决问题）
  解决方法3：若不想merge远程和本地修改，可以先创建新的分支：

  ` git branch [name]` 

  `git push -u origin [name]`

  多人协作的开发过程，一般推荐熟练使用分支功能来进行代码的托管，这样就会避免冲突的发生。

  [原文链接](https://blog.csdn.net/Rebel_Yangke/article/details/52611128)

  ​

  ​
