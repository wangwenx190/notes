# Git

- 常用命令

   | 命令 | 作用 | 参数 | 示例 |
   | --- | ---- | ---- | ---- |
   | **clone** [options] <repo_url> [path_to_save] | 将远端代码仓库克隆到本地文件夹 | --progress：显示进度；-b <branch_name>：切换到<branch_name>分支；--depth <log_depth>：将提交记录删减到<log_depth>（使用“--depth 1”可以单纯下载代码，不下载提交记录）；--shallow-submodules：针对所有子模块启用“--depth 1”；--recursive：将所有子模块一起克隆下来（默认没有这个参数，即不会自动下载子模块，需要显式的指定） | git clone --progress -b dev --recursive <https://example.git> D:\code\my_proj |
   | **add** <files_to_add> | 将被修改的文件添加到当前提交中 | “.”代表添加所有改动的文件，不用“.”就要把所有被修改的文件路径都作为参数放到命令后面 | git add . |
   | **diff** <original_commit> <new_commit> | 显示两个提交之间的差异。如果修改已经提交，则需要将所比较的两个或两个以上的提交ID作为参数传给此命令。如果修改尚未提交，则可以不带参数执行此命令，显示待提交的修改与上次提交之间的差异。 | 使用“>”可以将差异输出到文件中，即创建补丁文件（注：此方法已过时，请使用`git format-patch`命令创建补丁） | git diff abcdef ghijk>mypatch.patch |
   | **commit** [options] | 将当前提交合入本地仓库或修改提交 | -a：自动添加所有被修改的文件（就不用专门add了）；--author=<new_author>：覆盖原始作者；--date=<new_date>：覆盖原始日期；-m <commit_message>：设置提交信息，不添加这个参数的话git会启动默认文本编辑器，让你输入提交信息；--amend：修改提交，不添加额外的参数意为修改上一次提交，如果要修改的不是上一次提交，请将需修改的提交的ID附在此参数后面；-s：添加Sign-off-by，推荐使用此参数 | git commit -a -m "What have I done?" |
   | **branch** [options] [branch_name] | 查看或修改分支 | -D <branch_name>：删除本地分支；-r：列出或删除（如果与“-d”一起使用）远端分支；-a：列出远端和本地的所有分支；-m：移动或重命名一个分支；-c：复制一个分支；-l：列出所有本地分支；-t/--track：设置远端的跟踪分支，等价于`--set-upstream-to=`，注意，`--track`后面跟空格，然后是远端分支名，例如`git branch --track origin/upstream`，而后者由于末尾有等号，所以不要有空格，直接跟分支名，例如`git branch --set-upstream-to=origin/upstream`；--no-track：取消远端跟踪分支的设置，等价于`--unset-upstream` | git branch -d -r origin/test |
   | **checkout** [options] <branch_name> | 切换或新建分支 | 不加任何参数意为切换到<branch_name>分支；-b：创建一个新分支；--track：设置远端跟踪分支 | git checkout -b patch-fix --track origin/master |
   | **merge** [options] <branch_names> | 合并其他分支到当前分支 | --no-ff：为合并分支这个操作创建一个单独的提交；-m <commit_message>：设置提交信息；--signoff：添加Sign-off-by，注意merge这个命令不能用-s做这个，因为-s有别的含义；--abort：中断当前合并操作并恢复到合并前的状态；--continue：（冲突解决后）继续被中断的合并操作 | git merge --no-ff -m "Merge some patches" my-fix-1 my-fix-2 |
   | **log** [options] | 查看当前分支提交记录 | --oneline：显示最简化的信息；--graph：显示视图；--pretty=short/medium：部分简化信息 | git log --pretty=short --graph |
   | **tag** [options] <tag_name> [commit_id] | 打标签或修改标签 | -s：创建GPG签名的标签；-f：强制覆盖同名的标签；-d <tag_name>：删除已经存在的标签；-v <tag_name>：验证给定标签的GPG签名；-l：列出所有标签；-m <tag_message>：设置标签信息 | git tag -m "New release" v1.2.3 |
   | **fetch** [repo_url] [branch_name] | 从远端仓库获取提交（不一定必须与本地是同一个仓库） | 什么参数也不加意为从当前远端更新本地仓库的当前分支（单纯的fetch，并没有真实的修改本地文件）；--all：获取所有远端的提交记录 | git fetch <https://ex.git> master |
   | **pull** [repo_url] [branch_name] | 获取并合并远端仓库的提交（相当于fetch+merge） | 什么参数也不加意为从当前远端更新本地仓库的当前分支（fetch+merge，本地文件被修改）；--all：对所有远端执行fetch | git pull <http://ex.git> dev |
   | **push** [options] [repo_url] [branch_name] | 推送本地提交到远端仓库 | --set-upstream：设置远端分支，在本地新建的分支第一次推送到远端时要加这个参数；--all：推送所有分支；-d <branch_name>：从远端仓库删除<branch_name>分支；--tags：推送所有标签到远端仓库；-f：强制覆盖远端仓库；什么参数也不加意为推送当前分支到远端仓库的相同分支 | git push origin master |
   | **remote** | 查看或修改远端仓库的设置 | add <remote_name> <repo_url>：添加远端仓库；rename <old_name> <new_name>：重命名远端仓库；remove <remote_name>：删除远端仓库；什么参数也不加意为列出所有远端仓库 | git remote add staging git://git.kernel.org/.../gregkh/staging.git |
   | **submodule** | 查看或修改子模块 | add <repo_url> [path_to_save]：添加子模块；init [path]：初始化子模块；update [--init] [--recursive] [path]：更新子模块 | - |
   | **apply** <patch_file> | 应用补丁。此命令只适用于使用`git diff`命令制作的老式补丁，使用`git format-patch`命令制作的新式补丁应使用`git am`命令。 | - | git apply mypatch.patch |
   | **cherry-pick** [options] <some_commits> | 将某一个或某几个**已经存在**的提交合并到当前分支。可以用*fetch*命令刷新提交历史，获取最新的提交记录 | --continue：（冲突解决后）继续被中断的操作；--abort：中断当前操作并恢复到之前的状态 | - |
   | **rebase** [options] | 变基 | `git rebase -i <区间开始commit> <区间结束commit>`，如果不指定结束commit，则为到最后一次commit。区间开始commit为要合并的第一个commit的前一个commit | - |
   | **revert** [options] <commid_id> | 回退某次提交 | --continue；--abort | - |
   | **status** | 查看当前工作树状态 | - | git status |
   | **clean** [options] | 从当前工作树移除未跟踪文件，即将所有未被提交到本地仓库的文件都删除，从仓库根目录开始，递归清理所有子文件夹 | -d：未被跟踪的文件夹也一起删除；-f：强制删除，建议添加此参数，因为Git会在很多情况下不执行此类型的命令；-x：不忽略`.gitignore`里的文件，将忽略文件也一起删除（建议添加此参数） | git clean -fdx |
   | **stash** [options] [stash_name] | 暂存当前的修改 | push：`git stash push`=`git stash`，不推荐使用`git stash save`；list：列出所有暂存的提交；show [stash_name]：显示某次暂存的差异；apply [stash_name]：在当前工作树应用某次暂存的提交，但不要将其从暂存列表中移除；pop [stash_name]：在当前工作树应用某次暂存的提交，并将其从暂存列表中移除；clear：从暂存列表中移除所有暂存的提交；drop [stash_name]：从暂存列表中移除某次暂存的提交 | - |

   注：大多数命令都支持`--progress`参数。
- 设置代理：

   ```bash
   git config --global http.proxy http://127.0.0.1:1080
   git config --global https.proxy http://127.0.0.1:1080
   #git config --global http.proxy 'socks5://127.0.0.1:1080'
   #git config --global https.proxy 'socks5://127.0.0.1:1080'
   ```

   取消代理：

   ```bash
   git config --global --unset http.proxy
   git config --global --unset https.proxy
   ```

   注意：不带`--global`的话可以只对当前git仓库进行设置。

- 设置全局用户名：`git config --global user.name "Your Name"`
- 设置全局电子邮件：`git config --global user.email "me@example.com"`
- 换行符设置：

   ```bash
   git config --global core.autocrlf true # Windows
   git config --global core.autocrlf input # Unix
   ```

- 设置默认文本编辑器：

   ```bash
   git config --global core.editor "'D:\Notepad++\notepad++.exe' -multiInst -notabbar -nosession -noPlugin '$*'"
   # 路径必须用双引号包裹起来，里面有特殊字符的话要用单引号再包裹一层
   # 如果编辑器位于环境变量中，也可以不写完整路径，例如：
   # git config --global core.editor notepad++
   ```

   取消默认文本编辑器的设置：`git config --unset --global core.editor`
- 推荐初始设置：

   ```bash
   # A common mistake is forgetting to add new files to a commit. Therefore it is recommended to set up git to always show them in git stat and git commit, even if this is somewhat slower (especially on Windows):
   git config --global status.showuntrackedfiles all
   # Pre-2.0 git has a somewhat stupid default that git push will push all branches to the upstream repository, which is almost never what you want. To fix this, use:
   git config --global push.default tracking
   # Sometimes it is necessary to resolve the same conflicts multiple times. Git has the ability to record and replay conflict resolutions automatically, but - surprise surprise - it is not enabled by default. To fix it, run:
   git config --global rerere.enabled true
   git config --global rerere.autoupdate true # this saves you the git add, but you should verify the result with git diff --staged
   # git pull will show a nice diffstat, so you get an overview of the changes from upstream. git pull --rebase does not do that by default. But you want it:
   git config --global rebase.stat true
   # To get nicely colored patches (from git diff, git log -p, git show, etc.), use this:
   git config --global color.ui auto
   git config --global core.pager "less -FRSX"
   # Workaround long file names can't be cloned, mainly for Windows
   git config --global core.longpaths true
   ```

- 如何拉取或者更新子模块：

```bash
git submodule update --init
```

  1. `--init`参数很重要，可以将状态是`dirty`的子模块（有未提交或暂存的改动）恢复原状。如果没有这个参数，那么`dirty`状态的子模块就无法成功更新。
  2. 这个命令只是把子模块的提交定位到父项目所设置的提交，并不是更新到子模块对应仓库的最新提交。如果你是仓库的维护者，想把子模块更新到其对应仓库的最新提交，需要在命令后面加上`--remote`参数。
  3. 如果需要递归拉取或者更新子模块，需要加上`--recursive`参数。没有这个参数的话默认都是非递归的，一定要注意。

- 如何修改子模块的设置：
  - 修改子模块远端仓库网址：`git config submodule.子模块名.url 新URL`
  - 修改子模块默认分支：`git config -f .gitmodules submodule.子模块名.branch 分支名`
- 移动子模块所在的位置：`git mv dir1/sub1 dir2/sub2`
- 删除子模块：
  1. 仓库根目录执行`git submodule deinit sub/module`
  2. 仓库根目录执行`git rm sub/module`
- 如何删除已经被跟踪的文件：
  1. 本地已经不再需要该文件：`git rm 路径`
  2. 本地仍然需要该文件，仅从版本控制系统中移除：`git rm --cached 路径`

  注：进行这种修改后记得commit后push到远端
- 如何拉取别人的pull request到本地仓库：
  1. 使用`git remote add <remote_name> <remote_url>`添加对方的远端仓库
  2. 使用`git fetch --all`获取新远端的提交记录
  3. 使用`git checkout -b <branch_name>`新建并切换分支（防止污染当前分支）
  4. 使用`git cherry-pick <commit_id>`拉取你想要的提交
- 如何制作和应用补丁文件

  ```bash
  git format-patch -N
  # N 为一个正整数，意为将最后的 N 次提交制作为一个补丁
  git format-patch -M origin/master
  # 将当前分支所有超前 origin/master 的提交制作为一个补丁
  git format-patch <commit_id>
  # 将某次提交以后的所有提交制作为一个补丁
  git format-patch <commit_id_1>..<commit_id_2>
  # 将某两次提交之间的提交制作为一个补丁
  git am patch_file_name.patch
  # 应用补丁文件。推荐添加 -s 参数（即添加 Sign-off-by）
  ```

  制作补丁文件时，可以添加`-o`参数，设置补丁文件的输出文件夹。
- 推送一个本地新创建的分支到远端

  ```bash
  git push --set-upstream origin new_branch_name_local:new_branch_name_remote
  ```

  备注：
    - `--set-upstream`参数可以不加，但这样会导致本地分支没有绑定对应的远端分支，后面`push`时就要每次都带上分支名字，很不方便，所以最好在第一次`push`这个新建的分支的时候就带上这个参数。不过万一真的忘记了，还可以补救，多执行一次`git branch --set-upstream-to=origin/new_branch_name_remote`就行了。这个设置远端跟踪分支的操作执行一次就行，不需要多次执行。
    - 由于git允许你创建的远端分支名字和本地的不同，所以语法是`本地分支名字:远端分支名字`，就算你打算本地和远端都用同一个名字，也要这样重复一遍，这是固定用法。
    - 这里所有提到的`origin`都是你远端的名字，git一般默认都是`origin`，但如果你设置了别的名字，要自己对应的替换。

- 本地通过命令删除远端的分支

  ```bash
  git push origin --delete branch_name_to_be_deleted
  ```

  和`git branch -dr origin/branch_name_to_be_deleted`有何区别？

  `git branch -dr`命令只是删除你本地存储的远端分支信息，并不会把你这个删除分支的操作推送到你真正的远端仓库，你下次执行`fetch`或者`pull`之后这个信息就又会被恢复。而使用`git push --delete`删除分支是会推送到远端的，会真正把你的分支删除（所以也没法反悔，注意谨慎操作），这个命令隐含了`git branch -dr`这个操作，不需要再额外手动执行。`--delete`可以简写为`-d`。

- 修改当前commit（即最新/最后一次commit）的parent commit
  - 情况一：将当前提交修改为某个分支的最新提交（相当于将当前commit置于某个分支提交历史的顶端）
    
    ```bash
    git rebase some_branch_name
    ```

  - 情况二：将父提交修改为某个特定的提交（相当于将当前commit置于某个分支提交历史的中间某个地方）

    ```bash
    git rebase --onto abc1234 HEAD^
    ```

- 本地分支和远端分支有冲突导致没法拉取代码

  ```bash
  git remote prune origin
  ```

  这个命令可以帮助你同步本地仓库与远程仓库的状态，移除那些不再存在于远程仓库中的分支记录。
