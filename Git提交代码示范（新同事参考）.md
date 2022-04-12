## Git 提交代码示范（新同事参考）

- git status

  > 查看修改哪些文件，是不是自己的模块

- git diff

  > 比较每个文件修改前后的代码变化，查看是否有误

- git add .

  > 把工作时的所有变化提交到暂存区

- git commit

  > 将暂存区内容添加到本地仓库中，标记好提价内容
  >
  > - 英文字符下输入 **i** 使命令行进入输入状态
  > - 输入 commit 内容
  > - 退出按**Esc 键**，然后英文状态下输入 **:wq** 进行保存

  > **commit 内如开头：**
  >
  > - feature：新增内容用 feature 开头
  >
  > - bug：修改 bug 用 bug 开头

- git log

  > 查看自己的提交日志

- git pull --rebase origin develop

  > 将远端代码拉下来，并且将自己最新一次的 commit 放到最顶上作为最后一次提交

  > 有**冲突**的情况（没有冲突此步骤跳过）：
  >
  > - 使用编辑器修改冲突 ，修改完成后使用如下命令
  >
  > - git add .
  > - git rebase --continue

- git push -f origin develop

  > 将次提交推送到远端，提交完成


```js
git config --global user.name 'name'
git config --global user.email 'email'

git config --local // local只对制定仓库有效
git config --global // global对当前用户所有仓库有效
git config --system // 对系统所有登录的用户有效

git config --list --global // 显示config配置

git init project_name

git log -n3 --oneline --graph // graph分支线
git branch
git checkout -b temp xxxxx // 基于以前的版本创建分支

git cat-file -p xxxxxx

git diff xxx xxx // 两个commit进行比较

git diff HEAD HEAD^/HEAD~1/HEAD^^

git branch -D xxxx

// 修改最近一次commit
git commit --amend

// 修改历史commit
git rebase -i 
pick:use commit
reword:use commit but edit the commit message
squash:use commit but meld into previous commit合并到前边的commit里

git diff --cached // 暂存区diff比较
git diff -- index.html // 制定文件diff比较

git reset HEAD // 取消暂存区内容

// git变更工作区的文件 保存暂存区修改
git checkout -- index.html

git reset --hard xxxx

// 比较两个分支文件的差异
git diff dev master -- index.html

// 删除文件
git rm index.html


git stash
git stash list
git stash apply
git stash pop // list列表里的信息删除

// 有进度条的克隆 file://智能协议
git clone --bare file:///Users/.... temp

git remote -v
git remote add upstream ...
git push --set-upstream origin branch1 // 新建分支推送到远端

git branch // 查看本地所有分支
git branch -a // 查看所有分支

git push origin --delete branch // 删除远端分支

git branch -d branch1   // 删除 本地 
git branch -r -d origin/branch1   // 删除远程端分支branch1  分支
git push origin ：branch1 // 把本地的操作推送到远程来执行

3. git查看分支：
查看本地分支 git branch
查看远程分支 git branch -r
查看本地和远程分支 git branch -a

4.git删除分支：
删除本地分支 git branch -d 本地分支名
删除远程分支 git push origin --delete 远程分支名
推送空分支到远程（删除远程分支另一种实现）git push origin :远程分支

ssh-keygen -t rsa -b 4096 -C 'my-emails'

~/.ssh // 查看有没有ssh  id_rsa私钥 id_rsa.pub公钥
$ cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCqRgQxb7c9IGUWGSTsOIWrRn2Q+STlwA8b8HyxGXZ5dX6Mz4TxbQcTTM+ZdXRxX6uMAGYj+vNs+8HxZEJP5o/gFBgu9gSb1dKhLHnGa2c8wCzSXXhiq9JuEcQEtbvzbK38hSV9ukCiE/r8LleSy5RS4b6jFZ08gfxcu3e2JnT9nxNDpygIM1JmDZOaAdjSsmo7k8Iw2cDDDdZZKUroifvGVyyLxo/34X/4jgoS1bqfiAGl31LqPCXxAn4/0YM0BVgEcCQGKiD7VVXgehwlpU44VlVSkoQIKFnP5SeEBEhCdgMwTWllpafOLUwABAZwPyzObNrxRpWnNO4U7N5o7u/iwUBWdLRceWUhfC6V78khtJHXtQRQgFnxv/zEptuyeWPDdRT+MYrJfXeR66yZU4ydeKI4zwK83A6GhUxV7FtZGWrT/5KRtOgvFySkB1hp26/uaKaWsuFnid8AqRtMwltmvXE4B5IERqgpnM8XaT5uYSk+wRG7EWHzsVzspJ9u5x8= zhanqingbin@360.cn

git config --add --local user.name 'name'

git rm index2.html // git删除文件
```
工作目录==》暂存区==》历史版本
commit=>tree(对应的文件快照)=》blob

暂存区修改回工作区  git reset HEAD -- filename
工作区修改回暂存区区  git checkout -- filename