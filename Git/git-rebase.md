



git rebase master

将master新提交的改动，patch、合并到当前分支。

当前分支的提交会按顺序接在master 的提交后面，有冲突的话需要解决冲突 然后 git rebase --continue。

再切回master合并分支。



基于上述内容，可以使用如下流程来提交代码：

1. 在dev1分支上进行开发，然后commit提交，在dev1分支上生成一个提交单。
2. 切换到master分支，与remote/master分支同步。
3. 切换回dev1分支，将master分支rebase到dev1分支上，如果有冲突，修改冲突。**rebase操作的冲突修改与merge不一样，修改完冲突后，保存进index，然后直接`git rebase --continue`即可，不同再多做一次提交。**
4. 切换回master分支，合并dev1分支，此时合并会非常顺畅。然后push。



作者：邱simple
链接：https://www.jianshu.com/p/5c9c6383aa36
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

https://www.jianshu.com/p/5c9c6383aa36