## java中异常抛出后代码还会继续执行吗

###  在下面的代码中，当抛出运行时异常后，后面的代码还会执行吗，是否需要在异常后面加上return语句呢？

```java
public void add(int index, E element){
    if(size >= elements.length) {
        throw new RuntimeException("顺序表已满，无法添加"); 
        //return;  //需要吗？
    }
    ....
}
```

为了回答这个问题，我编写了几段代码测试了一下，结果如下：

```java
//代码1
public static void test() throws Exception  {

    throw new Exception("参数越界"); 
    System.out.println("异常后"); //编译错误，「无法访问的语句」
}
//代码2
try{
    throw new Exception("参数越界"); 
}catch(Exception e) {
    e.printStackTrace();
}
System.out.println("异常后");//可以执行
//代码3
if(true) {
    throw new Exception("参数越界"); 
}
System.out.println("异常后"); //抛出异常，不会执行
```

### 总结：

1. 若一段代码前有异常抛出，并且这个异常没有被捕获，这段代码将产生编译时错误「无法访问的语句」。如代码1
2. 若一段代码前有异常抛出，并且这个异常被try...catch所捕获，若此时catch语句中没有抛出新的异常，则这段代码能够被执行，否则，同第1条。如代码2
3. 若在一个条件语句中抛出异常，则程序能被编译，但后面的语句不会被执行。如代码3



以上问题是在查看项目中的一段代码时的一些疑惑：

```java
public String examMgrNew(Invocation inv, Model model) {
        UsersModel usersModel = (UsersModel) inv.getModel(Constant.SESSION_USER_KEY);

        SubAdminModel subAdminModel = subAdminBiz.getSubAdminByUserId(usersModel.getId());
        try {
            if (!RoleUtil.isAdminOrSuperAdmin(usersModel.getRole()) && subAdminModel != null && subAdminModel.getAllowExamMgr() == 0) {
                throw new ServiceException(ErrorCodeEnum.ACCOUNT_NO_PERSSION);
            }
        } catch (Exception e) {
            LOGGER.error("{},{}出现异常:","AdminController","examMgrNew",e);
            /**
             * 实际的业务逻辑，在catch到用户没有权限的异常之后，应该直接提示错误，就不应该执行接下来的业务代码，返回视图
             * 因此需加入以下这行代码
             */
            throw new ServiceException(ErrorCodeEnum.ACCOUNT_NO_PERSSION);
        }

        CompanysNewModel companysNewModel = companyBiz.getCompanysNewByCompanyId(usersModel.getCompanyId());

        //到期时间格式转换
        if (companysNewModel.getExpirationTime() != null) {
            String expirationTime = companysNewModel.getExpirationTime().split("\\s+")[0];
            companysNewModel.setExpirationTime(expirationTime);
        }

        model.add("user", usersModel);
        model.add("company", companysNewModel);
        model.add("sub_admin_right", subAdminModel);

        return "admin/exam_mgr_new";
    }
```

