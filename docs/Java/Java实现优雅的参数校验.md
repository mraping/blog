# Java实现优雅的参数校验

# 1. Hibernate Validator 简介

平时项目中，难免需要对参数 进行一些参数正确性的校验，这些校验出现在业务代码中，让我们的业务代码显得臃肿，而且，频繁的编写这类参数校验代码很无聊。鉴于此，觉得 Hibernate Validator 框架刚好解决了这些问题，可以很优雅的方式实现参数的校验，让业务代码 和 校验逻辑 分开,不再编写重复的校验逻辑。
Hibernate Validator 是 Bean Validation 的参考实现 . Hibernate Validator 提供了 JSR 303 规范中所有内置 constraint 的实现，除此之外还有一些附加的 constraint。

# 2. Hibernate Validator 的作用

- 验证逻辑与业务逻辑之间进行了分离，降低了程序耦合度；
- 统一且规范的验证方式，无需你再次编写重复的验证代码；
- 你将更专注于你的业务，将这些繁琐的事情统统丢在一边。

# Hibernate Validator 的使用

## 引入依赖

```xml
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>6.0.9.Final</version>
</dependency>
<dependency>
    <groupId>javax.el</groupId>
    <artifactId>javax.el-api</artifactId>
    <version>3.0.0</version>
</dependency>
<dependency>
    <groupId>org.glassfish</groupId>
    <artifactId>javax.el</artifactId>
    <version>3.0.1-b08</version>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>${lombok.version}</version>
</dependency>
```

## 测试类

```css
package com.netease.cloud.scaffold.entity;

import com.netease.cloud.scaffold.annotation.validator.DateValidator;
import com.netease.cloud.scaffold.entity.group.AccountGroup;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.validator.constraints.Length;
import org.springframework.format.annotation.DateTimeFormat;

import javax.validation.constraints.Max;
import javax.validation.constraints.Min;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Pattern;
import java.util.Date;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class MyAccount {
    private String id;

    @NotNull
    @Length(max = 20)
    private String userName;

    @NotNull
    @Pattern(regexp = "[A-Z][a-z][0-9]")
    private String passWord;

    @DateTimeFormat(pattern = "yyy-MM-dd")
    private Date createTime;

    private String alias;

    @Max(10)
    @Min(1)
    private Integer level;

    private Integer vip;
}
```

API访问

```java
    @PostMapping("/mytest")
    @ResponseBody
    public Object myTest(@RequestBody @Valid MyAccount account) {
        return account;
    }
```

## 封装工具类

```java
package com.netease.cloud.scaffold.util;

import lombok.Data;
import org.hibernate.validator.HibernateValidator;

import javax.validation.ConstraintViolation;
import javax.validation.Validation;
import javax.validation.Validator;
import java.util.ArrayList;
import java.util.List;
import java.util.Set;
import java.util.stream.Collectors;

public class ValidationUtil {
    /**
     * 开启快速结束模式 failFast (true)
     */
    private static Validator validator = Validation.byProvider(HibernateValidator.class).configure().failFast(false).buildValidatorFactory().getValidator();

    /**
     * 校验对象
     *
     * @param t      bean
     * @param groups 校验组
     * @return ValidResult
     */
    public static <T> ValidResult validateBean(T t, Class<?>... groups) {
        ValidResult result = new ValidationUtil().new ValidResult();
        Set<ConstraintViolation<T>> violationSet = validator.validate(t, groups);
        boolean hasError = violationSet != null && violationSet.size() > 0;
        result.setHasErrors(hasError);
        if (hasError) {
            for (ConstraintViolation<T> violation : violationSet) {
                result.addError(violation.getPropertyPath().toString(), violation.getMessage());
            }
        }
        return result;
    }

    /**
     * 校验bean的某一个属性
     *
     * @param obj          bean
     * @param propertyName 属性名称
     * @return ValidResult
     */
    public static <T> ValidResult validateProperty(T obj, String propertyName) {
        ValidResult result = new ValidationUtil().new ValidResult();
        Set<ConstraintViolation<T>> violationSet = validator.validateProperty(obj, propertyName);
        boolean hasError = violationSet != null && violationSet.size() > 0;
        result.setHasErrors(hasError);
        if (hasError) {
            for (ConstraintViolation<T> violation : violationSet) {
                result.addError(propertyName, violation.getMessage());
            }
        }
        return result;
    }

    /**
     * 校验结果类
     */
    @Data
    public class ValidResult {
        /**
         * 是否有错误
         */
        private boolean hasErrors;

        /**
         * 错误信息
         */
        private List<ErrorMessage> errors;

        public ValidResult() {
            this.errors = new ArrayList<>();
        }

        public boolean hasErrors() {
            return hasErrors;
        }

        public void setHasErrors(boolean hasErrors) {
            this.hasErrors = hasErrors;
        }

        /**
         * 获取所有验证信息
         *
         * @return 集合形式
         */
        public List<ErrorMessage> getAllErrors() {
            return errors;
        }

        /**
         * 获取所有验证信息
         *
         * @return 字符串形式
         */
        public String getErrors() {
            StringBuilder sb = new StringBuilder();
            for (ErrorMessage error : errors) {
                sb.append(error.getPropertyPath()).append(":").append(error.getMessage()).append(" ");
            }
            return sb.toString();
        }

        public void addError(String propertyName, String message) {
            this.errors.add(new ErrorMessage(propertyName, message));
        }

        /**
         * 获取去重之后的非法属性值，以逗号分隔
         * @return
         */
        public String getProperties() {
            return errors.stream().map(error -> error.getPropertyPath()).collect(Collectors.toSet()).stream().collect(Collectors.joining(", "));
        }
    }

    @Data
    public class ErrorMessage {

        private String propertyPath;

        private String message;

        public ErrorMessage() {
        }

        public ErrorMessage(String propertyPath, String message) {
            this.propertyPath = propertyPath;
            this.message = message;
        }
    }
}
```

自定义校验器：



```java
import org.apache.commons.lang3.time.DateUtils;

import javax.validation.Constraint;
import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;
import javax.validation.Payload;
import java.lang.annotation.Documented;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;
import java.text.ParseException;
import java.util.Date;

import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

@Target({FIELD})
@Retention(RUNTIME)
@Documented
@Constraint(validatedBy = {DateValidator.DateValidatorInner.class})
public @interface DateValidator {

    /**
     * 必须的属性
     * 显示 校验信息
     * 利用 {} 获取 属性值，参考了官方的message编写方式
     *
     * @see org.hibernate.validator 静态资源包里面 message 编写方式
     */
    String message() default "日期格式不匹配{dateFormat}";

    /**
     * 必须的属性
     * 用于分组校验
     */
    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};

    /**
     * 非必须
     */
    String dateFormat() default "yyyy-MM-dd HH:mm:ss";

    /**
     * 必须实现 ConstraintValidator接口
     */
    class DateValidatorInner implements ConstraintValidator<DateValidator, String> {
        private String dateFormat;

        @Override
        public void initialize(DateValidator constraintAnnotation) {
            this.dateFormat = constraintAnnotation.dateFormat();

        }

        /**
         * 校验逻辑的实现
         *
         * @param value 需要校验的 值
         * @return 布尔值结果
         */
        @Override
        public boolean isValid(String value, ConstraintValidatorContext context) {
            if (value == null) {
                return true;
            }
            if ("".equals(value)) {
                return true;
            }
            try {
                Date date = DateUtils.parseDate(value, dateFormat);
                return date != null;
            } catch (ParseException e) {
                return false;
            }
        }
    }
}
```

定义分组：

```java
public interface AccountGroup {
}
```

使用自定义校验器和自定义分组：

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class MyAccount {
    private String id;

    @NotNull
    @Length(max = 20)
    private String userName;

    @NotNull
    @Pattern(regexp = "[A-Z][a-z][0-9]")
    private String passWord;

    @DateTimeFormat(pattern = "yyy-MM-dd")
    private Date createTime;

//  @Pattern(regexp = "[A-Z][a-z][0-9]")
    @Min(2)
    private String alias;

    @Max(10)
    @Min(1)
    private Integer level;

    private Integer vip;

    @DateValidator(dateFormat = "yyyy-MM-dd", groups = {AccountGroup.class})
    private String birthday;
}
```

几个测试：

```java
@Test
    public void test5() {
        MyAccount account = new MyAccount();
        account.setAlias("kalakala");
//      account.setUserName("wokalakala");
        account.setPassWord("密码");
        account.setBirthday("2019.12.1");
        ValidationUtil.ValidResult validResult = ValidationUtil.validateBean(account);
        if (validResult.hasErrors()) {
            String properties = validResult.getProperties();
            System.out.println(properties);
        }
    }

    @Test
    public void test6() {
        MyAccount account = new MyAccount();
        account.setAlias("kalakala");
        account.setUserName("wokalakala");
        account.setPassWord("密码");
        account.setBirthday("2001.10.02");
        // 指定分组 AccountService.class
        ValidationUtil.ValidResult validResult = ValidationUtil.validateBean(account, AccountGroup.class);
        if (validResult.hasErrors()) {
            String errors = validResult.getErrors();
            String properties = validResult.getProperties();
            System.out.println(errors);
            System.out.println(properties);
        }
    }
```

## 其他常用constranint

```kotlin
@AssertFalse @AssertTrue  检验boolean类型的值

@DecimalMax @DecimalMin  限定被标注的属性的值的大小

@Digits(intege=,fraction=) 限定被标注的属性的整数位数和小数位数

@Future检验给定的日期是否比现在晚

@Past    校验给定的日期是否比现在早

@Max检查被标注的属性的值是否小于等于给定的值

@Min检查被标注的属性的值是否大于等于给定的值

@NotNull检验被标注的值不为空

@Null     检验被标注的值为空

@Pattern(regex=,flag=)  检查该字符串是否能够在match指定的情况下被regex定义的正则表达式匹配

@Size(min=,max=)  检查被标注元素的长度

@Valid递归的对关联的对象进行校验
```