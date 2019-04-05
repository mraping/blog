## 数据库约定
约定数据库里所有表必须包含名为id主键字段。

## api出参约定
约定所有出参里含list，且其他请求会用到这组list，则list里所有对象必须含id唯一标识。

## 入参约定
约定token身份认证统一传入参数模式，后端采用aop切面编程识别用户身份。其他参数一律为json。

## resultfull接口约定
- 尽可能少的使用动词
- 关于版本号，可在模块后增加/v1/等标识

## 参考文章
[某神秘公司 RESTful、共用接口、前后端分离、接口约定的实践](https://mp.weixin.qq.com/s/v7uGrnimNnPLJUTLmfSg9Q)