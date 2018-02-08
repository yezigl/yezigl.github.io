### 使用FeignClient发送Http请求
在使用了Spring Cloud全家桶之后，我们一般使用FeignClient来请求一个http接口，而不在使用HttpClient去封装请求，那么怎么用呢？

代码如下：

```
@FeignClient(name = "test", url = "http://www.test.com", configuration = TestConfiguration.class)
public class TestClient {

    @GetMapping("/get")
    public TestResponse testGet(
            @RequestParam("a") String a,
            @RequestParam("b") String b
    ) {
        // TODO
    }
    
    @GetMapping("/post1")
    public TestResponse testPost1(
            @RequestParam("a") String a,
            @RequestParam("b") String b
    ) {
        // TODO
    }
    
    @GetMapping("/post2")
    public TestResponse testPost2(
            @RequestBody TestRequest testRequest
    ) {
        // TODO
    }
}

class TestRequest() {
    String a;
    String b;
}
```

以上就配置完了接口，在需要使用的地方直接调用`TestClient`就可以了。

上面的GET请求没什么问题，最终的url如下：

```
http://www.test.com/get?a=a&b=b
```

而POST请求是这样的

对于post1这个请求，请求的url同GET类似，只是Content-Type是application/json：

```
http://www.test.com/post?a=a&b=b
```
对于post2，就跟平常一样，Content-Type是application/json，请求的body是`{"a": "a", "b":"b"}`。

如果服务端也是自己的服务，那么POST这么请求没什么问题，如果请求是一个外部接口，请求的格式又是一个form，改怎么办呢？

对Spring feign简单研究发现，对于request body的处理同response body类似，也是使用的HttpMessageConverter，我们看一下它的实现类，发现有一个FormHttpMessageConverter，声明如下：

```
public class FormHttpMessageConverter implements HttpMessageConverter<MultiValueMap<String, ?>> {
...
}
```
看到这就明白了，参数为MultiValueMap的方法，请求时会使用`application/x-www-form-urlencoded`格式，所以在Client中要这么写：

```
    @GetMapping("/post2")
    public TestResponse testPost1(MultiValueMap<String, ?> params) {
        // TODO
    }
```

