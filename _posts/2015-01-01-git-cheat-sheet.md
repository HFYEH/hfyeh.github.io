---
layout: post
title: "Git cheat sheet"
tags: [git]
---

This is a git memo.

<!--more-->

## Configuration

{% highlight bash %}
git config --global user.name "Username"
git config --global user.email "email"
git config --global color.ui true
git config --global core.editor vim
git config --global merge.tool opendiff
git config --global core.autocrlf input # Commit 時把 CR/LF 全部換成 LF
git config --global core.autocrlf true # Check in 時會換成 Window 的 CR/LF，commit 時會自動換成LF
git config --global core.autocrlf false # window only project
{% endhighlight %}

## Alias

`--global` 是此 user，存在 ~/.gitconfig

`--local` 是此 repo，存在 repo 的 .git/config

`--system` 是所有 user

{% highlight bash %}
git config --list  # 檢視所有設定
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.lol "log --graph --decorate --pretty=oneline --abbrev-commit --all"
git config --global alias.mylog "log --pretty=format:'%h %s [%an]' --graph"
git config --global alias.pushall "push --recurse-submodules=on-demand"    # 在parent repo push 時可以 push 所有submodule
{% endhighlight %}

## 裸裝與平裝的分別

{% highlight bash %}
git init project # 單人使用(平裝)
git init --bare project.git # 多人使用(裸裝)
{% endhighlight %}

## Git Commands

### glt clone

{% highlight bash %}
git clone /var/git/project.git/                                   # 複製本機的 repository
git clone ssh://imroot@127.0.0.1:/var/git/project.git/            # 複製外部的 repository
git clone -o origin ssh://imroot@127.0.0.1:/var/git/project.git/  # 為遠端 repository 取名
{% endhighlight %} 

### git diff

{% highlight bash %}
git diff             # 比較 working directory 與 HEAD
git diff --cached    # 比較 staging 跟 HEAD
git diff HEAD^       # 比較 working directory 與 HEAD 前一個 commit

# 比較先前的版本間差異
git log --oneline    # 顯示 log 方便後續操作
git diff SHA1..SHA2
git diff --since=1.week.ago --until=1.minute.ago
{% endhighlight %} 

### git reset

{% highlight bash %}
git reset (HEAD)             # 與 git add 功能相反，取消 staging 的狀態回到 unstaging 或 untracked
git reset (HEAD) filename    # 指定特定檔案從 staging area 狀態回到 unstaging 或 untracked

# 如果想把 commit 撤銷，但不要回到 unstaging 或 untrack，而是回到 staging，可加 --soft
git reset --soft <commit>    # 把 HEAD 指向該 commit，相應的結果為此 commit 之後的修改都回到 staging

# 如果要讓工作目錄與 HEAD 完全相同，可加 --hard
git reset --soft HEAD^       # 把最後一次 commit 的檔案回復到 staging 並取消最後一次 commit
git reset --hard             # 把當前目錄和 staging 與最後一次 HEAD 同步，未追蹤的資料不會變化
git reset --hard <commit>    # 把 commit 之後的記錄完全撤銷並且將當前目錄與 commit 同步
git reset --hard HEAD^       # 取消最後一個 commit 並且將專案目錄回復到上一個 commit 的樣子
{% endhighlight %}

### git revert

{% highlight bash %}
git revert <commit>          # 對該 commit 所有更變逆操作，會產生新的 commit
{% endhighlight %} 

### git checkout

{% highlight bash %}
git checkout .               # Recover all to HEAD version
git checkout -- filename     # 回復為 HEAD 中的版本
git checkout <branch>        # 切換到 branch 分支
git checkout v0.0.1          # 切換到具有 'v0.0.1' 的特定 commit
git checkout -b <branch>     # 建立分支並切換到該分支(常用）

# 處理遠端 branch
git checkout -b branch-name origin/<branch>    # 在 origin/branch 的基礎上創建一個新分支並切換過去
git checkout --track origin/<branch>           # 功能同上，小孩子不要學，學了也會忘

# 設定追蹤遠端 branch
git branch --set-upstream master origin/next   # 把本地的 master 追蹤到 origin/next
{% endhighlight %} 

### git remote

{% highlight bash %}
git remote -                          # 列出所有遠端 repository 代號 (-v 可看對應的 URL）
git remote add <name> <address>       # 新增一個遠端 repository
git remote rm <name>                  # 刪除遠端 repository
git remote update origin --prune      # 更新本地的 remote branch

git remote show origin                # 做下列三件事

# 1. 顯示所有 origin 中的 remote branch 及追蹤狀態
# 2. 顯示所有 local 及它們與 remote branch 的關係(使用 git pull 會發生的事)
# 3. 顯示所有 local 及它們與 remote branch 的關係(使用 git push 會發生的事)
{% endhighlight %} 

### git fetch
{% highlight bash %}
git fetch origin                  # 抓下及更新 remote repository 所有分支
git fetch origin <branch>         # 只抓特定分支，取回的分支，在本地命名為 origin/<branch>
git fetch -prune                  # 更新 remote repository 的同時清理已經被刪除的 remote branch
{% endhighlight %} 

### git pull (no fast-forward)

- 以 merge 操作

{% highlight bash %}

git pull origin <remote_branch>:<branch>    # 完整格式，取回 origin 的 remote_branch 分支，與本地的 branch 合併
git pull origin <remote_branch>             # 如果要與當前的 branch 合併，可以省略冒號後的 <branch>
git pull origin                             # 如果當前分支有追蹤某個遠端分支，可以省略遠端分支名
git pull                                    # 如果當前分支只有唯一的追蹤分支，則連 origin 也可省略

# 會做兩件事
# 1. 同步遠端 repository 所有內容至本機的 origin，即 git fetch
# 2. 合併 origin/branch 至當前分支，即 git merge origin/master
{% endhighlight %} 

- 以 rebase 操作

{% highlight bash %}
git pull --rebase

# 會做兩件事
# 1. 同步遠端 Repo 至本機 git fetch
# 2. rebase origin/master (git rebase origin/master)
{% endhighlight %} 

### git push

{% highlight bash %}
git push origin <branch>:<remote_branch>    # 對照 git pull，冒號的意思就是從左邊送到右邊
git push origin :<remote_branch>            # 因為把準備推上去的 branch 設為空，表示刪除遠端分支（之後用 git fetch -p 刪除本地的 origin/<remote_branch>）
git push -u origin <branch>                 # 上傳當前分支到遠端 repository，並將 upstream branch origin/<branch>

git push --tags                             # 為遠端 repository 加上 tag（預設不會 push tag，要用此指令才會）
{% endhighlight %}

### git rebase

Rebase 真正含義

首先，當前的 branch 是從某個 commit 分出來的，那個 commit 就是此 branch 的 base。

Rebase 時，先把當前 branch 的最後一個 commit 到 base commit 間的所有 commits 移到暫存資料區，再把 HEAD 指向要被 rebase 的目標（也就是跑了所有的新的 base 的 commit），再將暫存區中的 commit 一一 commit 回來。

假定現在要從某 branch 做 rebase master
{% highlight bash %}
git checkout <branch>           # 切到該 branch
git rebase master               # 先在本 branch 上跑 master 的 commit，再跑 branch 上的 commit
git checkout master
git merge <branch>              # 用此指令會是 fast-forward，不會產生新的 commit
git merge <branch> --no-ff      # 不用 fast-forward 的情況下，會產生新的 merge commit，一個 merge 就代表一個 feature 的完成

# 進階用法
# https://git-scm.com/book/zh-tw/v1/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E8%A1%8D%E5%90%88
# server的base在master，client的base在server，要想直接rebase client到master
git rebase --onto master server client  # 如此會從 client 的 base 以後重演 commit 在 master 上
git rebase master server                # 在 client 和 master 後重演 commit
{% endhighlight %}

有了這層認識後，`git pull --rebase` 就不難理解了。

當使用 `git rebase --rebase` 時，會找到當前 branch 的 origin/branch，origin/branch 和 local branch 有各自的更新，但是當前 branch 的 base 就是在上一次 `git pull` 的地方。所以會把 pull 後的 local commit 先移到暫存區，再跑所有的 origin/branch 的 commit，最後再把暫存區的 commit 回來。

參考ihower的[說明](https://ihower.tw/blog/archives/3843)

### git rebase -i (Interactive rebase)

在同一個分支裡跑 rebase 是在更改 commit 順序

{% highlight bash %}
# rebase 到 HEAD^
git rebase -i HEAD^      # 因為 HEAD^ 之後只有一個 commit，故只會有一個 commit 出現，所以其實不會改變順序
git rebase -i HEAD~3     # 重跑最後三個 commit，會跑出 editor，編輯完後再執行該 editor 的內容
# 進 editor 後，可編輯 commit 順序，也可以刪除或合併和拆解，不過要注意，最上面的 commit 會最先被做出來
{% endhighlight %}

### git branch
{% highlight bash %}
git branch                  # 列出目前的 local branch
git branch -r               # 列出目前的 remote branch
git branch -a               # 列出目前的 local branch 和 remote branch
git branch <branch>         # 建立一個分支 branch
git branch -d <branch>      # 刪除 branch 分支
git branch -D <branch>      # 強制刪除 branch 分支（有 commit 但未 merge 時用）
git branch --merged         # 列出已經合併的 local branch
git branch -r --merged      # 列出已經合併的 remote branch
{% endhighlight %}

### git log

{% highlight bash %}
git log --pretty=oneline                # 一個 commit 只顯示一行
git log --oneline -p`                   # 將所有 log 和修改過的檔案內容列出
git log --oneline --stat --summary      #  查每個版本間的更動檔案和行數
git log --oneline --graph               # 圖形化
git log --until=1.minute.ago            # 只顯示一分鐘前的所有 commit
git log --since=1.day(hour/month).ago   # 只顯示一天（小時/月）以內的所有 commit
{% endhighlight %}

### git tag (用於釋出版本)

使用時機：每次推向production時使用（除非是用CI）

{% highlight bash %}
git tag                               # 檢視所有 tags
git tag -a v0.0.3 -m "version 0.0.3"  # 加上 tag
git tag -a v0.0.3 9fceb02 -m "msg"    # 為特定的 commit 增加 tag
git push --tags                       # 將本地的所有 tag 推到為遠端 repository
git checkout v0.0.1                   # 切換到具有 'v0.0.1' 的特定 commit
git show v0.0.3                       # 檢視 tag 的 commit 資料
git tag -d v0.0.3                     # 刪除本地 tag
git tag -d `git tag | grep -E '.'`    # 刪除本地全部的 tag
git tag -d $(git tag -l)              # 刪除本地全部的 tag
git push origin --delete $(git tag -l)# 將與本地同名的遠端 tag 刪除
git push origin :refs/tags/v0.0.3     # 刪除遠端 tag

# 刪除遠端全部 tag
git ls-remote --tags origin | awk '/^(.*)(s+)(.*[a-zA-Z0-9])$/ {print ":" $2}' | xargs git push origin
{% endhighlight %}

### git blame

{% highlight bash %}
git blame filename --date short  # 關於此檔案的所有 commit 紀錄（包含作者、日期、更動的行及其內容）
{% endhighlight %}

## 建立遠端 branch

滿足條件

1. 須要別人協作自己的 branch
2. 存在超過一天的分支，想儲存在遠端 repository

{% highlight bash %}
git checkout -b <branch>        # 建立並切換分支
git push origin <branch>        # 連結近端 branch 到遠端 branch 並追蹤
# 修改...
git commit -am "message"
git push                        # 會自動儲存到遠端 repository 的該 branch
# 其他人只要 git pull 就可以同步了
{% endhighlight %}

### git stash

正在分支編寫,臨時要去 master 救火

{% highlight bash %}
git stash save "message"            # 把目前還沒有 commit 的文件存到暫存區並且將工作目錄復原到最後一次 commit

# 救火完成後...
git checkout <branch>               # 切回剛剛寫到一半的分支
git stash apply                     # 回到 stash 前的未 commit 狀態

# 進階 stash
git stash save --keep-index         # 只把 staging 的檔案 stash
git stash save --include-untracked  # 上述指令只考慮 tracked file，如要把 untracked file 也 stash 要用這個

git stash -h                        # 可以查到所有功能

# 每次跑 stash 都會把 stash 存到 stash stack 中
git stash list                      # 顯示所有 stash，從最新到最舊
git stash show                      # 顯示最新的 stash 其中的變更內容
git stash apply stash@{1}           # 回復最新的 stash
git stash drop                      # apply 後 stash 依然存在，使用此指令刪除最上層的 stash
git stash clear                     # 清除所有 stash

# Shortcut (常用）
git stash                           # git stash save
git stash apply                     # git stash apply stash@{0}
git stash drop                      # git stash drop stash@{0}
git stash pop                       # git stash apply; git stash drop;
{% endhighlight %}

### git cherry-pick
{% highlight bash %}

git cherry-pick 54ea45             # 要把別的branch的特定 commit 複製一份到當前 branch，複製過來的 commit 有不同的 Hash ID
git cherry-pick --edit 54ea45      # 要寫入不同的 commit message
git cherry-pick --no-commit 53131s # 拉到 staging area 不要 commit
git cherry-pick -x 54ea45          # 把原 commit 的 Hash ID 加進 commit message 中
git cherry-pick --signoff 54ea45   # 把當前的 user 資訊加進 commit message
{% endhighlight %}

### git modules

A git repository inside another git repository.

{% highlight bash %}
# 將某個 repository 抓下來當成 submodule，資訊會寫在 `.gitmodules`
git submodule add git@example.com:sub_m.git
git commit -m "Add sub_m submodule"

# 要修改 submodule
cd sub_m                    # 先進入該 submodule
git checkout master         # Submodule 抓下來後並不會切到特定的 branch，要自行指定
# 修改...
git commit -am "made changes..."
git push                    # 將 submodule 推上 remote repository
# 因為 parent repository 會指向先前的 submodule 未更動前的commit，所以要回到 parent repository 再更新
cd ..
git add sub_m
git commit -m "Update something in sub_m"
git push

# Clone 專案中有用 submodule（剛抓下來 submodule folder 會是空的）
git submodule init          # 讀 .gitsubmodule 並寫入資訊到 .git/config
git submodule update        # clone submodules
# 之後如果submodule有變化，跑 git submodule update 也會更新
# 特別注意！每次跑 submodule update，都會把 submodule 變成 headless，所以要再 checkout 一次
{% endhighlight %}

### git filter-branch

計算 repository 大小

`git count-objects -vH`

這些情況下會有檔案要從所有歷史中完全移除。

1. 違反著作權
2. 大檔案移除

可以使用 BFG 或 filter-branch，[BFG](https://rtyley.github.io/bfg-repo-cleaner/)要安裝，[filter-branch](https://help.github.com/articles/removing-sensitive-data-from-a-repository/)較慢

## 大檔尋找

`git ls-tree -r -t -l --full-name HEAD | sort -n -k 4 | tail -n 10`

`git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch PATH-TO-YOUR-FILE-WITH-SENSITIVE-DATA' --prune-empty --tag-name-filter cat -- --all`

## exclude (專屬自己的資料夾，不給別人看的)
{% highlight bash %}
.git/info/exclude
experiments/    # exclude folder
tutorial.mp4    # file
*.mp4           # all file ended with .mp4
{% endhighlight %}

## .gitignore (所有 repository 中都有的，要故意忽略的)
{% highlight bash %}
*.log
{% endhighlight %}

## 情境題

### 若最後一次 commit 後，push 出去之前，有一些小錯誤要修正，想把修改之後的結果放入同一次 commit

{% highlight bash %}
git add file-changed
git commit --amend -m "message"
git commit -a -m "message"
{% endhighlight %}

### Merge 發生 conflict

{% highlight bash %}
git pull     # 在合併時會出錯
git status   # 該文件會顯示 both modified
# 修改該文件...
git commit -a
git push
{% endhighlight %}

### Rebase發生conflict

{% highlight bash %}
git fetch
git rebase   # 預設會去 rebase origin/branch_name

# 碰到衝突有三種處置方法
# 1. 解決衝突後 git add . 再 git rebase --continue
# 2. 跳過 master 上的這個 commit git rebase --skip
# 3. 回復到使用 rebase 前的狀態 git rebase --abort
{% endhighlight %}

### 要將本來在 repository 的檔案停止追蹤但不刪除

{% highlight bash %}
git rm --cached development.log    # 之後再加入.gitignore
{% endhighlight %}

## Github

### Fork project workflow
{% highlight bash %}
# How to update your fork
git remote add upstream <path_to_repo>   # Add remote for upstream (now we have origin, upstream)
git fetch upstream                       # Fetch changes (fetch from upstream)
git merge upstream/master master         # Merge them into master
git push origin master                   # Push to your remote
{% endhighlight %}

### Issue
{% highlight bash %}
git commit -m "This message will show up at issue #1"

# 下述三個，當 commit 被 merge 回 master 時，會關閉 issue
git commit -m "Fixes #1"
git commit -m "closes #1"
git commit -m "resolves #1"
{% endhighlight %}

## [commit messges 七條準則](https://chris.beams.io/posts/git-commit/)

1. 標題和主體以空行分隔
2. 標題控制在50個字母內，至多72個
3. 標題首字字首大寫
4. 標題句尾不加句號
5. 使用祈使句，要滿足這樣的句式：If applied, this commit will <your_commit_message>
6. 主體也以一行72個字母為限
7. 使用主體解釋 what, why vs. how，說明為何做，之前是如何，現在變得如何
