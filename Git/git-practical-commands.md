### git 命令

- 更新 ```git pull```

- 添加 ```git add xxx.xx```

- 丢弃工作区的修改 ```git checkout -- file 让这个文件回到最近一次`git commit`或`git add`时的状态```

- 从版本库中删除该文件 ```git rm file```

- 提交 ```git commit -m "git tracks changes"```

- 把本地库的所有内容推送到远程库 ```git push origin master 本地`master`分支的最新修改推送远程```

  

- 创建`dev`分支  ```git branch dev```

- 切换到`dev`分支 ```git checkout dev```

  *上2可用```git checkout -b dev```

- 查看当前分支 ```git branch```

- 合并指定分支到当前分支  ```git checkout master && git merge --no-ff dev```

- 删除`dev`分支 ```git branch -d dev```  

  

- 创建远程`origin`的`dev`分支到本地 ```git checkout -b dev origin/dev```

- 指定本地`dev`分支与远程`origin/dev`分支的链接 ```git branch --set-upstream-to=origin/dev dev```

- 把`dev`分支`push`到远程 ```commit && git push origin dev```

### 协作

多人协作的工作模式通常是这样：

1. 首先，可以试图用```git push origin ```推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用```git pull```试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用```git push origin ```推送就能成功！

如果```git pull```提示```no tracking information```，则说明本地分支和远程分支的链接关系没有创建，用命令```git branch --set-upstream-to  origin/```

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

小结

- 查看远程库信息，使用```git remote -v```；
- 本地新建的分支如果不推送到远程，对其他人就是不可见的；
- 从本地推送分支，使用```git push origin branch-name```，如果推送失败，先用```git pull```抓取远程的新提交；
- 在本地创建和远程分支对应的分支，使用```git checkout -b branch-name origin/branch-name```，本地和远程分支的名称最好一致；
- 建立本地分支和远程分支的关联，使用```git branch --set-upstream branch-name origin/branch-name```；
- 从远程抓取分支，使用```git pull```，如果有冲突，要先处理冲突。

### 分支管理命令

开发分支Develop

主分支只用来分布重大版本，日常开发应该在另一条分支上完成。我们把开发用的分支，叫做Develop。

![img](https://www.ruanyifeng.com/blogimg/asset/201207/bg2012070504.png)

这个分支可以用来生成代码的最新隔夜版本（nightly）。如果想正式对外发布，就在Master分支上，对Develop分支进行"合并"（merge）。

- Git创建Develop分支的命令：
```
> 　　git checkout -b develop master
```
- 将Develop分支发布到Master分支的命令：
```
> 　　# 切换到Master分支
> 　　git checkout master
>
> 　　# 对Develop分支进行合并
> 　　git merge --no-ff develop
```
这里稍微解释一下，上一条命令的--no-ff参数是什么意思。默认情况下，Git执行"快进式合并"（fast-farward merge），会直接将Master分支指向Develop分支。

![img](https://www.ruanyifeng.com/blogimg/asset/201207/bg2012070505.png)

使用--no-ff参数后，会执行正常合并，在Master分支上生成一个新节点。为了保证版本演进的清晰，我们希望采用这种做法。关于合并的更多解释，请参考Benjamin Sandofsky的[《Understanding the Git Workflow》](http://sandofsky.com/blog/git-workflow.html)。

![img](https://www.ruanyifeng.com/blogimg/asset/201207/bg2012070506.png)

#### 协作流程

- step 1: 切换到`develop`分支 ```git checkout develop```

- step 2: 分出一个功能性分支 ```git checkout -b feature-discuss```

- step 3: 在功能性分支上进行开发工作，多次commit，测试以后...

- step 4: 把做好的功能合并到`develop`中

  ```
   >>> git checkout develop
  
  	# 回到develop分支
  
      >>> git merge --no-ff feature-discuss
      # 把做好的功能合并到develop中
  
      >>> git branch -d feature-discuss
      # 删除功能性分支
  
      >>> git push origin develop
      # 把develop提交到自己的远程仓库中
  ```



### Git 如何优雅的回退代码?

[Git 如何优雅的回退代码](https://juejin.im/post/5e377e705188254ca22bc128)

