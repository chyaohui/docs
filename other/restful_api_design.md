> `RESTful` 是目前最流行的 API 设计规范，用于 web 数据接口的设计。它的大原则容易把握，但是细节不容易作对。本文总结如何设计出易于理解和使用的 API。

## 一、URL 设计
### 1、动词 + 宾语
RESTful 的核心思想就是，客户端发出的数据操作指令都是 `动词 + 宾语` 的结构。比如，`GET /articles` 这个命令，GET 是动词，/articles 是宾语。动词通常就是五种 HTTP 方法，对应 CRUD 操作。
* GET：读取(Read)
* POST：新建(Create)
* PUT：更新(Update)
* PATCH：更新(Update)，通常是部分更新。
* DELETE：删除(Delete)

根据 HTTP 规范，动词一律大写。

### 2、动词的覆盖
有些客户端只能使用 GET 和 POST 这两种方法。服务器必须接受 POST 模拟其它三种方法 (PUT、PATCH、DELETE)。这时，客户端发出的 HTTP 请求，要加上 `X-HTTP-Method-Override` 属性，告诉服务器应该使用哪一个动词，覆盖 `POST` 方法。
```
POST /api/Person/4 HTTP/1.1
X-HTTP-Method-Override: PUT
```
上面代码中，`X-HTTP-Method-Override` 指定本次请求的方法是 `PUT`，而不是 `POST`

### 3、宾语必须是名词
宾语就是 API 的 URL，是 HTTP 动词作用的对象。它应该是名词，不能是动词。比如，`/articles` 这个 URL 就是正确的，而下面的 URL 不是名词，所以都是错误的。
* /getAllCars
* /createNewCar
* /deleteAllRedCars

### 4、复数URL

