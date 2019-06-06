## 简介
swagger,中文“拽”的意思。它是一个功能强大的api框架，它的集成非常简单，不仅提供了在线文档的查阅，而且还提供了在线文档的测试。另外swagger很容易构建restful风格的api，简单优雅帅气，正如它的名字。
## 与SpringBoot的集成
### 引入依赖
```
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.6.1</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.6.1</version>
</dependency>
```
### 写配置类
```
package com.ksxing.configuration;

/**
 * Swagger2 配置类
 * @Profile({"dev"}) 用来配置在生产环境禁用Swagger2
 */
@Configuration
@EnableSwagger2
@Profile({"dev"})
public class Swagger2 {

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.xxx.controller"))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("xxx系统api文档")
                .description("简单优雅的restful风格，http://www.xxx.com/")
                .termsOfServiceUrl("http://www.xxx.com/")
                .version("1.0")
                .build();
    }
}

```
### 写生成文档的注解
swagger通过注解表明该接口会生成文档，包括接口名、请求方法、参数、返回信息的等等。
 - @Api：修饰整个类，描述Controller的作用
 - @ApiOperation：描述一个类的一个方法，或者说一个接口
 - @ApiParam：单个参数描述
 - @ApiModel：用对象来接收参数
 - @ApiProperty：用对象接收参数时，描述对象的一个字段
 - @ApiResponse：HTTP响应其中1个描述
 - @ApiResponses：HTTP响应整体描述
 - @ApiIgnore：使用该注解忽略这个API
 - @ApiError ：发生错误返回的信息
 - @ApiParamImplicitL：一个请求参数
 - @ApiParamsImplicit： 多个请求参数
### 启动项目，查看API效果
访问：http://localhost:8080/swagger-ui.html 