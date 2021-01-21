##### Performance

`performance`接口可以获取到当前页面中与性能相关的信息；

`window.performance`获取该类型对象

* `navigation` 对象提供了在指定的时间段里发生的操作相关信息，包括页面是加载还是刷新、发生了多少次重定向而已；在`web workers`中不可用；
* `timing` 对象里存储了各种与浏览器性能有关的时间数据；
	* `dns`dns查询时间 `timing.domainLookupEnd - timing.domainLookupStart`；
	* `dom`解析DOM树时间`timing.domComplete - timing.domInteractive`；
	* `redirect`重定向时间 `timing.redirectEnd - timing.redirectStart`；
	* `request`请求耗时 `timing.responseEnd - timing.responseStart`；
	* `wait`白屏时间`timing.responseStart- timing.navigationStart`;
	* 总下载时间 `timing.loadEventEnd - timing.navigationStart`
* `memory` 提供里一个可以获取到基本内存使用情况的对象，是一个`Chrome`添加的一个非标准扩展；
* `timeOrigin` 返回性能测量开始的时间的高精度时间戳；

`onresourcetimingbufferfull`回调


##### 方法
* `mark`根据`name`值，在浏览器的性能输入缓冲区中创建一个相关的`timestamp`；
* `measure`在浏览器指定的`start mark`和`end mark`间的性能输入缓冲区中创建一个指定的`timestamp`；
* `clearMarks`将给定的mark从浏览器的性能输入缓冲区中移除；
* `clearMeasures`将给定的`measure`从浏览器的性能输入缓冲区中移除；
* `clearResourceTimings`从浏览器的性能数据缓冲区中移除所有`entryType`是`resource`的`performance entries`；
* `getEntries`基于给定的`filter`返回一个`PerformanceEntry`对象的列表；
* `getEntriesByName`基于给定的`name`和`entry type`返回一个`PerformanceEntry`对象的列表；
* `getEntriesBtType`基于给定的`entry type`返回一个`PerformanceEntry`对象的列表；
* `now`返回一个表示从性能测量时刻开始经过的毫秒数`DOMHIGHResTimeStamp`；
* `toJSON`返回`Performance`对象的`JSON`对象；


##### PerformanceObserver
> `PerformanceObserver`构造函数给给定的观察者`callback`生成一个新的`PerformanceObserver`对象；

```
var observer = new PerformanceObserver(callback)
```
在触发`callback`回调时，会接收两个参数，第一个是**性能观察条目列表**，第二个是**观察者**对象；







`lighthouse`从5个方面来给网页打分，Performance、Accessibility、Best Practices、SEO、PWA，


###### Performance

- `First Contentful Paint`内容首次开始绘制
- `First Meaningful Paing`用户看到网页主要内容的时间；
- `Speed Index`明显填充页面内容的速度，是一个页面加载性能指标；
- `First CPU Idle`首次CPU空闲时间
- `Time to Interactive`页面可以交互的时间，页面中大多数网络资源加载完成并且CPU在很长一段时间都很空闲的所需时间，此时预期CPU非常空闲，可以及时响应用户操作；
- `Max Potential First Input Delay`最大输入延迟时间，超过100ms响应时间，用户就会认为应用反应迟缓；


##### lighthouse CLI 命令

-- emulated-form-factor 设置模拟设备`mobile`、`desktop`



#### 采购系统优化策略

dist总体积4.4MB
用star策略描述为什么要做这件事情，实施过程是怎么样的，遇到了什么困难，最后如何解决？最后取得了什么样的成效？在大量用户的环境下，会遇到什么样的问题？

从两方面着手：内存、初始化，加载中，运行时

通过`lighthouse`给网站评分，发现的问题；
> 发现在不同的网络环境下`lighthouse`给出的评分竟然差了很多，并不知道为什么，但是在评分最高的情况下首屏渲染时长（这里指`First Meaningful Paing`）也超出了5s；
##### 1. 首屏渲染慢
1. 对于一些不改变页面内容的脚本延迟加载，比如平台项目中的阿里云OSS链接；
2. `Serve static assets with an efficient cache policy`---服务器资源配置有效的缓存策略：`lighthouse`会标出所有未设置缓存策略的静态资源，此处有：
  - chunk-vendor.hash.js
  - chunk-vendor.hash.css
  - app.hash.js
  - app.hash.css
  - aliyun-oss-sdk-4.4.4.min.js
  - logo.hash.png
  - background.hash.svg 
4. `removed unused css`
	- chunk-vendors.css
5. `Properly size images`
	- logo.images
6. `Serve images in next-gen formats`
	
7. `Minimize main-thread work`
	在加载过程中，保持主线程忙碌4s中以上，`lighthouse`就认为`fail`;
	文档给出的优化策略：
	- 只加载用户用到的代码通过分包；
	- 压缩代码；
	- 移除无用代码；
	- 使用PRPL模式缓存代码
8. `Reduce JavaScirpt execution time`
3. `keep request counts low and transfer sizes small` --- 

以上是`lighthouse`给出的优化建议，在做完这些优化后，发现首屏加载时间仍有6.2s，这个时间还是很长，我们一般网站的首屏加载时间都会控制在5s以内；

通过`webpack-bundle-analyzer`分析打包后文件，发现`common-vendor.js`包巨大（这是理所当然的），里面占比最大的有`lodash`（分块引入了），`g2.js`(并没有引用到)，`element-ui\lib`（1.4MB），`video.cs.js`(并没有引用到)；

`lodash`是一个很强大的js工具库，但是在我的项目中只用到了`isplainobject`这样一个方法，并没有大面积的使用到，`lodash`官方提供引用具体方法，也提供用npm的方式安装某个具体方法：
```
var chunk = require('lodash/array/chunk');
```
1. **将基础库与工具库分开打包**：
	在我这个系统中用到的基础库有：`vue`全家桶 + `axios`，`element-ui`，可以把这些单独打包在一个chunk-basic文件里初始化加载，其余在用户交互过程中用到的工具库可以打包成独立文件然后懒加载；
```
config.optimization.splitChunks.cacheGroups = {
        basic: {
          name: 'chunk-basic',
          test: /[\\\/]node_modules[\\\/](vue|vuex|vue-router|element-ui|axios)/,
          priority: -10,
          chunks: 'initial',
        },
        vendors: {
          name: 'chunk-vendors',
          test: function (module, chunk) {
            // 好像js不能反向匹配，所以采用这种方法匹配工具库
            const reg1 = /[\\\/]node_modules[\\\/](vue|vuex|vue-router|element-ui|axios)/
            const reg2 = /[\\\/]node_modules[\\\/]/
            if (!module.context) console.log('这个module没有context属性')
            if (reg2.test(module.context) && !reg1.test(module.context)) {
              return true
            }
          },
          priority: 10,
          chunks: 'initial',
          reuseExistingChunk: true,
        }
      }
```
> Prefetch 链接将会消耗带宽。如果你的应用很大且有很多 async chunk，而用户主要使用的是对带宽较敏感的移动端，那么你可能需要关掉 prefetch 链接并手动选择要提前获取的代码区块。

2. **不要滥用prefetch和preload**
	直接使用VUE-CLI打包的项目，会将所有异步加载资源打上`prefetch`标签，`prefetch`允许浏览器在**后台空闲时**获取将来可能用到的资源，并将他们存储在浏览器的缓存中；
	
对于某些站点来说，提前获取资源可能会**导致数据过期失效**，并且过早的`prefetch`会**降低页面加载速度**；

更重要的是，这样就做不到路由文件**按需加载**了！
```
// vue-cli默认配置
new PreloadPlugin(
 {
    rel: 'preload',
    include: 'initial', // 初始化文件会被预加载
    fileBlacklist: [
      /\.map$/,
      /hot-update\.js$/
    ]
  }
),
/* config.plugin('prefetch') */
new PreloadPlugin(
  {
    rel: 'prefetch',
    include: 'asyncChunks'
  }
),
// 修改后配置
config.plugin('preload').tap(options => {
 options[0].fileBlacklist.push(/chunk-vendors/)
  return options
})
config.plugins.delete('prefetch')
```
3.  在把初始代码进行分包后，终于成功的把首页渲染时间降到了3.6s，但是使用`lighthouse`分析之后仍然是一片红，没有一点绿色；在给出的优化提示里有这么一条`Eliminate render-blocking resources - css/chunk-basic.hash.css`，即这个css文件阻塞了渲染；
	
	我们都知道CSS是阻塞渲染资源，它们会阻塞DOM的渲染但不会阻塞DOM树的构建，在DOM渲染过程中，遇到CSS资源就会下载并执行，在此之前页面不会继续渲染，但是DOM树会继续构建；

像这样的单页面应用，首页会加载大量的CSS资源但其实在首页并没有使用到的，虽然首页的DOM结构可能很简单，但是由于需要解析大量的CSS，也依然会使得页面渲染时间变长；

解决方法有三种：
1. 媒体查询
2. preload
```
<style>
[Your critical CSS goes here]
</style>


<!– Reliably defer loading of non-critical CSS with loadCSS–>
<link rel=“preload” href=”[yourcssfile]” as=“style” onload=“this.onload=null; this.rel=‘stylesheet’”>
<noscript><link rel=“stylesheet” href=”[yourcssfile]” ></noscript>



<!– Inline the loadCSS preload polyfill code or load it via external JS file–>
<script src=“./cssrelpreload.js”></script>
```

4.  针对图片应用http2

解决多个浏览器tab登录导致串用户的问题，实现长列表渲染；
采用代码分割打包，css外链文件preload等方式优化首页渲染时间；
修改项目webpack配置，优化打包速度和体积；