
### 前端安全系列 - XSS（跨站脚本攻击）
> xss攻击指在攻击者在网页中注入恶意脚本，当用户浏览网页时，网页脚本执行；

XSS按攻击方式可以非为三种类型，**反射型xss**、**DOM型xss**、**存储型xss**，其中**反射型xss**和**DOM型xss**是**非持久型xss**，**存储型xss**是**持久型xss**，即攻击行为伴随着攻击数据一直存在；

#### 反射型xss
步骤：
1. 构建含有恶意代码的URL（输入框的输入文本）；
2. 诱导用户打开含有恶意代码的URL，服务端将恶意代码从URL中取出当作参数处理然后返回给浏览器；
3. 浏览器接收到数据后无法分辨其中的恶意代码，直接解析执行；
4. 恶意代码窃取到用户的敏感数据后冒充用户调用目标网站接口；

场景：
搜索，用户搜索数据，服务端返回搜索结果包含用户输入的数据，浏览器解析执行；

####　DOM型xss
> 通过修改页面DOM节点形成的xss

1. 同样是攻击者构造出含恶意代码的URL，诱导用户打开；
2. 用户打开含有恶意代码的URL，前端将恶意代码从URL中取出并执行；
3. 接下来的行为同上；

DOM型与反射型不同的点在于DOM型是取出和执行恶意代码都是由前端执行；

场景：
页面展示数据从URL中取出，比如订单号等等；

#### 存储型xss
> 存储型xss的恶意代码存储在服务器上，存储型xss不需要用户手动触发，当浏览器请求数据时，脚本从服务器传回并执行；
任何允许用户存储数据的web程序都有可能存在存储型xss漏洞；若某个页面遭受存储型xss攻击，所有访问该页面的都用都会遭受到攻击；

1. 攻击者把恶意代码提交到目标网站的服务器中；
2. 用户访问目标网站，网站把含有恶意代码的数据返回给浏览器；
3. 浏览器接收代码解析执行；

场景：
1. 评论，用户输入的评论被服务器存储然后返回给页面；


#### 防御方法
##### x-xss-protection响应头
`x-xss-protection`会检测URL中或者DOM中元素的相关性；只对**反射型xss**有一定的防御能力，但是也不能阻止所有的**反射型xss**，并且只有`IE`、`Chrome`、`Safari`支持；

* `x-xss-protection: 0`禁止XSS过滤；
* `x-xss-protection: 1`启用XSS过滤，如果浏览器检测跨站脚本攻击，就会清除页面中不安全的部分；
* `x-xss-protection: 1; mode=block`启用XSS过滤，如果检测到，就阻止页面加载；
* `x-xss-protection: 1; report=<reporting URL>`启用XSS过滤。 如果检测到跨站脚本攻击，浏览器将清除页面并使用CSP report-uri指令的功能发送违规报告。

##### CSP（内容安全政策）Content Security Policy
> 提供白名单，

1. HTTP响应头设置`Content-Security-Policy`；
2. 使用`meta`标签：
	```
	<meta http-equiv="Content-Security-Policy" content="script-src 'self'; object-src 'none'; style-src cdn.example.org third-party.org; child-src https:">
	```
启用之后，不符合的外部资源就会被禁止加载；

* `Content-Security-Policy-Report-Only`不执行限制选项，只记录违反限制的行为；

http://www.ruanyifeng.com/blog/2016/09/csp.html

##### 转义
前端和后端都需要对用户输入的字符进行转义，主要针对`<`，`>`，`"`，`'`，`&`等特殊字符;
```
function escapeHTML(str) {
    if (!str) return '';
    str = str.replace(/&/g, "&amp;");
    str = str.replace(/</g, "&lt;");
    str = str.replace(/>/g, "&gt;");
    str = str.replace(/"/g, "&quot;");
    str = str.replace(/'/g, "&#39;");
    return str;
};
```

##### 过滤
如果数据是富文本信息，就不能对其进行转义，这时需要设置白名单标记允许的HTML标签及属性；


#### 总结

XSS攻击出现的场景
1. 
2. javascirpt这样的字符串出现在特定的位置，也会引发XSS攻击，比如a标签的href属性；可以通过白名单去过滤；
3. 对于HTML的转义规则是非常复杂的，在不同的情况下要采用不同的转义规则，应该采用业内成功的转义库；

