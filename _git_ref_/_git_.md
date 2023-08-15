# 								Git

## 1.git常用命令



```
git config -globle user.name "USERNAME"
git config -globle user.email "EMAIL"

git init																初始化本地库
git status															查看本地库
git add																	添加到暂存区
git commit -m "MESSAGES" "FILE NAME"		提交到本地库
git reflog															查看历史记录
git reset --hard "VERSION"							版本穿梭
```



## 2.查看本地库状态



Assuming that you have added a new file in one directory.

git status

```
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	hello.txt

nothing added to commit but untracked files present (use "git add" to track)
```



## 3.添加暂存区



git add hello.txt 

git status

```
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   hello.txt
```



### 从暂存区删除文件

git rm --cached hello.txt 

```
rm 'hello.txt'
```

git status

```
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	hello.txt

nothing added to commit but untracked files present (use "git add" to track)

```





## 4.提交本地库



git commit -m "first commit" hello.txt

```
[master (root-commit) 63bf72e] first commit
 1 file changed, 5 insertions(+)
 create mode 100644 hello.txt

```



git status

```
On branch master
nothing to commit, working tree clean
```



### 查看版本信息

git reflog

```
63bf72e (HEAD -> master) HEAD@{0}: commit (initial): first commit
```



git log

```
commit 63bf72eb46b52f14c84d8d9566baae9e2defa9ea (HEAD -> master)
Author: Jerome <jrm.ys@foxmail.com>
Date:   Tue Aug 1 08:09:54 2023 +0800

    first commit
```



## 5.修改文件



 Assuming that you have modified a file.



git status

```
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")

```



git add hello.txt

git status

```
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   hello.txt

```



git commit -m "second commit" hello.txt

```
[master 00b15b1] second commit
 1 file changed, 5 insertions(+)

```



(if deletion occured,which means one line has been modified,cuz git will change a line after deleting the former one.)





 git reflog

```
00b15b1 (HEAD -> master) HEAD@{0}: commit: second commit
89f48cd HEAD@{1}: commit (initial): first commit

```





"FILE MODIFIED"

git status

```
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")
```



git add hello.txt

git status

```
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   hello.txt

```



git commit -m "third commit" hello.txt

```
[master 26a69af] third commit
 1 file changed, 2 insertions(+), 2 deletions(-)

```





## 6.版本穿梭



git reset --hard 00b15b1

```
HEAD is now at 00b15b1 second commit

```



git reflog

```
00b15b1 (HEAD -> master) HEAD@{0}: reset: moving to 00b15b1
26a69af HEAD@{1}: commit: third commit
00b15b1 (HEAD -> master) HEAD@{2}: commit: second commit
89f48cd HEAD@{3}: commit (initial): first commit
```



cat hello.txt

FILE THAT CHAGED TO THE SECOND MODIFIED PERIOD





## 7.分支操作





### 1.CMD



```
git branch "BRANCH NAME"		创建分支

git branch -v					查看分支

git checkout "BRANCH NAME"		切换分支

git merge "BRANCH NAME"			把指定的分支合并到当前分支上
```





### 2.查看



git branch -v

```
* master 26a69af third commit

```



### 3.创建



git branch hot-fix

git branch -v

```
$ git branch -v
  hot-fix 26a69af third commit
* master  26a69af third commit

```





### 4.修改/切换



git checkout hot-fix

```
Switched to branch 'hot-fix'

```

git branch -v

```
* hot-fix 26a69af third commit
  master  26a69af third commit

```

​	

vim hello.txt

(MODIFY THE FILE)



git status

```
On branch hot-fix
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")

```

git add hello.txt

git commit -m "hot-fix first commit" hello.txt

```
[hot-fix 59b377c] hot-fix first commit
 1 file changed, 1 insertion(+), 1 deletion(-)
```







THEN

.git/HEAD ---> ref: refs/heads/hot-fix

.git/refs/heads ---> {hotfix,master}

.git/refs/heads/hotfix ---> 59b377ca7421cb8c1ddb9761aeea8bbd913a5ad3





### 5.合并



#### 1正常合并

git branch -v

```
* hot-fix 59b377c hot-fix first commit
  master  26a69af third commit

```



git checkout master

```
Switched to branch 'master'

```



cat hello.txt

(FILE IN MASTER)



git merge hot-fix

```
Updating 26a69af..59b377c
Fast-forward
 hello.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

```



#### 2.冲突合并





vim hello.txt

(MODIFY MASTER)



git add hello.txt

git commit -m "master test" hello.txt

```
[master 6d505b5] master test
 1 file changed, 1 insertion(+), 1 deletion(-)
```



cat hello.txt

```
hello git! 1
hello git! 2 modified 1 hot-fix modified 1
hello git! 3 modified 2
hello git! 4
hello git! 5
modified 1
modified 2
modified 3
modified 4 master modified 1
modified 5

```



git checkout hot-fix

```
Switched to branch 'hot-fix'
```



cat hello.txt

```
hello git! 1
hello git! 2 modified 1 hot-fix modified 1
hello git! 3 modified 2
hello git! 4
hello git! 5
modified 1
modified 2
modified 3
modified 4
modified 5

```



vim hello.txt

(MODIFY HOT-FIX)



git add hello.txt

git commit -m "hot-fix test" hello.txt

```
[hot-fix c6e31e5] hot-fix test
 1 file changed, 1 insertion(+), 1 deletion(-)

```



cat hello.txt

```
hello git! 1
hello git! 2 modified 1 hot-fix modified 1
hello git! 3 modified 2
hello git! 4
hello git! 5
modified 1
modified 2
modified 3
modified 4
modified 5 hot-fix modified 1

```





git checkout master

```
Switched to branch 'master'
```



git merge hot-fix

```
Auto-merging hello.txt
CONFLICT (content): Merge conflict in hello.txt
Automatic merge failed; fix conflicts and then commit the result.

```

git status

```
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")

```

(HERE'S MERGING STATUS)



vim hello.txt

```
hello git! 1
hello git! 2 modified 1 hot-fix modified 1
hello git! 3 modified 2
hello git! 4
hello git! 5
modified 1
modified 2
modified 3
<<<<<<< HEAD
modified 4 master modified 1
modified 5
=======
modified 4
modified 5 hot-fix modified 1
>>>>>>> hot-fix

```



(EDIT)

```
hello git! 1
hello git! 2 modified 1 hot-fix modified 1
hello git! 3 modified 2
hello git! 4
hello git! 5
modified 1
modified 2
modified 3
modified 4 master modified 1
modified 5 hot-fix modified 1
```



git status

```
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")

```



git add hello.txt

git commit -m "merge test" hello.txt

```
fatal: cannot do a partial commit during a merge.

```





git commit -m "merge test" (WITHOUT FILE_NAME)

```
[master dff5185] merge test

```



cat hello.txt

```
hello git! 1
hello git! 2 modified 1 hot-fix modified 1
hello git! 3 modified 2
hello git! 4
hello git! 5
modified 1
modified 2
modified 3
modified 4 master modified 1
modified 5 hot-fix modified 1

```





git checkout hot-fix

```
Switched to branch 'hot-fix'

```



cat hello.txt

```
hello git! 1
hello git! 2 modified 1 hot-fix modified 1
hello git! 3 modified 2
hello git! 4
hello git! 5
modified 1
modified 2
modified 3
modified 4
modified 5 hot-fix modified 1

```





# 团队协作





## 1.创建远程仓库



```
git remote -v								查看当前所有远程地址别名

git remote add "ALIAS" "REMOTE ADDRESS"
```





git remote -v

git remote add "git_test" https://github.com/Pendulumm/git_test.git

git remote -v

```
git_test        https://github.com/Pendulumm/git_test.git (fetch)
git_test        https://github.com/Pendulumm/git_test.git (push)

```





## 2.推送本地库到远程库



```http
https://www.runoob.com/git/git-basic-operations.html
```



```
git push "REMOTE HOST" "BRANCH"
```





git checkout master

```
Switched to branch 'master'

```





git push git_test master

```
Enumerating objects: 21, done.
Counting objects: 100% (21/21), done.
Delta compression using up to 4 threads
Compressing objects: 100% (14/14), done.
Writing objects: 100% (21/21), 1.53 KiB | 785.00 KiB/s, done.
Total 21 (delta 6), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (6/6), done.
To https://github.com/Pendulumm/git_test.git
 * [new branch]      master -> master

```



git pull git_test master

```
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 673 bytes | 61.00 KiB/s, done.
From https://github.com/Pendulumm/git_test
 * branch            master     -> FETCH_HEAD
   dff5185..79deeba  master     -> git_test/master
Updating dff5185..79deeba
Fast-forward
 hello.txt | 2 ++
 1 file changed, 2 insertions(+)
```





git clone https://github.com/Pendulumm/git_test.git

(CLONE DOES NOT NEED CREDENTIALS)

```
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 673 bytes | 61.00 KiB/s, done.
From https://github.com/Pendulumm/git_test
 * branch            master     -> FETCH_HEAD
   dff5185..79deeba  master     -> git_test/master
Updating dff5185..79deeba
Fast-forward
 hello.txt | 2 ++
 1 file changed, 2 insertions(+)

```





vim hello.txt

(MODIFIED BY OTHERS)



git status

```
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")

```





git add hello.txt

git commit -m "clone commit 1" hello.txt

```
git commit -m "clone commit 1" hello.txt
[master 3a213e2] clone commit 1
 1 file changed, 2 insertions(+)

```





git push https://github.com/Pendulumm/git_test.git master

```
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 4 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 281 bytes | 281.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/Pendulumm/git_test.git
   79deeba..3a213e2  master -> master

```

