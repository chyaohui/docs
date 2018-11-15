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
既然 URL 是名词，那么应该使用复数，还是单数？这个没有统一的规定，但常见的操作是读取一个集合，比如 `GET /articles`，这里明显应该是复数，为了统一起见，建议都使用复数 URL，比如：`GET /articles/2` 要好于 `GET /article/2`。

### 5、避免多级URL
常见的情况是，资源需要多级分类，因此很容易写出多级的 URL，比如获取某个作者的某一类文章。
```
GET /authors/12/categories/2
```
这种 URL 不利于扩展，语义也不明确，往往要想一会，才能明白含义。更好的做法是，除了第一级，其它级别都用查询字符串表达。
```
GET /authors/12?categories=2
```
下面是另一个例子，查询已发布的文章，你可能会设计成下面的 URL：
```
GET /articles/published
```
查询字符串的写法明显更好
```
GET /articles?published=true
```

## 二、状态码
### 1、状态码必须精确
客户端的每一次请求，服务器都必须给出回应，回应包括 HTTP 状态码和数据两部分。HTTP 状态码就是一个三位数，分成五个类别。
* 1xx：相关信息
* 2xx：操作成功
* 3xx：重定向
* 4xx：客户端错误
* 5xx：服务器错误

这五大类总共包含 100 多种状态码，覆盖了绝大部分可能遇到的情况。每一种状态码都有标准的解释，客户端只需查看状态码，就可以判断出发生了什么情况，所以服务器应该返回尽可能精确的状态码。API 不需要 1XX 状态码。

### 2、2xx状态码
2xx 状态码表示操作成功，但是不同的方法可以返回更精确的状态码。
* GET: 200 OK
* POST: 201 Created
* PUT: 200 OK
* PATCH: 200 OK
* DELETE: 204 No Content

上面代码中，POST 返回 201 状态码，表示生成了新的资源；DELETE 返回 204 状态码，表示资源已经不存在。此外，202 Accepted 状态码表示服务器已经收到请求，但还未进行处理，会在未来再处理，通常用于异步操作。

### 3、3xx状态码
API 用不到 301 状态码(永久重定向)和 302 状态码(暂时重定向)，因为它们可以由应用级别返回，浏览器会直接跳转，API 级别可以不考虑这两种情况。

API 用到的 3xx 状态码，主要是 303 See Other，表示参考另一个 URL。它与 302 和 307 的含义一样，也是暂时重定向，区别在于 302 和 307 用于 GET 请求，而 303 用于 POST、PUT、DELETE 请求。收到 303 以后，浏览器不会自动跳转，而会让用户自己决定下一步怎么办，下面是一个例子。
```
HTTP/1.1 303 See Other
Location: /api/orders/12345
```