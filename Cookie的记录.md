## 同一应用服务器内共享方法
```
cookie.setPath("/");
```
## 跨域共享cookie的方法
```
cookie.setDomain(".xxx.com"); 
```
> 注意：这个参数必须以“.”开始。
