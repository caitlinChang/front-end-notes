##### 浏览器缓存--indexedDB
前端存储的发展历史
`cookie` -> `web Storage` -> `web sql` -> `indexedDB`

* `cookie`的存储大小只有4k，而且每次请求都会带上这个token；
* `web Storage`的存储空间有5M，而且`local storage`可以永久存储，缺点在于只能存储`string`类型的数据；
* `web SQL`关系型数据库，使用起来比较复杂；
* `indexedDB`可以存储大量的数据，具有查询能力，可以离线使用，而且可以存储多种类型的数据；`indexedDB`在浏览器的隐私模式下是被完全禁止的；

`indexedDB`就是一个基于事务操作的`key-value`型的前端数据，API大多是异步；

##### 特点

1. `indexedDB`数据库使用`key-value`键值对存储数据；
2. `indexedDB`是事务模型的数据库；所有操作都依赖事务，当不同tab打开相同web应用实例时，如果没有事务操作的支持，这两个实例就会互相影响；
3. `indexedDB`API基本上是异步的；
4. `indexedDB`是面向对象的；
5. `indexedDB`不使用结构化查询语言；它通过索引`index`所产生的指针来完成查询操作；
6. `indexedDB`遵循同源策略；

##### 名次解释

* 事务`transaction`
	> 在一个特定的数据库上，一组具备原子性和持久性的数据访问和数据修改操作；任何对于数据库的读和修改操作，只能在事务中进行；

* 请求`request`
	在数据库上进行读写操作完成后的操作；每一个请求代表一个读或写操作

* 索引`index`
	一个对象仓库，专门用来查找另一个对象仓库中的记录，其中被查找的对象仓库称为引用对象仓库；
##### 构建数据库

`indexedDB`使用对象存储仓库而不是表；一个单独的数据库可以包含任意数量的对象存储空间；

**indexedDB.open** 创建、更新数据库
**createObjectStore**创建存储空间
**createIndex**创建存储空间的索引

> 只能在`onupgradeneeded`回调函数中创建存储空间，而不能在数据库打开后的`success`回调函数中创建；

##### 事务
事务有三种模式
* `readOnly`只读
* `readwrite`读写
* `versionchange`数据库版本变化

`transaction`事务函数的第一个参数为需要关联的存储空间，第二个可选参数为事务模式，成功时也会触发`onsuccess`函数，失败时也会触发`onerror`函数

###### 查找数据
`get()`方法用于你知道你想要检索哪一个键
`openCursor()`游标可以限制被检索项目的范围，并且可以指定迭代方向；


##### 使用场景




