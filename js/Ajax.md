# Ajax

## 请求步骤

1. 创建一个异步请求对象

  `let xhr = new XMLHttpRequest();`
2. 配置请求参数 (第三个参数为是否异步.默认为true )

  `xhr.open('get', 'http://localhost/phpbase/goodlist.json', true);` 第一个参数是类型,第二个参数是url,第三个参数是是否异步
3. 发起请求
  `xhr.send(); 有可选参数,用于Post请求,发送给服务器的字符串数据
4. 响应　(此事件一般在新建请求对象之后马上绑定)
  readyState == 0|1|2|3|4; 0表示还没open(),1已经调用open却没调用send,2表示已经调用send,但是还没受到数据,3已经收到部分的数据,4已经受到完整数据

```javascript
xhr.onreadystatechange = function (){
  //status 状态码,200 表示请求成功, 304 代表使用缓存 400 代表服务器不识别,401代表请求需要用户认证,404 代表请求地址不存在,500 代表服务器出错或者无响应,503 服务器过载,请求时间过长.
  if(xhr.readyState == 4 && xhr.status == 200){
    //解析数据
    // 返回一串字符串
    let data = xhr.responseText;
  }
};
```

## Get请求

```js
function ajax (url, data) {
    let args = [];
    for (let key in data) {
        // 参数需要转义
      args.push(`${encodeURIComponent(key)}=${encodeURIComponent(data[key])}`);
    }
    let search = args.join("&");
    // 判断当前url是否已有参数
    url += ~url.indexOf("?") ? `&${search}` : `?${search}`;
    let xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.send();
}
```

## post请求

  原生请求默认的Content-Type:的类型是 text/plain;

  jQuery 的默认Content-Type 是 application/x-www-form-urlencoded
  它是一种最常用的一种请求编码方式，支持GET/POST等方法，特点是：所有的数据变成键值对的形式key1=value1&key2=value2的形式，并且特殊字符需要转义成utf-8编号，如空格会变成%20：

  如果服务器要求是要使用 json 格式作为参数 那么就要设置Content-Type 为 application/json

## XMLHttpRequest对象属性方法

- 事件 onreadystatechange ,可用于监听请求过程
- 事件 upload ,可用于监听上传过程
- 属性 readystate 详情看步骤
- 属性 responseText 属性,类型为字符串,返回相应结果
- 属性 status 状态码,详情看步骤
- 方法 setRequestHeader(key,val):设置请求头
  - 设置POST 请求的提交数据格式
- 方法 getAllResponseHeaders()
- 方法 getResponseHeaders()

### 相应属性

.responseXML

### 进度检测

- req.addEventListener("progress", updateProgress, false);
- req.addEventListener("load", transferComplete, false);
- req.addEventListener("error", transferFailed, false);
- req.addEventListener("abort", transferCanceled, false);

**注意** 事件监听要在open之前注册

progress 事件被指定由 updateProgress() 函数处理，并接收到传输的总字节数和已经传输的字节数，它们分别在事件对象的 total 和 loaded 属性里。但是如果 lengthComputable 属性的值是 false，那么意味着总字节数是未知并且 total 的值为零。

以上是下载的时间,以下是上传的时间

- req.upload.addEventListener("progress", updateProgress);
- req.upload.addEventListener("load", transferComplete);
- req.upload.addEventListener("error", transferFailed);
- req.upload.addEventListener("abort", transferCanceled);

**注意** 注意 progress 在使用file: 协议的时候是无效的

以下是 3 种加载都可以监听的事件(abort、load、error)

`req.addEventListener("loadend", loadEnd, false);`

## 提交表单,和上传文件

- 使用 Ajax
- 使用FormData API

这是第二种,快捷简单.

```js
let formData = new FormData();
formData.append("id", 5); // 数字5会被立即转换成字符串 "5"
formData.append("name", "#yin");
// formData.append("file", input.files[0]);
let xhr = new XMLHttpRequest();
xhr.open("POST", "/add", true); 
xhr.send(formData);

**using pure Ajax upload**


```

## 长连接(WebSocket, EventSource)

`var evtSource = new EventSource("ssedemo.php");` 从服务器接受事件.

## 跨域请求

- 同源(同域)(同源策略)
  - 域名,协议,端口三者一致才能访问,js不允许跨域访问,默认同域操作,否则报错;
- JSONP(跨域请求(get))
  - 原理:利用script标签可以跨域访问的特性,配合服务器实现,请求代码执行;
    1.
- cors(cross-origin resource sharing)跨域资源共享(ie10以下不支持)
  - 在服务器设置头()
    ```php
    header('Access-Conrtol-Allow-Origin:' . 'a.com');
    header('Access-Conrtol-Allow-Origin:' . '*');
    header('Access-Control-Allow-Methods:POST');
    header('Access-Control-Allow-Headers:x-requested-with,content-type');
    ```
- 服务器代理
  - 后端不存在跨域问题,所以可以利用后端间接获取其他网站的数据

## Promise(es5)

- 注意 如果对象中有promise对象,该对象会变为该promise对象
- 定义

  ```JavaScript
    //参数是一个函数,该函数接受两个函数参数,一个名为resolve(成功过时候执行),一个名为reject(失败的时候调用),一般用于异步操作.
    let promise = new Promise((resolve,reject)=>{

    });
  ```

- 三种状态
  - Pending等待,挂起,即promise对象创建出来并没有执行操作;
  - Resolved 操作成功
  - Rejected 操作失败
  - promise只能从Pending状态到Resolved状态或者从Pending到Rejected状态;不能反向和互连
- 静态方法
  - Promise.resolve():

  ```javascript
    //返回一个resolved状态的Promise对象
    let promise = Promise.resolve('foo');
    //上面代码可以直接promise.then((foo)=>{.....})
    // 相当于
    let promise = new Promise((resolve)=>{
      return resolve('foo');
    });
  ```

- Promise.reject();返回一个新的Promise,该对象的状态为rejected
- Promise.all([p1,p2,p3,p4...]);把多个promise对象封装成一个对象,当所有的promise对象准备好以后,才会执行他调用的方法;

  ```javascript
    let promise1 = new Promise((relolve,reject)=>{});
    let promise2 = new Promise((relolve,reject)=>{});
    let promise3 = new Promise((relolve,reject)=>{});
    let promise4 = new Promise((relolve,reject)=>{});
    Promise([promise1,promise2,promise3,promise4]).then();
  ```

- Promise.race(arr);接受一个promise对象数组,只会执行之完成最快的对象
- promise.then([,resolve,reject]);连续调用时接受上参数是上一个then()的return 值;
- promise.catch(()=>{}); 错误处理函数.

- 封装

```js
function get(url){
  return new Promise((resolve,reject)=>{
    var xhr = new XMLHttpRequest();
    xhr.onload = function(){
      if (xhr.status == 200) {
        resolve();
      }else {
        reject(Error(xhr.statusText));
      }

    }
    xhr.open('GET',url);
    xhr.send();
  });
}
function getJson(url){

  return get(url).then(JSON.parse);
}
// 配合yeied使用更佳
```
