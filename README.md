AJAX ：异步的 JavaScript 和 XML（Asynchronous JavaScript and XML）。

就是利用JS来无刷新与后端交互，通过get和post方式把数据发送到后端，或者请求后端的数据，然后根据请求的数据进行改变DOM节点等操作，从而取消掉用form的submit方式一提交就会跳转页面的情况，典型的案例：在创建账号的时候检测此用户名是否存在。

原生AJAX：(核心API XMLHttpRequest 对象，老版本IE对应ActiveXObject)

1.GET

var xmlhttp = new XMLHttpRequest();//var xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");

xmlhttp.open("GET","demo_get.php?id=1&name=lemoo&t=" + Math.random(),true);

xmlhttp.send();

传递参数直接在?后指定，多个参数用&分隔；
GET请求同一URL时会有缓存，通过参数是否一致来判断；
解决缓存问题，加个时间戳使每次参数不一致。

2.POST

var xmlhttp = new XMLHttpRequest();

xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");

xmlhttp.onreadystatechange=function(){

  if (xmlhttp.readyState==4 && xmlhttp.status==200){

    document.getElementById("myDiv").innerHTML=xmlhttp.responseText;

    }

  }

xmlhttp.open("POST","ajax_test.php",true);

xmlhttp.send();

POST没有缓存
POST发送的数据量大
AJAX无法发送文件
readyState改变时触发onreadystatechange事件，4为完成
status是返回状态，200是成功，404是未找到页面
responseText是返回的数据，为字符串格式








当 readyState 等于 4 且状态为 200 时，表示响应已就绪：

xmlhttp.onreadystatechange=function(){
  if (xmlhttp.readyState==4 && xmlhttp.status==200){
    document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
    }
  }
 

实例：

function showHint (str) {
var xmlhttp;
if (str.length==0) {
  document.getElementById("txtHint").innerHTML="";
  return;
  }
if (window.XMLHttpRequest) {
// code for IE7+, Firefox, Chrome, Opera, Safari
  xmlhttp=new XMLHttpRequest();
  }
else {
// code for IE6, IE5
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
xmlhttp.onreadystatechange=function() {
  if (xmlhttp.readyState==4 && xmlhttp.status==200) {
    document.getElementById("txtHint").innerHTML=xmlhttp.responseText;
    }
  }
xmlhttp.open("GET","gethint.php?q="+str,true);
xmlhttp.send();
}




源代码解释：

如果输入框为空 (str.length==0)，则该函数清空 txtHint 占位符的内容，并退出函数。

如果输入框不为空，showHint() 函数执行以下任务：

创建 XMLHttpRequest 对象

当服务器响应就绪时执行函数

把请求发送到服务器上的文件

请注意我们向 URL 添加了一个参数 q （带有输入框的内容）

 

当创建了XMLHttpRequest对象后，要先设置onreadystatechange的回调函数。在回调函数中，通常我们只需通过readyState === 4判断请求是否完成，如果已完成，再根据status === 200判断是否是一个成功的响应。

XMLHttpRequest对象的open()方法有3个参数，第一个参数指定是GET还是POST，第二个参数指定URL地址，第三个参数指定是否使用异步，默认是true，所以不用写。

注意，千万不要把第三个参数指定为false，否则浏览器将停止响应，直到AJAX请求完成。如果这个请求耗时10秒，那么10秒内你会发现浏览器处于“假死”状态。

最后调用send()方法才真正发送请求。GET请求不需要参数，POST请求需要把body部分以字符串或者FormData对象传进去。







1.    因为浏览器的同源策略导致默认情况下，JavaScript在发送AJAX请求时，URL的域名必须和当前页面完全一致。（跨域）

2.    跨域方法：

1） 是通过Flash插件发送HTTP请求，这种方式可以绕过浏览器的安全限制，但必须安装Flash，并且跟Flash交互。不过Flash用起来麻烦，而且现在用得也越来越少了。

2） 通过在同源域名下架设一个代理服务器来转发，JavaScript负责把请求发送到代理服务器：'/proxy?url=http://www.sina.com.cn'，代理服务器再把结果返回，这样就遵守了浏览器的同源策略。这种方式麻烦之处在于需要服务器端额外做开发。

3） 第三种方式称为JSONP，它有个限制，只能用GET请求，并且要求返回JavaScript。这种方式跨域实际上是利用了浏览器允许跨域引用JavaScript资源：

<html>
<head>
<script src="http://example.com/abc.js"></script>
 ...
</head>
<body>
...
</body>
</html>
JSONP通常以函数调用的形式返回。

JSONP：Jsonp(JSON with Padding) 是 json 的一种"使用模式"，可以让网页从别的域名（网站）那获取资料，即跨域读取数据。


JSONP 应用
1. 服务端JSONP格式数据
如客户想访问 : http://www.runoob.com/try/ajax/jsonp.php?jsonp=callbackFunction。

假设客户期望返回JSON数据：["customername1","customername2"]。

真正返回到客户端的数据显示为: callbackFunction(["customername1","customername2"])。

服务端文件jsonp.php代码为：

1.1      jsonp.php 文件代码
<?php 
header('Content-type: application/json'); 
//获取回调函数名 
$jsoncallback = htmlspecialchars($_REQUEST ['jsoncallback']);
 //json数据 
$json_data = '["customername1","customername2"]'; 
//输出jsonp格式的数据 
echo $jsoncallback . "(" . $json_data . ")"; 
?>
2． 客户端实现 callbackFunction 函数
<script type="text/javascript"> 
function callbackFunction(result, methodName) {
 var html = '<ul>';
 for(var i = 0; i < result.length; i++) {
 html += '<li>' + result[i] + '</li>';
 }
 html += '</ul>';
 document.getElementById('divCustomers').innerHTML = html;
 }
 </script>
2.1 页面展示
<div id="divCustomers"></div>
2.2 客户端页面完整代码
<!DOCTYPE html>
 <html>
 <head>
 <meta charset="utf-8">
 <title>JSONP 实例</title>
 </head>
 <body>
 <div id="divCustomers"></div>
 <script type="text/javascript">
function callbackFunction(result, methodName) {
 var html = '<ul>';
 for(var i = 0;i < result.length; i++) {
 html += '<li>' + result[i] + '</li>';
 }
 html += '</ul>';
 document.getElementById('divCustomers').innerHTML = html;
 }
</script>
 <script type="text/javascript" src="http://www.runoob.com/try/ajax/jsonp.php?jsoncallback=callbackFunction"></script>
 </body>
 </html>
jQuery 使用 JSONP
        以上代码可以使用 jQuery 代码实例：
<!DOCTYPE html> 
<html> 
<head> 
<meta charset="utf-8"> 
<title>JSONP 实例</title> 
<script src="http://cdn.static.runoob.com/libs/jquery/1.8.3/jquery.js"></script> 
</head> 
<body> 
<div id="divCustomers"></div> 
<script>
$.getJSON("http://www.runoob.com/try/ajax/jsonp.php?jsoncallback=?", function(data) {
 var html = '<ul>'; 
for(var i = 0; i < data.length; i++) {
 html += '<li>' + data[i] + '</li>';
 } 
html += '</ul>'; $('#divCustomers').html(html); 
});
</script> 
</body>
 </html>
 

4） HTML5之CORS，Cross-Origin Resource Sharing，是HTML5规范定义的如何跨域访问资源

 

$.ajax()

执行异步 AJAX 请求

$.ajaxPrefilter()

在每个请求发送之前且被 $.ajax() 处理之前，处理自定义 Ajax 选项或修改已存在选项

$.ajaxSetup()

为将来的 AJAX 请求设置默认值

$.ajaxTransport()

创建处理 Ajax 数据实际传送的对象

$.get()

使用 AJAX 的 HTTP GET 请求从服务器加载数据

$.getJSON()

使用 HTTP GET 请求从服务器加载 JSON 编码的数据

$.getScript()

使用 AJAX 的 HTTP GET 请求从服务器加载并执行 JavaScript

$.param()

创建数组或对象的序列化表示形式（可用于 AJAX 请求的 URL 查询字符串）

$.post()

使用 AJAX 的 HTTP POST 请求从服务器加载数据

ajaxComplete()

规定 AJAX 请求完成时运行的函数

ajaxError()

规定 AJAX 请求失败时运行的函数

ajaxSend()

规定 AJAX 请求发送之前运行的函数

ajaxStart()

规定第一个 AJAX 请求开始时运行的函数

ajaxStop()

规定所有的 AJAX 请求完成时运行的函数

ajaxSuccess()

规定 AJAX 请求成功完成时运行的函数

load()

从服务器加载数据，并把返回的数据放置到指定的元素中

serialize()

编码表单元素集为字符串以便提交

serializeArray()

编码表单元素集为 names 和 values 的数组

1.2      jQuery实现AJAX
1.GET
        使用get()方法时，采用GET方式向服务器请求数据，并通过方法中回调函数的参数返回请求的数据，它的调用格式如下：
$.get(url,[callback])

$.get("demo_test.php?id=1&name=lemoo",function(data,status){
    alert("Data: " + data + "\nStatus: " + status);
  });
·         参数通过URL传递

·         有缓存

2.POST
与get()方法相比，post()方法多用于以POST方式向服务器发送数据，服务器接收到数据之后，进行处理，并将处理结果返回页面，调用格式如下：
$.post(url,[data],[callback])

$.post("demo_test.php",{
  num:1
},
function (data) {
  alert(data);
});
使用serialize()方法可以将表单中有name属性的元素值进行序列化，生成标准URL编码文本字符串，直接可用于ajax请求，它的调用格式如下：
$(selector).serialize()

3.ajax
使用ajax()方法是最底层、功能最强大的请求服务器数据的方法，它不仅可以获取服务器返回的数据，还能向服务器发送请求并传递数值，它的调用格式如下：
$.ajax([settings])
其中参数settings为发送ajax请求时的配置对象，在该对象中，url表示服务器请求的路径，data为请求时传递的数据，dataType为服务器返回的数据类型，success为请求成功的执行的回调函数，type为发送数据请求的方式，默认为get。

$.ajax({
  type:"post",
  url:"demo_test.php",
  data: { num: 123 },
  dataType:"text",
  success: function (data) {
    alert(1);
  }
});
4.getJSON
使用getJSON()方法可以通过Ajax异步请求的方式，获取服务器中的数组，并对获取的数据进行解析，显示在页面中，它的调用格式为：
$.getJSON(url,[data],[callback])
可以与$.each搭配来遍历数据

$.getJSON("demo_test.php",function(data){
  $.each(data, function (index, sport) {
    if(index==3)
      $("ul").append("<li>" + sport["name"] + "</li>");
  });
});
 
