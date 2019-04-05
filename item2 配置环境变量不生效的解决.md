我用的zsh，由于平时设置的环境变量都是在.bash_profile文件中，每次重启iterm后，都需要重启手动加载.bash_profile文件，很麻烦。

设置自动加载.bash_profile的方法如下： 
在.zshrc配置文件中调用.bash_profile文件 
打开zsh的配置文件.zshrc，在最后面添加一行代码source /Users/mymac/.bash_profile，这样重启iterm便会自动加载.bash_profile文件了。
--------------------- 

文件如下

.zshrc
```
export PATH="~/.composer/vendor/bin:$PATH"
source /Users/suxiongwei/.bash_profile
```

.bash_profile

```
export M2_HOME=/Users/suxiongwei/program/apache-maven-3.6.0
export PATH=$PATH:$M2_HOME/bin
PATH=$PATH:/usr/local/mysql/bin

if [ -f ~/.git-completion.bash ]; then
  . ~/.git-completion.bash
fi
#enables colorin the terminal bash shell export
export CLICOLOR=1

#setsup thecolor scheme for list export
export LSCOLORS=gxfxcxdxbxegedabagacad

#sets up theprompt color (currently a green similar to linux terminal)
export PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;36m\]\w\[\033[00m\]\$     '
#enables colorfor iTerm
export TERM=xterm-256color

find_git_branch () {
    local dir=. head
    until [ "$dir" -ef / ]; do
        if [ -f "$dir/.git/HEAD" ]; then
            head=$(< "$dir/.git/HEAD")
            if [[ $head = ref:\ refs/heads/* ]]; then
                git_branch=" → ${head#*/*/}"
            elif [[ $head != '' ]]; then
                git_branch=" → (detached)"
            else
                git_branch=" → (unknow)"
            fi
            return
        fi
        dir="../$dir"
    done
    git_branch=''
}
PROMPT_COMMAND="find_git_branch; $PROMPT_COMMAND"
black=$'\[\e[1;30m\]'
red=$'\[\e[1;31m\]'
green=$'\[\e[1;32m\]'
yellow=$'\[\e[1;33m\]'
blue=$'\[\e[1;34m\]'
magenta=$'\[\e[1;35m\]'
cyan=$'\[\e[1;36m\]'
white=$'\[\e[1;37m\]'
normal=$'\[\e[m\]'

PS1="$white[$magenta\u$white@$green\h$white:$cyan\w$yellow\$git_branch$white]\$ $normal"
#测试环境
alias stag00="ssh root@47.93.180.171"
alias stag01='ssh root@39.107.59.19'

#管理员端
alias prod00="ssh root@47.93.115.220"
alias prod01="ssh root@47.94.43.76"

#考生端
alias exam00="ssh root@47.93.182.99"
alias exam01="ssh root@47.93.182.72"
alias exam02="ssh root@39.107.205.30"
alias exam03="ssh root@47.94.34.31"
alias exam04='ssh root@39.97.3.179'

# 考试服务
alias server00="ssh root@39.96.115.177"
alias server01='ssh root@39.96.115.197'

# 统计分析和超管
alias exam05="ssh root@47.95.32.86"

alias hf01='ssh root@47.93.138.64'
alias hf02='ssh root@39.107.204.82'
alias hf03='ssh root@47.93.137.225'
```

