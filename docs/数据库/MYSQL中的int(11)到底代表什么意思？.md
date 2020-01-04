## 一、前言

在工作中经常要与mysql打交道，但是对mysql的各个字段类型一直都是一知半解，因此写本文总结记录一番。

## 二、简介

![图片描述](https://segmentfault.com/img/bV0wwi?w=497&h=286)

对于int类型的一些基础知识其实上图已经说的很明白了，在这里想讨论下常用的int(11)代表什么意思，很长时间以来我都以为这代表着限制int的长度为11位，直到有天看到篇文章才明白，11代表的并不是长度，而是字符的**显示宽度**，在字段类型为int时，无论你显示宽度设置为多少，int类型能存储的最大值和最小值永远都是**固定**的，这里贴一些原文片段

> The number in the parenthesis does not determines the max and min values that can be stored in the integer field. The max and min values that can be stored are always fixed.
>
> The display width of the column does not affects the maximum value that can be stored in that column. A column with INT(5) or INT(11) can store the same maximum values. Also, if you have a column INT(20) that does not means that you will be able to store 20 digit values (BIGINT values). The column still will store only till the max values of INT.

那么照文中所说，所以无论怎么设置int类型的显示宽度，int所能存储的最大值和最小值是固定的，那么这个显示宽度到底有什么用呢？
当int字段类型设置为**无符号**且**填充零**（UNSIGNED ZEROFILL）时，当数值位数未达到设置的显示宽度时，会在数值前面补充零直到满足设定的显示宽度，为什么会有无符号的限制呢，是因为ZEROFILL属性会隐式地将数值转为无符号型，因此不能存储负的数值。

具体用以下代码解释。

首先创建一张表：

```
CREATE TABLE int_demo (
    id INT(11) NOT NULL AUTO_INCREMENT,
    a INT(11) NOT NULL,
    b INT(11) UNSIGNED ZEROFILL NOT NULL,
    c INT(5) DEFAULT NULL,
    d INT(5) UNSIGNED ZEROFILL NOT NULL,
    e INT(15) DEFAULT NULL,
    PRIMARY KEY (`id`)
)
```

插入两条数据

```
INSERT INTO int_demo (a, b, c, d, e) VALUES (1, 1, 1, 1, 1);
INSERT INTO int_demo (a, b, c, d, e) VALUES (1234567890, 1234567890, 1234567890, 1234567890, 1234567890);
select * from int_demo;
```

| id   | a          | b           | c          | d          | e          |
| ---- | ---------- | ----------- | ---------- | ---------- | ---------- |
| 1    | 1          | 00000000001 | 1          | 00001      | 1          |
| 2    | 1234567890 | 01234567890 | 1234567890 | 1234567890 | 1234567890 |

注释：如果用navicate软件查询出来并不会显示左边的0，但把数据导出时可看到真实的数据，猜测是软件对数据格式进行了处理？

## 三、结论

从上个例子我们可以得出以下几个结论：

1. 如果一个字段设置了无符号和填充零属性，那么无论这个字段存储什么数值，数值的长度都会与设置的显示宽度一致，如上述例子中的字段b，插入数值1显示为00000000001，左边补了10个零直至长度达到11位；
2. 设置字段的显示宽度并不限制字段存储值的范围，比如字段d设置为int(5)，但是仍然可以存储1234567890这个10位数字；
3. 设置的字符宽度只对数值长度不满足宽度时有效，如d字段int(5)，插入1时，长度不足5，因此在左边补充4个零直到5位，但是插入1234567890时**超过**了5位，这时的显示宽度就起不了作用了。