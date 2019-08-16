## request.getParameter()、request.getInputStream()和request.getReader()

## request的Content-Type小结

application/x- www-form-urlencoded是Post请求默认的请求体内容类型，也是form表单默认的类型。**Servlet API规范中对该类型的请求内容提供了request.getParameter()方法来获取请求参数值。但当请求内容不是该类型时，需要调用request.getInputStream()或request.getReader()方法来获取请求内容值。**

当请求体内容（注意：get请求没有请求体）类型是application/x- www-form-urlencoded时也可以直接调用request.getInputStream()或request.getReader()方法获取到请求内容再解析出具体都参数，但前提是还没调用request.getParameter()方法。此时当request.getInputStream()或request.getReader()获取到请求内容后，无法再调request.getParameter()获取请求内容。即对该类型的请求，三个方法互斥，只能调其中一个。今天遇到一个Controller请求经过Spring MVC 的RequestMapping处理后，只能通过request.getParameter()获取到参数、无法通过request.getInputStream()和request.getReader()读取内容很可能就是因为在请求经过Spring MVC时已调用过request.getParameter()方法的原因。

**注意：在一个请求链中，请求对象被前面对象方法中调用request.getInputStream()或request.getReader()获取过内容后，后面的对象方法里再调用这两个方法也无法获取到客户端请求的内容，但是调用request.getParameter()方法获取过内容后，后面的对象方法里依然可以调用它获取到参数的内容。**

当请求体内容是其它类型时，比如 multipart/form-data或application/json时，无法通过request.getParameter()获取到请求内容，此时只能通过request.getInputStream()和request.getReader()方法获取请求内容，此时调用request.getParameter()也不会影响第一次调用request.getInputStream()或request.getReader()获取到请求内容。

request.getInputStream()返回请求内容字节流，多用于文件上传，request.getReader()是对前者返回内容的封装，可以让调用者更方便字符内容的处理（不用自己先获取字节流再做字符流的转换操作）。



ajax向后台传对象时，@RequestBody 接收对象

@RequestBody 接收的是json字符串，不能为json对象，需保持类型一致

```json
var data =  {"id" : $("#id").val(),"userId" : $("#userId").val(),"visitInstruct" : $("#visitInstruct").val(),"isVisit" : isVisit}
$.ajax({
	url : "/api/updateFeedback",
	async : false,
	type : "POST",
	contentType : 'application/json',
	dataType : 'json',
	data :JSON.stringify(data),
	success : function(data) {
		lert("111");
	}
});
```

其中，dataType和contentType也必须设置成json格式的，而JSON.stringify(obj)这个函数可以把json对象转换为json字符串，这**三个设置**是必不可少的。