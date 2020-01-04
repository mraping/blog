```shell
#!/usr/bin/env bash
# 项目名称
CODE_DIR="project"
# 打包后的jar名称前缀
PACKAGED_NAME_ADMIN="app-1.0.0"
# jar存放位置
APP_DEPLOY_PATH="/www/java"
# JAVA_HOME="/usr/java/jdk1.8.0_161"
# spring boot用哪个环境运行
PROFILE="produce"
# PORT=39005

#如果任何语句的执行结果不是true则应该退出
set -e

#git初始化配置，配置后，无需手动输入用户名及密码即可从指定git管理代码
function gitinit(){
 echo "start gitinit..."
 cd ~/
 touch .git-credentials
 # 这里的git地址和账号请修改
 echo "https://yourname@shangshiwendao.com:password@gitee.com/fuwu360/project.git" > .git-credentials
 git config --global credential.helper store
 #执行此句后~/.gitconfig文件多了一项[credential] helper = store
 echo "finish gitinit..."

}
function clone(){
 echo "start pull git resource code..."
 rm -rf ./$CODE_DIR
 # 请自行修改这里的git地址为你的项目地址
 git clone https://gitee.com/fuwu360/project.git
 cd ./$CODE_DIR
 echo "git checkout succeed ..."
}

#pull and package gitcodeResource 
function package(){
 echo "start package..."
 cd ./$CODE_DIR
 git pull
 mvn clean package -Dmaven.test.skip=true
 echo "packaging app success ..."
}

#code deploy
function deploy(){
 cd $APP_DEPLOY_PATH
 # 如果备份目录back不存在则创建
 if [ ! -d backup ];then
 mkdir backup
 else
 echo "dir backup exist"
 fi
 echo "重命名旧版本并备份..."
 if [ -f $PACKAGED_NAME_ADMIN.jar ]
 then
 mv $PACKAGED_NAME_ADMIN.jar ./backup/$PACKAGED_NAME_ADMIN'_'`date +%Y%m%d%H%M%S`.jar
 fi
 echo "复制新的jar..."
 cp $APP_DEPLOY_PATH/$CODE_DIR/target/$PACKAGED_NAME_ADMIN.jar $APP_DEPLOY_PATH
}

# 停止进程
function shutdown(){
 PID=$(ps -ef | grep $PACKAGED_NAME_ADMIN.jar | grep -v grep | awk '{ print $2 }')
 if [ -z "$PID" ]
 then
 echo jar is already stopped
 else
 echo kill $PID
 kill -9 $PID
 fi
}

# app startup
function startup(){
 echo "startuping"
 shutdown
 package
 deploy
 # export JAVA_HOME=$JAVA_HOME
 # app_log.txt是日志名字，请自行修改
 nohup java -jar $PACKAGED_NAME_ADMIN.jar --spring.profiles.active=$PROFILE >app_log.txt 2>&1 &
 echo "startuping success ..."
 # echo "打开端口："$PORT"..."
 # firewall-cmd --zone=public --add-port=$PORT/tcp --permanent

}

#pring helpinfo
function help(){
 echo "Usage: ./run_app.sh [gitinit|clone|package|deploy|startup|shutdown|help]"
 echo "gitinit:初始化git设置"
 echo "package:程序打包"
 echo "deploy:程序发布"
 echo "startup:程序启动"
 echo "shutdown:程序关闭"
 echo "help:打印帮助信息"
}

case "$1" in
 'gitinit')
 gitinit
 ;;
 'clone')
 clone
 ;;
 'package')
 package
 ;;
 'deploy')
 deploy
 ;;
 'startup')
 startup
 ;;
 'shutdown')
 shutdown
 ;;
 'help')
 help
 ;;
 *)


esac
exit 0
```

