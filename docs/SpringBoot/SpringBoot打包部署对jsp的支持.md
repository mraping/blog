## 问题引出
项目中SpringBoot选择了jsp作为视图，在打包部署的时候选择了jar的方式，出现了jsp页面无法访问的问题。
## 解决方式
需要打成 war 包
```
<packaging>war</packaging>
```
## 启动
```
nohup java -jar xxx.war > nohup.out &
```