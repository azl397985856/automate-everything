
# 自动化小脚本

## 自动删除不在远程分支的分支

```bash
 git fetch -p && for branch in `git branch -vv | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
```
