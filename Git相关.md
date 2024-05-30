### 1.打开Git-bash自动配置ssh私钥并启动ssh-agent

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

### 2.
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/623a5d2d-78bc-4f9b-a7e8-c1ec73d4b476)
