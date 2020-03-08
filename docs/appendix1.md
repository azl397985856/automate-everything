# 附录1

## 删除不在远程分支的分支

```bash
 git fetch -p && for branch in `git branch -vv | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
```

## 其他分支清理脚本

https://gist.github.com/StuPig/06736fbdeede11001aff

## 复制文件

功能比cp强大

```bash
rsync -av --progress sourcefolder /destinationfolder --exclude thefoldertoexclude
```

## discard all changes【Git】

```bash
git checkout . && git clean -xdf
```

