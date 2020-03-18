[//]: # "git diff old mode 解决办法"

把项目部署到服务器后没有做改动，用 `git pull` 拉取变更的代码提示本地文件已修改。随便 `git diff` 则显示

```bash
git diff xxx
old mode 100644
new mode 100755
```
明显可以看到是文件权限改变了引起的，解决办法也很简单，只需要设置 git 忽略本地文件的权限价差

- 方法1，当前项目设置

```bash
git config core.filemode false
```

- 方法2，git 全局设置

```bash
git config --global core.filemode false
```