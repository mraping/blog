在Java中，使用break可以跳出循环，默认情况下是跳出最里层的循环，假如我们要跳出多层循环怎么办呢，Java替我们已经做好了这一点，就是用 循环标签 ：即是对某个循环定义一个名字，然后在 break 后面加上这个名字，当符合 break 条件时，程序就会跳到规定的循环那。这个有点像我们编写PL/SQL程序。下面来一个例子吧，说明下这个问题。

```java
public static void main(String[] args){
		lableB:
		for(int i=0;i<3;i++){
			lableA:
			for(int j=0;j<3;j++){
				System.out.println(j);
				if(j==1){
					break lableB;
				}
			}
		}		
		System.out.println("over!");
}
```


 标签名的命名方法是：java命名规则 和 半角冒号  比如： lableA: 

PS：lableB标签的定义需要在使用break lableB语句之前定义。

break只跳出当前for循环

return是结束当前方法的执行

continue是终止当前循环语句的执行，继续下一条循环语句