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
