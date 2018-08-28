# 附录1

## 删除不在远程分支的分支

```bash
 git fetch -p && for branch in `git branch -vv | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
```

## 复制文件

功能比cp强大

```bash
rsync -av --progress sourcefolder /destinationfolder --exclude thefoldertoexclude
```

