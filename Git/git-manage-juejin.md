[//]:#Git管理from掘金 

文章转载自[https://div.io/topic/1772](https://div.io/topic/1772)

### 分支示意图
```
origin/
  master                    // 线上版本
  |- hotfix-login           // 热修复，如登录异常
  release                   // 最新的要部署的版本
  develop                   // 开发分支
  |- feature-homepage-v2    // 正在开发的业务，如第二版的首页
  |- feature-timeline-api   // 正在开发的业务，如 Timeline 的 API

developer-ming
  master
  release
  develop
  |- feature-timeline-api   // 我正在开发这个 feature，不断和 origin 同步
```

### 新的业务
1. 任何的一个新的业务开发都要在本地从 ```develop fork``` 出来一个新的 ```branch feature-name```
2. 业务开发完成后，提交 Pull Request，```feature-name -> develop```，记得打 label 到 ```feature```
3. Code Review，如果有错误，在 ```feature-name``` 里修复
4. 相关负责人 Merge Pull Request，假删除这个分支

### 部署新的业务
1. ```develop``` 上不断 merge 新的 review 过的业务功能
2. 部署前，发 Pull Request 到 ```develop -> release```
3. 相关负责人 Code Review，合并代码
4. ```npm run build``` 打包业务代码，准备部署
5. 部署前的 commit，打 label 到 ```publish```
6. 发 PR 到 ```release -> master```，标注版本号
7. 部署，如果出错，回滚或者新建 ```hotfix``` 分支

### 小技巧
1. ```develop``` 和 ```release``` 的同步，用 ```git rebase```
2. ```develop``` 及 ```feature``` 分支不做 ```build``` 操作
3. 多人负责一个 feature 的时候，可以就一个功能再分拆到各个 branches

更详细分支管理可见阮一峰博客[https://www.ruanyifeng.com/blog/2012/07/git.html](https://www.ruanyifeng.com/blog/2012/07/git.html)