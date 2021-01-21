##### `splitChunks.automaticNameDDelimiter`
@(string) 默认情况下，`webpack`会使用文件原来的名字生成`chunk name`，该配置可以让你具体说明定界符去生成名称；

##### `splitChunks.automaticNameMaxLength`
@(number = 109) 允许为生成的`chunk name`设置一个最大长度；

###### `splitChunks.chunks`
@(function(chunk)|string)[all|async|initial] 该配置表示匹配那种类型的chunk；`all`表示包括初始化资源以及动态加载的资源，`async`代表只匹配动态加载chunks，`initial`代表只匹配初始化资源；

##### `splitChunks.maxAsyncRequests`
@(number) 按需加载的并行请求次数最大值

##### `splitChunks.maxInitialRequests`
@(number)单入口并行起请求最大值

##### `splitChunks.minChunks`
@(number) 使用的最小次数

##### `splitChunks.minSize`
@(number) 进行分包的最小体积

##### `splitChunks.minRemainingSize` `splitChunks.cacheGroups.{cacheGroup}.minRemainingSize`
@(number) 该配置的设置是为了避免分包之后最后剩余的包是0体积；开发环境下默认是`0`。其他情况下该配置的值默认为`splitChunks.minSize`的值；

##### `splitChunks.maxSize`
@(number) 该配置项告诉`webpack`将体积大于`maxSize`的包进行分包，且分包的大小不能小于`minSize`。

##### `splitChunks.maxAsyncSize`
@(number) 类似于`maxSize`，但是`maxAsyncSize`只会影响`on-demand`加载的块；

##### `splitChunks.maxInitialSize`
@(number) 类似于`minSize`，`maxInitialSize`只作用于`initial load chunks`；

##### `splitChunks.name`
@(boolean = true | function(module, chunks, cacheGroupKey) => string | string) 分包名称；
值为`true`时会基于`cacheGroupsKey`自动生成名称；
值为`string`或`function`时可以自定义名称；如果为不同chunks指定了相同的名称，则会将这些chunks打包成一个文件，这会导致文件体积过大影响页面加载速度；
如果选择`function`去生成`name`，`function`中的参数`chunks`是一个`array`，每一个`chunk`都具有`chunk.name`和`chunk.hash`属性，可以使用这些属性生成名称；
如果`splitChunks.name`与一个`entry point name`匹配，这个`entry point`将会被移除？
> 推荐在生产环境下将`splitChunks.name`设置成`false`以便避免不必要的姓名更改

##### `splitChunks.automaticNamePrefix`
@(string = ‘‘ ) 设置`chunks`名称的前缀

##### `splitChunks.cacheGroups`
`Cache groups`可以继承或者覆盖`splitChunks.*`的配置；但是`test`、`priority`、`reuseExistingChunk`只能在`cache group`中设置；
To disable any of the default cache groups , set them to false;

##### `splitChunks.cacheGroups.{cacheGroup}.priority`
@(number) 一个模块可能同时属于多个`cache group`，应该被打包到哪个`chunk`中呢？`priority`代表优先级，模块会被打包到优先级最高的那一组；

##### `splitChunks.cacheGroups.{cacheGroup}.reuseExistingChunk`
@(boolean) 

##### `splitChunks.cacheGroups.{cacheGroup}.type`
@(function | RegExp | string)

##### `splitChunks.cacheGroups.test` `splitChunks.cacheGroups.{cacheGroups}.test`
@(function(moudle, chunk) => boolean | RegExp | string) 该配置项匹配模块，它可以匹配绝对路径或者模块名称；当匹配到一个模块名称时，这个模块下所有的文件都会被选择；

##### `splitChunks.cacheGroups.{cacheGroup}.filename`
@(string | function (chunkData): string) 该配置项会使`inintial chunk`名称改变；

##### `splitChunks.cacheGroups.{cacheGroups}.enforce`
@(boolean = false) 该配置项告诉`webpack`忽略掉`splitChunks.minSize`，`splitChunks.minChunks`·，`splitChunks.maxAsyncRequests`和`splitChunks.maxInitialRequests`并且总是为该组生成`chunks`；

##### `splitChunks.cacheGroups.{cacheGroup}.idHint`
@(string) 设置`chunk id`，会被添加到`chunk's filename`中；
