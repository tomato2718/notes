# [Git] 修改已提交的 commit
## 概要
在提交 commit 時，可能有提交者資訊打錯或是訊息錯誤的情況，這篇文章會說明在此情況如何透過 git 指令修改已經送出的 commit。

> **_注意：_** 非必要情況不要修改已經提交的 commit。

## 說明

- 使用 `git rebase -i` 開始修改。

```sh
# 取得最後提交的 5 筆 commit
>>> git rebase -i HEAD~5
```

- 會列出類似畫面，下方註解區塊是操作說明。

```plaintext
pick 1051ddc Feat: Add `types.py` module.
pick 7159778 Fix: Fix incorrect `__all__` member
pick 8617a27 Tests: Remove unnecessary test
pick ed84267 Docs: Update documents
pick c1bbc5c Chore: Update project to v2.0.0

# Rebase e811dd0..c1bbc5c onto e811dd0 (5 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
#         create a merge commit using the original merge commit's
#         message (or the oneline, if no original merge commit was
#         specified); use -c <commit> to reword the commit message
# u, update-ref <ref> = track a placeholder for the <ref> to be updated
#                       to this position in the new commits. The <ref> is
#                       updated at the end of the rebase
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
```

- 將要修改的 commit 改為 `e` 或 `edit`，並儲存退出。
    - 在指令列操作的話，通常是進入 `vi` 或 `vim` 編輯。

```plaintext
edit 1051ddc Feat: Add `types.py` module.
pick 7159778 Fix: Fix incorrect `__all__` member
pick 8617a27 Tests: Remove unnecessary test
edit ed84267 Docs: Update documents
pick c1bbc5c Chore: Update project to v2.0.0
```

- 保存並退出後，會出現修改提示。

```sh
>>> git rebase -i HEAD~5
Stopped at 1051ddc...  Feat: Add `types.py` module.
You can amend the commit now, with

  git commit --amend 

Once you are satisfied with your changes, run

  git rebase --continue
```

- 重新設定 commit 內容。
    - `--reset-author`: 重設作者為現在設定資訊。
    - `--no-edit`: 不進行訊息修改。

```sh
# 修改 commit 訊息
>>> git cimmit --amend
# 作者資訊設定錯誤時，重設作者為當前設定資訊
>>> git commit --amend --reset-author --no-edit
```

- 完成後，修改下一則 commit。

```sh
>>> git rebase --continue
```

- 再次回到前一個步驟。

```sh
# 修改 commit 訊息
>>> git cimmit --amend
# 作者資訊設定錯誤時，重設作者為當前設定資訊
>>> git commit --amend --reset-author --no-edit
```

- 重複直到訊息都修改完畢。

```sh
>>> git rebase --continue
Successfully rebased and updated refs/heads/dev.
```

- 重新提交修改後的內容。

```sh
>>> git push
```

- 完成！