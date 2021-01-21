### TerserWebpackPlugin

#### `Terser`
[Terser](https://github.com/terser/terser#compress-options)是`ES6+`的`JavaScript`解析器和`mangler/compressor`工具包；
**为什么选择Terser？**因为`uglify-es`不再维护了而且`uglify-js`也不支持ES6+；`terser`是`uglify-es`的一个分支，保留了其大部分API；

```
const TerserPlugin = require('terser-webpack-plugin')

module.exports = {
	optimization:{
		minimize:true,
		minimizer:[new TerserPlugin()]
	}
}
```

#### Options
##### `test`
 **Type**：`String|RegExp|Array<String\Regexp>`
 **default**：`/\.m?js(\?.*)?$/i`

##### `include`
 **Type**：`String|RegExp|Array<String\Regexp>`
 **default**：`undefined`

##### `exclude`
 **Type**：`String|RegExp|Array<String\Regexp>`
 **default**：`undefined`

##### `chunkFilter`
**Type** `Function<(chunk) -> boolean>`
**Default** `() => true`
默认情况下所有的js文件都会被压缩，该配置项用来过滤应该被压缩的`chunks`，返回`true`就压缩，`false`则不压缩；

#####  `cache`
**Type** `Boolean|String`
**Default** `true`
是否缓存，`String`可以设置缓存路径，默认为`node_modules/.cache/terser-webpack-plugin`

##### `cacheKeys`
**Type** `Function<(defaultCachekeys, file) -> Object>`
**Default** `defaultCachekeys => defaultCachekeys`

???

##### `parallel`
**Type** `Boolean|Number`
**Default** `false`
使用多进程并行压缩提成构建速度，默认并行数为CPU内核数；

##### `sourceMap`
**Type** `Boolean`
**Default** `false`

##### `minify`
**Type** `Function`
**Default** `undefined`
默认情况下使用`terser`压缩，该配置项可以覆盖默认的压缩函数；

`terserOptions`
**Type** `Object`
**Default** `default`
`Terser`默认配置，
```
module.exports = {
	optimization:{
		minimize; true,
		minimizer: [
			{
				new Terserplugin({
				terserOptions:{
					ecma: undefined,
					warnings: false,
					parse: {},
					compress: {},
					mangle: true,
					module: false,
					output: null,
					toplevel: false,
					nameCache: null,
					ie8: false,
					keep_classnames: undefined,
					keep_fnames: false,
					safari10: false
					}
				})
			}
		]
	}
}
```

##### `extractComments`
**Type** `Boolean|String|RegExp|Function<(node, comment) -> Boolean|Object|Object`
**Default** `true`
`Boolean`表示**使用/禁用**提取注释功能；
`String`仅提取匹配到的注释；
`RegExp`同样是提取匹配的注释到独立文件中；
`Function<(node, comment) -> Boolean>` 返回`true`就抽离，返回`false`则保留；
`object`允许自定义
*  `condition` 
**Type** `Boolean|String|RegExp|Function<(node, comment) -> Boolean|Object>`
用于匹配什么样的注释需要抽离
* `filename`
**Type** `String|Function<(String) -> String>`
**Default** `[file].LICENSE.txt[query]`
有效的占位符有`[file]`，`[query]`，`[filebase]`
* `banner`
**Type** `Boolean|String|Function<(String) -> String>`
**Default** `/*! For license information please see ${commentsFile} */`
`banner`的意思是抽取出来的注释文件会被添加到源文件的顶部；可以为`false`，即`no banner`，

##### `warningsFilter`
**type** `Function<(warning, source, file) -> Boolean>`
**Default** `() => true`
筛选`terser`警告；