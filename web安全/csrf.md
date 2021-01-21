### 前端安全系列 - CSRF（跨站请求伪造）

攻击流程：
1. 用户访问目标网站；
2. 诱导用户访问第三方网站；
3. 第三方网站向目标网站发送了一个请求，浏览器会默认携带目标网站的`Cookie`；
4. 目标网站接收到请求后，对请求验证成功，以为是用户发送的请求；
5. 用户在不知情的情况下，让目标网站执行了第三方网站定义的操作；

#### Get类型
通过URL拼接参数定义在目标网站的操作

#### Post类型

页面包含一个自动提交的表单，访问该页面后，页面会自动提交，相当于模拟用户进行了一次`POST`；

#### 链接类型
本域的；
在目标网站发布的内容比如图片中嵌入恶意链接，诱导用户主动去点击；

#### 防护策略
* 阻止不明外域访问
	* 同源检测
	* `Samesite Cookie`
* 提交时要求只有本域才能附加的信息
	* `CSRF token`
	* 双重`Cookie`验证

##### 同源检测

`Origin header` 和 `Referer header`;
`Origin header`在IE跨域请求以及302重定向的请求中不会被带上；
`Referrer Policy`的策略设置成`same origin`，对于同源的链接和引用，会发送`Referer`，`Referer`值为`Host`不带`Path`，跨域访问则不携带`Referer`；

设置`Referrer Policy`
1. 在`CSP`设置；
2. 在`meta`标签中设置；
3. 在`a`标签中添加`referrerpolicy`属性

##### Same-site
响应头的`set-cookie`被用来由服务端向客户端发送`Cookie`；

`set-cookie`的值：
* `HttpOnly`不能使用`Document.cookie`、`xhr`、`Request APIs`进行访问，以防范`XSS`攻击；
* `Samesite=Strict`：`Cookie`绝不可能作为第三方的`Cookie`；
* `Samesite=Lax`：假如这个请求改变了当前页面或者打开了一个新页面，并且同时是个`get`请求，则这个`Cookie`可以作为第三方`Cookie`；


#### 双重Cookie验证
