#### CORS 跨域资源共享
> Cross Origin Resource Sharing

CORS是一个W3C标准，允许浏览器向跨源服务器发送XHR请求；
##### 简单请求
1. 请求方法是GET、POST、HEAD三者之一，并且请求头不能超过以下几种
	 `Accept、Accept-Language，Content-Language，Last-Event-ID，Content-Type`
而且`Content-Type`的值只能是`mutipart/form-data`,  `text-plain`，`application/x-www-form-urlencoded`;
2. 工作方法：对于简单请求，浏览器会在请求头上自动加上`Origin`这个字段，用来说明本次请求来自于哪个源；
	服务器看到了请求头信息，判断`Origin`的值在不在允许请求的范围内，**如果不在许可范围内，服务器就会返回一个正常的HTTP回应**，浏览器发现响应头内不含`Access-Control-Allow-Origin`字段，就会抛出错误；
	如果源在许可范围内，服务器就会返回相应资源，并且响应头里出现了以下几个响应头：
	- **Access-Control-Allow-Origin: * **
	- **Access-Control-Allow-Credentials: true** 为`true`代表允许发送Cookie；
		此处需要注意的一点是，如果要发送Cookie到跨源的服务器，就算是则XHR请求必须要开启`withCredentials`，即设置`xhr.withCredentials = true`，否则，即使服务器允许发送Cookie，浏览器也不会设置发送Cookie；此外，如果要发送Cookie给跨域服务器，则`Access-Control-Allow-Origin`的值不能设置为* ，必须是`Origin`；这是因为虽然请求跨域了，但是Cookie还是遵循同源策略，只有服务器设置的域名的Cookie才可以上传；并且原网页代码中的`document.cookie`也无法读取服务器域名下的Cookie；
	- **Access-Control-Expose-Headers** 可选字段，定义xhr对象的`getResponseHeader()`方法能够获取到的头字段；
##### 非简单请求
非简单请求：不属于简单请求的都是非简单请求；
1.  预检请求
	 对于非简单请求，要先用`OPTIONS`方法发送预检请求，通过预检请求询问服务器是否支持该源访问资源；
	 预检请求的请求头中，除了`Origin`字段还多了另外两个字段：
	 - **Access-Control-Request-Method**：列出浏览器的CORS请求会用到哪些请求方法；
	 - **Access-Control-Request-Headers**:  指定浏览器的CORS请求会额外发送的头信息字段；
	 服务器接收到预检请求后，除了检查`Origin`是否在自己的许可范围内，还要检查请求资源支不支持其它两个请求头字段列出的请求方法和请求头，检查后确认允许请求就可以发出回应；
	 服务器响应的其它CORS头字段如下：
	 - **Access-Control-Allow-Methods**: 必须字段，表明服务器所支持的所有跨域请求方法；这是为了避免多次预检请求；
	 - **Access-Control-Allow-Headers**: 如果预检请求包含了`Access-Control-Request-Headers`，则该响应字段是必须的；该字段表明了服务器所支持的所有跨域请求的请求头；
	 - **Access-Control-Max-Age**: 用来指定本次预检请求的有效期，单位为秒；即允许在这段时间内的跨域请求不再发出预检请求；