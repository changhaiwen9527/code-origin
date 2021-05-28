# Git工作流（暂定）
## 前言
先来看看我们的开发中会遇到哪些case:

1. 新功能开发
2. bug修复
3. 功能测试
4. 欢迎补充

为了覆盖以上开发情形（其实也没几个），我计划采用两个长期分支: `master` 和 `testing`

其中`master` 作为主干，只有经过功能测试，稳定的代码在能够合并到`master`,`testing`则用作合并刚开发完的分支，在这个里进行测试

接下来我们来看下具体的操作

## 流程
### 新功能开发
开发新功能时，我们先从`master`分支创建新的`feature`分支，开发完毕后提交MR到`testing`分支进行测试，测试过程中有BUG继续使用`feature`分支提MR到`testing`修复并验证，验证完毕从`feature`提交最终MR到`master`并且打上tag上线，之后删除`feature`分支

#### 从 `master` 新建分支

新分支名类似: `feature/xxx`

1. 切换到 `master`: `git checkout master`
2. 拉取最新代码: `git pull origin master` 或 `git pull`
3. 创建新分支并切换: `git checkout -b feature/xxx master`

注意：创建新分支前先用 `git branch -l` 检查分支是否存在，如存在请执行 `git branch -d 分支名` 删除分支。

#### 提交至`testing`

1. 切换到你的本地分支: `git checkout feature/xxx`
2. 添加修改文件至暂存区: `git add XXX`
3. 提交暂存区内容到本地仓库: `git commit -m "maint:这里写你此次提交的描述"`
2. push 你的分支: `git push origin feature/xxx`
3. 提交 Merge Request 到 `testing`

#### 上线后

请删除本地分支：

1. `git checkout master`
2. `git pull origin master`
3. `git branch -d feature/xxx`

## Git 冲突解决方案

### 合并到 `testing` 冲突

**不要在浏览器里解决冲突！！！**

正确的方法：

1. 确保你的功能分支的代码都已经 commit，保持分支是 clean 状态。
2. 切换到本地 `testing` 分支，并拉取最新代码，执行 `git checkout testing` 然后执行 `git pull origin testing`。
3. 执行本地分支合并到 `testing` 的操作 `git merge feature/xxx`
4. 解决冲突并 commit 代码。
5. 由于 `testing` 不能直接 push，所以我们用一个新名字直接 push `testing` 分支，执行 `git push origin testing:feature/xxx-resolved`，作为示例给新分支起名 `feature/xxx-resolved`。
6. 提交 `feature/xxx-resolved` 到 `testing` 的合并请求，合入并删除 `feature/xxx-resolved`，执行完这步以后之前冲突的合并请求也已经合入。
7. 在 `testing` 分支下执行 `git pull origin testing` 拉取最新代码。
8. 切换到你的功能分支 `git checkout feature/xxx`
9. 执行 `git checkout testing [之前冲突的文件(相对路径)]`
10. commit 这些文件。
11. push 你的分支 `git push origin feature/xxx`
12. 到这里实际上冲突已解决，如此分支还需要继续开发，可继续下面的操作：
13. 再次提交 `feature/xxx` 到 `testing` 的合并请求，并合入。

### 合并到 `master` 冲突

可以在浏览器里解决冲突。

如浏览器里无法解决，可按以下步骤操作：

1. 确保你的功能分支的代码都已经 commit，保持分支是 clean 状态。
2. 合并 `master` 到你的功能分支，执行 `git pull origin master`。（注意，因为我们的功能分支是从 `master` 创建的，所以 `master` 合并到功能分支是安全的）
3. 解决冲突并 commit 代码。
4. 重新 push 功能分支。
