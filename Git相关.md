### 1. 打开Git-bash自动配置ssh私钥并启动ssh-agent

1、在 “C:\Users\用户名” 目录下创建.profile文件

2、文件内写入：
```Plain Text
env=~/.ssh/agent.env

agent_load_env () { test -f "$env" && . "$env" >| /dev/null ; }

agent_start () {
    (umask 077; ssh-agent >| "$env")
    . "$env" >| /dev/null ; }

agent_load_env

# agent_run_state: 0=agent running w/ key; 1=agent w/o key; 2=agent not running
agent_run_state=$(ssh-add -l >| /dev/null 2>&1; echo $?)

if [ ! "$SSH_AUTH_SOCK" ] || [ $agent_run_state = 2 ]; then
    agent_start
    # ssh-add ~/.ssh/your_self_rsa 后面跟具体私钥名，不写就是默认加载id_rsa
    ssh-add
elif [ "$SSH_AUTH_SOCK" ] && [ $agent_run_state = 1 ]; then
    # ssh-add ~/.ssh/your_self_rsa 后面跟具体私钥名，不写就是默认加载id_rsa
    ssh-add
fi

unset env
```

3、重启shell

### 2. 基础
git init  // 初始化仓库<br>
git status  // 查看状态<br>
git branch  // 查看项目下所有分支<br>
git log  // 查看历史日志，q退出<br>
git add fileName 或者 git add .  // 加入到版本控制<br>
git commit -m "要写的注释"  // 提交<br>
git branch xxx  // 创建分支<br>
git checkout xxx  // 切换分支<br>
git checkout -b xxx  // 创建并切换到 xxx 分支<br>
git merge xxx  // 将 xxx 合并过来到当前分支<br>
git branch -d xxx  // 删除分支<br>
git branch -D xxx  // 强制删除分支<br>
git remote add {远端代码库名字} {远端代码库地址(如：https://yyy.git)}  // 添加远端代码库<br>
git push {远端代码库名字} {本地分支名}  // push本地分支到远端代码库，远程代码库产生的分支自动使用本地分支名<br>
git fetch {远端代码库名字}  // 拉取全部分支<br>
git pull {远端代码库名字} {本地分支}  // 拉取某个分支<br>

### 3. 给git命令取别名，缩短输入，快速开发
git config --global alias.c 'commit -m'  // 给 commit -m 建立别名为c，以后提交时调用 git c {注释} 即可<br>
git config --global alias.co 'checkout'  // 给 checkout 建立别名<br>

### 4. 合并分支时，不想直接变成 rebase，而是想必须有合入节点 merge

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/623a5d2d-78bc-4f9b-a7e8-c1ec73d4b476)

### 5. 如果已经commit了，还没推远端，此时想修改commit信息

git commit --amend

### 6. 撤销一次commit操作，让HEAD回到上一次的地方

git reset HEAD^ [--hard]可选是否强制执行

### 7. 变基操作 rebase，同时 squash commits

1. git rebase {要变基到的分支的 hash 或者 分支名} -i
2. 摁i，进入编辑模式，一般保留第一行的`pack`，其他改成`s`，即 squash 的意思
3. 摁ESC退出编辑模式，然后输入`:wq`完成操作
4. git-bash会继续完成rebase操作，让进行commit，此时直接摁`d`一直删到留下最新的一条，然后修改commit信息（如何修改看2、3步骤）
5. 输入`:wq`完成整体变基操作
6. 如果中间解决了冲突，最后需要git rebase --continue

### 8. 三路合并算法

https://marsishandsome.github.io/2019/07/Three_Way_Merge

### 9. 将Head回到历史某次提交，慎重操作
1. git reflog // 查看历史日志，找到要回到的提交的hash
2. git reset --hard {要回到的提交的hash}

### 10. 找回本地强制删除的提交
1. git log -g // 使用此命令找到要恢复的commit，记录下commitId
2. git recover branch {新分支名} commitId

### 11. fetch 远程代码报错：error setting certificate file: F:/Git/mingw64/etc/ssl/certs/ca-bundle.crt（错误的crt地址）
1. git config --system http.sslcainfo "正确的crt地址"

### 12. 给IDEA添加 git-bash 快捷启动
![image](https://github.com/user-attachments/assets/b5edefff-68fa-4d96-a98e-cdf01d6f2dd8)

### 13. 将已经跟踪的文件，解除git跟踪
1. 为了避免混乱，建议先把要解除跟踪的文件路径写到合适位置的 `.gitignore` 里
2. 进入项目目录，打开 git bash
3. git rm -r -n --cached {要解除的文件路径} // -n 表示查看哪些文件可能会被解除，并不真的执行
4. git rm -r --cached {要解除的文件路径} // 正式执行解除
5. 解除后表示这个文件已经回到了不受管理的原始状态，由于第1步已经把它们的路径写到了 `.gitignore` 里，所以此时这个文件便真正处于 ignore 状态了
6. 将刚刚解除跟踪的文件删除，然后和 `.gitignore` 文件一并提交上去
7. 解释说明：因为远端还有这个文件，所以要进行一次删除操作，也就是说，要想解除某个文件的跟踪，本地先要存在这个文件，第4步才能正确执行，文件才能被解除管理
8. 如何验证：使用 git fetch 拉代码到新的项目目录，看看ignore的文件是否已经不会拉回来了，然后本地修改刚刚解除管理的文件，看看是否会被认为需要提交

### 14. 误操作 git reset current branch to here, 如何撤回
1. 确保还没提交代码到远端
2. git log -g // 查看想回到的那个commit的hash id
3. git reset --mixed {hash id} // --mixed是当时误操作时选的合并方式，默认是mixed，如果当时改了，就用对应的操作

### 15. 将当前分支推送到另一个仓库（可能用于基于某个阶段的代码开起新库）
1. 添加远程仓库: git remote add {给远程仓库起个名} {远程仓库地址}
例如：git remote add new-origin https://github.com/username/new-repo.git
2. 查看远程仓库: git remote -v
3. 推送代码: git push {刚才给远程仓库起的名} {当前分支名}:{远程分支名}
例如：git push new-origin master:master-new
如果想远程分支名和本地分支名一样，可以直接：git push new-origin master
4. 查看远程所有分支名: git ls-remote --heads {刚才给远程仓库起的名}
例如：git ls-remote --heads new-origin
