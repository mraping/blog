SpringBoot使用RestTemplate+HttpClient连接池发送Http消息

## RestTemplate简介
Spring框架提供的RestTemplate类可用于在应用中调用rest服务，它简化了与http服务的通信方式，统一了RESTful的标准，封装了http链接， 我们只需要传入url及返回值类型即可。相较于之前常用的HttpClient，RestTemplate是一种更优雅的调用RESTful服务的方式。

RestTemplate默认依赖JDK提供http连接的能力（HttpURLConnection），如果有需要的话也可以通过setRequestFactory方法替换为例如 Apache HttpComponents、Netty或OkHttp等其它HTTP library。


### maven依赖配置

```
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
   <artifactId>httpclient</artifactId>
   <version>4.5.6</version>
</dependency>
```

### Java配置类
#### HttpPoolProperties.java

```
@Component
@ConfigurationProperties(prefix = "http-pool")
@Data
public class HttpPoolProperties {

    private Integer maxTotal;
    private Integer defaultMaxPerRoute;
    private Integer connectTimeout;
    private Integer connectionRequestTimeout;
    private Integer socketTimeout;
    private Integer validateAfterInactivity;

}
```

#### RestTemplateConfig.java
```
@Configuration
public class RestTemplateConfig {

    @Autowired private HttpPoolProperties httpPoolProperties;

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate(httpRequestFactory());
    }

    @Bean
    public ClientHttpRequestFactory httpRequestFactory() {
        return new HttpComponentsClientHttpRequestFactory(httpClient());
    }

    @Bean
    public HttpClient httpClient() {
        Registry<ConnectionSocketFactory> registry = RegistryBuilder.<ConnectionSocketFactory>create()
                .register("http", PlainConnectionSocketFactory.getSocketFactory())
                .register("https", SSLConnectionSocketFactory.getSocketFactory())
                .build();
        PoolingHttpClientConnectionManager connectionManager = new PoolingHttpClientConnectionManager(registry);
        connectionManager.setMaxTotal(httpPoolProperties.getMaxTotal());
        connectionManager.setDefaultMaxPerRoute(httpPoolProperties.getDefaultMaxPerRoute());
        connectionManager.setValidateAfterInactivity(httpPoolProperties.getValidateAfterInactivity());
        RequestConfig requestConfig = RequestConfig.custom()
                //服务器返回数据(response)的时间，超过抛出read timeout
                .setSocketTimeout(httpPoolProperties.getSocketTimeout())
                //连接上服务器(握手成功)的时间，超出抛出connect timeout
                .setConnectTimeout(httpPoolProperties.getConnectTimeout())
                //从连接池中获取连接的超时时间，超时间未拿到可用连接，会抛出org.apache.http.conn.ConnectionPoolTimeoutException: Timeout waiting for connection from pool
                .setConnectionRequestTimeout(httpPoolProperties.getConnectionRequestTimeout())
                .build();
        return HttpClientBuilder.create()
                .setDefaultRequestConfig(requestConfig)
                .setConnectionManager(connectionManager)
                .build();
    }
}
```


## 使用方法
### 发送GET请求
```
// 1-getForObject()
User user1 = this.restTemplate.getForObject(uri, User.class);

// 2-getForEntity()
ResponseEntity<User> responseEntity1 = this.restTemplate.getForEntity(uri, User.class);
HttpStatus statusCode = responseEntity1.getStatusCode();
HttpHeaders header = responseEntity1.getHeaders();
User user2 = responseEntity1.getBody();
  
// 3-exchange()
RequestEntity requestEntity = RequestEntity.get(new URI(uri)).build();
ResponseEntity<User> responseEntity2 = this.restTemplate.exchange(requestEntity, User.class);
User user3 = responseEntity2.getBody();
```

### 发送POST请求
```
// 1-postForObject()
User user1 = this.restTemplate.postForObject(uri, user, User.class);

// 2-postForEntity()
ResponseEntity<User> responseEntity1 = this.restTemplate.postForEntity(uri, user, User.class);

// 3-exchange()
RequestEntity<User> requestEntity = RequestEntity.post(new URI(uri)).body(user);
ResponseEntity<User> responseEntity2 = this.restTemplate.exchange(requestEntity, User.class);
```

### 设置HTTP Header
```
// 1-Content-Type
RequestEntity<User> requestEntity = RequestEntity
        .post(new URI(uri))
        .contentType(MediaType.APPLICATION_JSON)
        .body(user);

// 2-Accept
RequestEntity<User> requestEntity = RequestEntity
        .post(new URI(uri))
        .accept(MediaType.APPLICATION_JSON)
        .body(user);

// 3-Other
RequestEntity<User> requestEntity = RequestEntity
        .post(new URI(uri))
        .header("Authorization", "Basic " + base64Credentials)
        .body(user);
```

### 开发中发送微信模板消息的示例（Post）
```
 /**
 * 发送模板消息
 * @param template
 * @return
 */
public String sendTemplateMsg(Template template){
    //获取token
    String accessToken = this.getAccess_Token();
    String requestUrl = templateSendUrl.replace("ACCESS_TOKEN", accessToken);

    HttpHeaders headers = new HttpHeaders();
    MediaType type = MediaType.parseMediaType("application/json; charset=UTF-8");
    headers.setContentType(type);
    headers.add("Accept", MediaType.APPLICATION_JSON.toString());

    org.springframework.http.HttpEntity<String> formEntity = new org.springframework.http.HttpEntity<>(template.toJSON(), headers);
    ResponseEntity<String> responseEntity = restTemplate.postForEntity(requestUrl,formEntity,String.class);
    return responseEntity.getBody();
}

```