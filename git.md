## Git学习笔记

#### 1. 关联远程仓库

```shell
# 假设已有远程仓库地址git@github.com:michaelliao/learngit.git

# 1.关联一个远程库，使用命令，关联一个远程库时必须给远程库指定一个名字，origin是默认习惯命名；
git remote add origin git@server-name:path/repo-name.git；

# 2.关联后，第一次推送master分支的所有内容；
# 加了参数-u后，以后即可直接用git push 代替git push origin master
git push -u origin master

# 3.每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；
git push origin master
```

#### 2. **git push** 

**git push** 命用于从将本地的分支版本上传到远程并合并

```shell
# 命令格式如下：
git push <远程主机名> <本地分支名>:<远程分支名>

# 如果本地分支名与远程分支名相同，则可以省略冒号：
git push <远程主机名> <本地分支名>

# 实例
# 将本地的 master 分支推送到 origin 主机的 master 分支。
git push origin master
# 相当于
git push origin master:master
```

#### 3. git pull

**git pull** 命令用于从远程获取代码并合并本地的版本。

**git pull** 其实就是 **git fetch** 和 **git merge FETCH_HEAD** 的简写。

```shell
# 命令格式如下：
git pull <远程主机名> <远程分支名>:<本地分支名>

# git pull实例
# 将远程仓库dev分支合并到主分支mater
git pull origin dev:master
```

#### 4. git提交实例

```shell
# 新建文件
touch runoob-test.txt

# 第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区；
git add runoob-test.tx

# 第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支
git commit -m "添加到远程"
master 69e702d] 添加到远程
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 runoob-test.txt
# 推送到远程仓库，将本地的 master 分支推送到 origin 主机的 master 分支
git push origin master
```

#### 5. git创建分支并推送到远程分支

```shell
# 列出分支
git branch
* master
  release

# git status命令用于查看在你上次提交之后是否有对文件进行再次修改。 
git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean

# 创建dev分支，然后切换到dev分支，创建并切换到新的dev分支
git checkout -b dev
# 或者git switch -c dev
# git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：
git branch dev
git checkout dev

# 切换分支：
git checkout <name>
git switch <name>

# 删除dev分支
git branch -d dev

# 把新建的本地分支push到远程服务器
# 1.远程没有local_branch分支，本地已经切换到local_branch(远程新建名为remote_branch分支，并push代码)
git push origin local_branch:remote_branch

# 2.远程已有remote_branch分支并且已经关联本地分支local_branch且本地已经切换到local_branch(直接push代码)
git push

# 3.远程已有remote_branch分支但未关联本地分支local_branch且本地已经切换到local_branch(关联分支并push代码)
git push -u origin/remote_branch
```

#### 6. 分支提交实例

```shell
# 分支提交实例
# 切换master到dev branch
$ git switch dev
Switched to branch 'dev'

# 查看当前分支
$ git branch
* dev
  master
# 新建Student.java
​```Student.java
public class Student{

    public static void main(String[] args) {
        system.out.println("I am a student");
    }
}
​```Student.java

# 查看当前状态
$ git status
On branch dev
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        Student.java

nothing added to commit but untracked files present (use "git add" to track)

# git add把文件添加进去，实际上就是把文件修改添加到暂存区；
git add Student.java
# 第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支
git commit -m "Student.java"
# 推送到远程仓库，将本地的 master 分支推送到 origin 主机的 master 分支
git push origin dev:dev

# 切换到主分支
$ git switch master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.

# 将远程仓库dev分支合并到主分支mater
git pull origin dev:master

# 合并后push到远程主分支
git push
```

