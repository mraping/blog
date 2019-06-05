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