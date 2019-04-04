# Git
- 常用命令

   | 命令 | 作用 | 参数 | 示例 |
   | --- | ---- | ---- | ---- |
   | **clone** [options] <repo_url> [path_to_save] | 将远端代码仓库克隆到本地文件夹 | --progress：显示进度；-b <branch_name>：切换到<branch_name>分支；--depth <log_depth>：将提交记录删减到<log_depth>（使用“--depth 1”可以单纯下载代码，不下载提交记录）；--shallow-submodules：针对所有子模块启用“--depth 1”；--recursive：将所有子模块一起克隆下来（默认没有这个参数，即不会自动下载子模块，需要显式的指定） | git clone --progress -b dev --recursive https://example.git D:\code\my_proj |
   | **add** <files_to_add> | 将被修改的文件添加到当前提交中 | “.”代表添加所有改动的文件，不用“.”就要把所有被修改的文件路径都作为参数放到命令后面 | git add . |
   | **diff** <original_commit> <new_commit> | 显示两个提交之间的差异 | 使用“>”可以将差异输出到文件中，即创建补丁文件；1 | git diff abcdef ghijk>mypatch.patch |
   | **commit** [options] | 将当前提交合入本地仓库或修改提交 | -a：自动添加所有被修改的文件（就不用专门add了）；--author=<new_author>：覆盖原始作者；--date=<new_date>：覆盖原始日期；-m <commit_message>：设置提交信息，不添加这个参数的话git会启动默认文本编辑器，让你输入提交信息；--amend：修改提交 | git commit -a -m "What have I done?" |
   | **branch** [options] [branch_name] | 查看或修改分支 | -D <branch_name>：删除本地分支；-r：列出或删除（如果与“-d”一起使用）远端分支；-a：列出远端和本地的所有分支；-m：移动或重命名一个分支；-c：复制一个分支；-l：列出所有本地分支 | git branch -d -r origin/test |
   | **checkout** [options] <branch_name> | 切换或新建分支 | 不加任何参数意为切换到<branch_name>分支；使用“-b <branch_name> --track <remote_name>/<remote_branch_name>”可以创建并切换到一个新的分支 | git checkout -b patch-fix --track origin/master |
   | **merge** [options] <branch_names> | 合并其他分支到当前分支 | --no-ff：为合并分支这个操作创建一个单独的提交；-m <commit_message>：设置提交信息；--abort：中断当前合并操作并恢复到合并前的状态；--continue：（冲突解决后）继续被中断的合并操作 | git merge --no-ff -m "Merge some patches" my-fix-1 my-fix-2 |
   | **log** [options] | 查看当前分支提交记录 | --oneline：显示最简化的信息；--graph：显示视图；--pretty=short/medium：部分简化信息 | git log --pretty=short --graph |
   | **tag** [options] <tag_name> [commit_id] | 打标签或修改标签 | -s：创建GPG签名的标签；-f：强制覆盖同名的标签；-d <tag_name>：删除已经存在的标签；-v <tag_name>：验证给定标签的GPG签名；-l：列出所有标签；-m <tag_message>：设置标签信息 | git tag -m "New release" v1.2.3 |
   | **fetch** [repo_url] [branch_name] | 从远端仓库获取提交（不一定必须与本地是同一个仓库） | 什么参数也不加意为从当前远端更新本地仓库的当前分支（单纯的fetch，并没有真实的修改本地文件） | git fetch https://ex.git master |
   | **pull** [repo_url] [branch_name] | 获取并合并远端仓库的提交（相当于fetch+merge） | 什么参数也不加意为从当前远端更新本地仓库的当前分支（fetch+merge，本地文件被修改） | git pull http://ex.git dev |
   | **push** [options] [repo_url] [branch_name] | 推送本地提交到远端仓库 | --all：推送所有分支；-d <branch_name>：从远端仓库删除<branch_name>分支；--tags：推送所有标签到远端仓库；-f：强制覆盖远端仓库；什么参数也不加意为推送当前分支到远端仓库的相同分支 | git push origin master |
   | **remote** | 查看或修改远端仓库的设置 | add <remote_name> <repo_url>：添加远端仓库；rename <old_name> <new_name>：重命名远端仓库；remove <remote_name>：删除远端仓库；什么参数也不加意为列出所有远端仓库 | git remote add staging git://git.kernel.org/.../gregkh/staging.git |
   | **submodule** | 查看或修改子模块 | add <repo_url> [path_to_save]：添加子模块；init [path]：初始化子模块；update [--init] [--recursive] [path]：更新子模块 | - |
   | **apply** <patch_file> | 应用补丁 | - | git apply mypatch.patch |
   | **cherry-pick** [options] <some_commits> | 将某一个或某几个已经存在的提交合并到当前分支 | --continue：（冲突解决后）继续被中断的操作；--abort：中断当前操作并恢复到之前的状态 | - |
   | **rebase** [options] | 变基 | - | - |
   | **revert** [options] <commid_id> | 回退某次提交 | --continue；--abort | - |

   注：大多数命令都支持`--progress`参数。
- 设置代理：
   ```bash
   git config --global http.proxy http://127.0.0.1:1080
   git config --global https.proxy https://127.0.0.1:1080
   #git config --global http.proxy 'socks5://127.0.0.1:1080'
   #git config --global https.proxy 'socks5://127.0.0.1:1080'
   ```
   取消代理：
   ```bash
   git config --global --unset http.proxy
   git config --global --unset https.proxy
   ```
- 设置全局用户名：`git config --global user.name "Your Name"`
- 设置全局电子邮件：`git config --global user.email "me@example.com"`
- 换行符设置：
   ```bash
   git config --global core.autocrlf true # Windows
   git config --global core.autocrlf input # Unix
   ```