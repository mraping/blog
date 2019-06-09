- @RequestBody主要用来接收前端传递给后端的json字符串中的数据的(请求体中的数据的);
- GET方式无请求体，所以使用@RequestBody接收数据时，前端不能使用GET方式提交数据，而是用POST方式进行提交。
- 在后端的同一个接收方法里，@RequestBody 与@RequestParam()可以同时使用，@RequestBody最多只能有一个，而@RequestParam()可以有多个。
- @requestBody注解常用来处理content-type不是默认的application/x-www-form-urlcoded编码的内容，比如说：application/json或者是application/xml等。一般情况下来说常用其来处理application/json类型。



ajax向后台传对象时，@RequestBody 接收对象

@RequestBody 接收的是json字符串，而ajax传送的是json对象，类型不一致

```
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

其中，dataType和contentType也必须设置成json格式的，而JSON.stringify(obj)这个函数可以把json对象转换为json字符串，这三个设置是必不可少的。