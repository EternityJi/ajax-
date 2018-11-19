# ajax

## 概念

ajax不是一门新的技术 是对现有的技术的综合运用
本质上是在HTTP协议的基础上以异步的方式与服务器进行通信

## http协议

HTTP协议  是超文本传输协议  是一种规定了浏览器和服务器之间互相通信的规则
HTTP协议规定了请求和响应的标准

get请求报文：
(响应与响应报文 ) 
200 成功  304读缓存 404找不到资源 500服务端错误

请求行:在请求行中  加请求路径+参数
请求头:
请求体:get没有请求体  但是参数会拼接到请求行中

post请求报文：
内容类型:表单数据,如果是post请求  必须指定这个属性
Content-Type:application/x-www-form-urlencoded
请求行 请求头 请求体

GET请求与POST请求的对比
GET请求没有请求体   因为请求的参数  拼接到地址栏中了
POST有请求体   就是传递的参数

## Ajax



## XMLHttpRequest对象

- get请求,设置请求行时,需要把参数列表拼接到url后面
- get请求不用设置请求头, 不用说明请求主体的编码方式
- get请求的请求体为null


- post请求, 设置请求行时, 不拼接参数列表
- post必须设置请求头中的content-type为application/x-www-form-urlencoded, 标记请求体解析方式
- post 请求需要将参数列表设置到请求体中

## 获取响应

readyState记录了XMLHttpRequest对象的当前状态
xhr.readyState = 0;  尚未调用 
xhr.readyState = 1;  已经调用
xhr.readyState = 2； 接到头部信息
xhr.readyState = 3;  接收到响应主体
xhr.readyState = 4； DONE响应完成

## day02

## xml

xml是一种标记语言  很类似html
xml的设计宗旨是传输数据  而非显示数据
xml标签没有被预定义 需要自行定义标签

## 语法规范

## php获取xml文件的内容

如果需要返回xml数据 需要将头部的content-type 改成text/xml   不然浏览器以text/html解析

header("content-type:text/xml;charset=utf-8")
file_get_content 用于获取文件的内容

## js解析xml

缺点:虽然描述和传输复杂数据 但是其解析过于复杂 并且体积较大  项目中很少使用

## json数据

  json是一种轻量级数据交换格式
  基于ECMAScript规范   采用于独立于编程语言的文本格式来存储和表示数据

  特点: 数据存储在键值对中
        数据由逗号分隔
        花括号保存对象  方括号保存数组
        键使用双引号

## php处理json

```php
json_encode 将对象转换成字符串
json_decode 将字符串转换成对像
$obj = json_decode($json,true)
//false 将json转换成对象(默认)
//true  将json转换成数组(推荐)
  
//通过json文件获取到的内容就是一个json字符串
$data = file_get_contents("data.json")
  
//将json转换成数组
 $result  = json_decode($dara,true);
print_r($result)
  
```

## js处理json

- js 对象  ==>json 字符串 JSON.stringify(obj)
- 反过来  JSON.parse(str);  json字符串=》js对象

```js
var obj = { a: 'Hello', b:'World'}
//将一个js对象转换成字符串
$result = JSON.stringify(obj);
```

​	

```
ajax原理:
//1.创建xhr对象
//2.设置请求行
//3.设置请求头
//4.设置请求体
//5.监听响应状态
//6.获取响应内容
```

```js
$.ajax({
    type:"get",//请求类型
    url:"02.php",//请求地址
    data:{name:"zs",age:18},//请求数据
  dataType:"json",//希望接受的数据类型
  timeout:5000,//设置超时时间
    //发送ajax之前会调用的函数
  //如果这个函数返回false (注意：必须返回false) 这次请求就不发送了
  //一般 beforeSend 中用于表单校验
     beforeSend：function(){
        //alert("发送前调用");   
     },
  success:function(data){
       //成功时调用
       console.log(data);
  },
  error:function(error){
    //失败时调用
      console.log(error)
  },
  //不管成功还是失败  都会调用这个函数
  complete:function(){
       //请求完成时调用
  }
});
```

​	

- 一些ajax的参数

```js
$.ajax({
     type:"get/post",
    url:"a.php",
    dataType:"json",
    data:{name:"zs",age:18},
    beforeSend:function(){
      
    },
  success:function(data){
    log(data);
  },
  error:function(error){
      log(error);
  }
  
})
```



# day03

## ajax的函数封装

```js
//ajax的函数封装
 get.onclick = function(){
      ajax({
        url: '02-xml/xml.php',
        data:{
          name: 'ls',
          age: 18
        },
        success: function(res){
          console.log(res);
        },
        error: function(){

        }
      });
    }

    post.onclick = function(){
      ajax({
        type: 'post',
        url: '07-ajaxfn.php',
        params: 'name=ls'
      });
    }
  
  
    //可以发送get,也可以发送post,参数是以对象的形式传递进来的
    //封装函数, 
    // 请求类型 type, 
    // 路径 url, 
    // 参数不能写死params
    // 成功的回调函数 success
    // 失败的回调函数  error
    // 是否异步 async true 异步 false 同步
    function ajax(options){

      //如果不传参数/ 传进来的不是对象,则函数直接return
      if(!options || typeof(options) !== 'object'){
          return;
      }

      var url = options.url;
       //如果不传参数,默认是get请求
      var type = options.type === 'post' ? 'post' : 'get';
      //由于传入参数的方式发生变化,传的是对象,所以需要把对象转成键值对形式的字符串
      var params = getParams(options.data);
      var async = options.async === false ? false : true;
       
       

      //如果不传url, 则结束函数
      if(!url){
        //结束函数
        return;
      }

      //1. 创建对象
      var xhr = new XMLHttpRequest();
      // 2 设置请求行
  
      //先判断是否是get请求,如果是拼接参数
      if(type === 'get'){
        //要在url中拼接参数
        url += '?' + params;
        params = null;
      }
      xhr.open(type, url, async);

      //判断是否是post请求,如果是这是请求头,不是则不做
      if(type === 'post'){
        xhr.setRequestHeader('content-type','application/x-www-form-urlencoded');
      }

      xhr.send(params);

      
      //完成相应部分
      xhr.onreadystatechange = function(){
        //判断响应是否完成
        if(xhr.readyState == 4){

          //判断是否响应成功
          if(xhr.status == 200){
            //成功
            //判断传了才调用,没传不调用

            //获取服务器返回的响应头中的content-type 
              var cType = xhr.getResponseHeader('content-type');
              console.log(cType);
            // 根据content-type的信息,决定如何处理数据,
            var result;
            if(cType.indexOf('xml') != -1){
              // 证明返回的是xml
              // console.log('xml');
              result = xhr.responseXML; 
            }else if(cType.indexOf('json') != -1){
              //证明是json
              // console.log('json');
              //转成对象之后,在传给成功的回调函数
              result = JSON.parse(xhr.responseText);

            }else{
              //证明是普通的文本字符串
              // console.log('txt');
              result = xhr.responseText;
            }
            // 最后把数据传递到成功的回调函数中
           
            options.success && options.success(result);
          }else{
            //失败
            options.error && options.error();
          }

        }

      }
      
    }
    

    //封装函数,把一个对象拼接成一个键值对的字符串
    function getParams(obj){
      if(!obj){
        return;
      }

      //拼接字符串
      // var str = '';
      var arr = [];
      for(var k in obj){
        // str += k + '=' + obj[k] + '&';
        arr.push(k + '=' + obj[k]);
      }

      return arr.join('&');

      // console.log(arr.join('&'));



    }

    // getParams({
    //       name: 'ls',
    //       age: 18
    //     });


//数组的方法
//indexOf()  获取指定元素第一次出现的位置  如果没有的话就返回-1  有的话就返回值是出现的位置的下标
//lastIndexOf() 从后面开始查找第一次出现的位置, 如果没有就返回就返回-1,有的话就返回值是出现的位置的下标


```

## 接口化开发

```
请求地址即所谓的接口，通常我们所说的接口化开发，其实是指一个接口对应一个功能， 并且严格约束了请求参数 和响应结果 的格式，这样前后端在开发过程中，可以减少不必要的讨论， 从而并行开发，可以极大的提升开发效率，另外一个好处，当网站进行改版后，服务端接口进行调整时，并不影响到前端的功能。
//总结:方便于前后台数据交互  前台提供一些参数  后台写一些接口 方便前台从后台拿数据
```



###  获取短信验证码

    ​```js
总需求：点击获取验证码按钮，向服务端发送请求, 调用服务器端短信接口, 服务器端根据传参, 调用第三方短信接口, 给手机发送验证码

    ​```

### 模板引擎 

artTemplate的使用

```
1.引入包
2.创建模板
3.用ajax获取数据
4.var html = template("myTmp", json);
5.添加元素。
```




