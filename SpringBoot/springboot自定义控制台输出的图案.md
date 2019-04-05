springboot自定义控制台输出的图案1. 在src/main/resources文件夹下 新建banner.txt
2. 编写图案 http://patorjk.com/software/taag/

> banner里的spring版本号

    ${application.version}                   这个是MANIFEST.MF文件中的版本号  
    ${application.formatted-version}         这个是上面的的版本号前面加v后上括号  
    ${spring-boot.version}                   这个是springboot的版本号  
    ${spring-boot.formatted-version}         同上  


> 控制banner的样式，Spring提供了三个枚举类来设定字符的颜色，分别是：

    AnsiColor： 用来设定字符的前景色
    AnsiBackground： 用来设定字符的背景色
    AnsiStyle： 用来控制加粗、斜体、下划线等等
3. 加载banner.gif，也就是动图，让你的控制台更加丰富~

4. banner.txt文件内容示例
```
${AnsiColor.BLACK}
Application Version: ${application.formatted-version}
Spring Boot Version: ${spring-boot.formatted-version}
-////////////////////////////////////////////////////////////////////
-//                          _ooOoo_                               //
-//                         o8888888o                              //
-//                         88" . "88                              //
-//                         (| ^_^ |)                              //
-//                         O\  =  /O                              //
-//                      ____/`---'\____                           //
-//                    .'  \\|     |//  `.                         //
-//                   /  \\|||  :  |||//  \                        //
-//                  /  _||||| -:- |||||-  \                       //
-//                  |   | \\\  -  /// |   |                       //
-//                  | \_|  ''\---/''  |   |                       //
-//                  \  .-\__  `-`  ___/-. /                       //
-//                ___`. .'  /--.--\  `. . ___                     //
-//              ."" '<  `.___\_<|>_/___.'  >'"".                  //
-//            | | :  `- \`.;`\ _ /`;.`/ - ` : | |                 //
-//            \  \ `-.   \_ __\ /__ _/   .-` /  /                 //
-//      ========`-.____`-.___\_____/___.-`____.-'========         //
-//                           `=---='                              //
-//      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^        //
-//            佛祖保佑       永不宕机     永无BUG                    //
-////////////////////////////////////////////////////////////////////
${AnsiColor.BLACK}
```