---
layout: page
title: 示例
show_sidebar: true
---


{% comment %}
This page contains additional examples of how to apply various parts of the specification.
{% endcomment %}
本页包含了额外的示例，用以说明如何应用规范的各个部分。

## <a href="#sparse-fieldsets" id="sparse-fieldsets" class="headerlink"></a> {% comment %}Sparse Fieldsets{% endcomment %}稀疏字段集

{% comment %}
Examples of how [sparse fieldsets]({{ site.github.url }}/format/#fetching-sparse-fieldsets) work.
{% endcomment %}
[稀疏字段]({{ site.github.url }}/format/#fetching-sparse-fieldsets)集如何工作的示例：

{% comment %}
Basic request:
{% endcomment %}
基本请求：
```http
GET /articles?include=author HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "data": [{
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "JSON API paints my bikeshed!",
      "body": "The shortest article. Ever.",
      "created": "2015-05-22T14:56:29.000Z",
      "updated": "2015-05-22T14:56:28.000Z"
    },
    "relationships": {
      "author": {
        "data": {"id": "42", "type": "people"}
      }
    }
  }],
  "included": [
    {
      "type": "people",
      "id": "42",
      "attributes": {
        "name": "John",
        "age": 80,
        "gender": "male"
      }
    }
  ]
}
```

{% comment %}
Request with `fields` parameter:
{% endcomment %}
含`fields`参数的请求：

```http
GET /articles?include=author&fields[articles]=title,body,author&fields[people]=name HTTP/1.1
```
{% comment %}
> Note: The above example URI shows unencoded `[` and `]` characters simply
for readability. In practice, these characters must be percent-encoded, as
noted in the base specification.
{% endcomment %}
> 注：以上URI示例显示未编码的’[‘和’]’字符必须是基本规范中注明的%编码。

{% comment %}
Here we want `articles` objects to have fields `title`, `body` and `author` only and `people` objects to have `name` field only.
{% endcomment %}
我们要想 `articles` 对象只拥有 `title`、`body` 和 `author` 字段，而`people`对象只拥有`name` 字段。

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "data": [{
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "JSON API paints my bikeshed!",
      "body": "The shortest article. Ever."
    },
    "relationships": {
      "author": {
        "data": {"id": "42", "type": "people"}
      }
    }
  }],
  "included": [
    {
      "type": "people",
      "id": "42",
      "attributes": {
        "name": "John"
      }
    }
  ]
}
```
{% comment %}
Pay attention to the fact that you have to add a relationship name both in `include` and `fields` (since relationships are fields too), otherwise you'll get:
{% endcomment %}
注意你要加关联名在`include` 和“`fields`”中（因为“关联”也是字段），否则，你会得到：

```http
GET /articles?include=author&fields[articles]=title,body&fields[people]=name HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "data": [{
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "JSON API paints my bikeshed!",
      "body": "The shortest article. Ever."
    }
  }],
  "included": [
    {
      "type": "people",
      "id": "42",
      "attributes": {
        "name": "John"
      }
    }
  ]
}
```
{% comment %}
> Note: The above example URI shows unencoded `[` and `]` characters simply
for readability. In practice, these characters must be percent-encoded, as
noted in the base specification.
{% endcomment %}
> 注：以上URI示例表明未编码的‘[’和’]’字符仅仅是为了可读性。实际应用中，这些字符必须是基本规范中注明的%编码。

## <a href="#pagination" id="pagination" class="headerlink"></a>{% comment% %} Pagination Links{% endcomment %}分页链接

{% comment %}
Example of a page-based strategy on how to add [pagination links](http://jsonapi.org/format/#fetching-pagination).
{% endcomment %}
有关如何添加基于页面的策略的[分页链接](http://jsonapi.org/format/#fetching-pagination)示例：

{% comment %}
Basic request:
{% endcomment %}
基本请求：

```http
GET /articles?page[number]=3&page[size]=1 HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "meta": {
    "total-pages": 13
  },
  "data": [
    {
      "type": "articles",
      "id": "3",
      "attributes": {
        "title": "JSON API paints my bikeshed!",
        "body": "The shortest article. Ever.",
        "created": "2015-05-22T14:56:29.000Z",
        "updated": "2015-05-22T14:56:28.000Z"
      }
    }
  ],
  "links": {
    "self": "http://example.com/articles?page[number]=3&page[size]=1",
    "first": "http://example.com/articles?page[number]=1&page[size]=1",
    "prev": "http://example.com/articles?page[number]=2&page[size]=1",
    "next": "http://example.com/articles?page[number]=4&page[size]=1",
    "last": "http://example.com/articles?page[number]=13&page[size]=1"
  }
}
```
{% comment %}
> Note: The above example URIs show unencoded `[` and `]` characters simply
for readability. In practice, these characters must be percent-encoded, as
noted in the base specification.

> Note: Putting a property like `"total-pages"` in `"meta"` can be a convenient way
to indicate to clients the total number of pages in a collection (as opposed to
the `"last"` link, which simply gives the URI of the last page). However, all
`"meta"` values are implementation-specific, so you can call this member whatever
you like (`"total"`, `"count"`, etc.) or not use it at all.
{% endcomment %}
> 注：以上URI示例表明未编码的`[`和`]`字符仅仅是为了可读性。实际应用中，这些字符必须是基本规范中注明的%编码。
> 注：在`"meta"`中加入如`"total-pages"`这样的属性能够方便的向客户端表明在一个集合中的页面总数（而不是`"last"`链接，它仅仅给出了最后一页的URI）。然而，所有的`"meta"`值都是特定于实现的，所以你怎么称呼这个成员随你便（`"total"`、`"count"`等）或者根本不用它。

<<<<<<< HEAD
## <a href="#error-objects" id="error-objects" class="headerlink"></a> {% comment %}Error Objects{% endcomment %}Error 对象
=======
## <a href="#error-objects" id="error-objects" class="headerlink"></a> Error 对象
>>>>>>> 72fd7c5e73f42c86e2102c1116147bb9d9c202a5

{% comment %}
Examples of how [error objects](http://jsonapi.org/format/#error-objects) work.
{% endcomment %}
[error 对象](http://jsonapi.org/format/#error-objects)使用示例：

### <a href="#error-objects-basics" id="error-objects-basics" class="headerlink"></a> {% comment %}A Basic Error Object{% endcomment %}基本的Error对象

{% comment %}
In the response below, the server is indicating that it encountered an error
while creating/updating the resource, and that this error was caused
by an invalid `"first-name"` attribute:
{% endcomment %}
在以下的响应中，服务器表明它在创建/更新资源时发生了错误，而错误是由无效的`"first  name"`引起的：

```http
HTTP/1.1 422 Unprocessable Entity
Content-Type: application/vnd.api+json

{
  "errors": [
    {
      "status": "422",
      "source": { "pointer": "/data/attributes/first-name" },
      "title":  "Invalid Attribute",
      "detail": "First name must contain at least three characters."
    }
  ]
}
```

{% comment %}
Every member in an error object is optional, but all help the client
by providing extra details.
{% endcomment %}
error对象中的每个成员是可选的，但都是为了辅助客户端，提供额外的细节信息。

{% comment %}
The `source` member is used to [indicate](https://tools.ietf.org/html/rfc6901)
which part of the request document caused the error.
{% endcomment %}
`source`成员是用来[表明](https://tools.ietf.org/html/rfc6901)请求文档的哪个部分发生了错误。

{% comment %}
The `title` and `detail` members are similar, but `detail` is specific
to this occurrence of the problem, whereas `title` is more generic.
{% endcomment %}
 `title`和`detail`成员也是如此，不过`detail`成员是发生的问题的详细信息，而`title`是比较通用的信息。

{% comment %}
The `status` member represents the HTTP status code associated with the problem.
It's very helpful when multiple errors are returned at once (see below), as the
HTTP response itself can only have one status code. However, it can also be
useful for single errors, to save clients the trouble of consulting the HTTP
headers, or for using JSON API over non-HTTP protocols, which may be officially
supported in the near future.
{% endcomment %}
`status`成员代表与问题相关的HTTP状态码。HTTP响应本身只能有一个状体码，一旦有多个错误返回时，它是很有用的（见下文）。然而，它也可用于单个错误，可保存客户端的查询HTTP头问题，或使用在非HTTP协议之上的JSON API，它或许会在不久后得到官方支持。

### <a href="#error-objects-multiple-errors" id="error-objects-multiple-errors" class="headerlink"></a> {% comment %}Multiple Errors{% endcomment %}多个错误

{% comment %}
When multiple errors occur in response to a single request, the server
can simply add each error to the `errors` array:
{% endcomment %}
当对单个请求的响应中出现多个错误时，服务器仅需将每个错误添加到errors数组中：

```http
HTTP/1.1 400 Bad Request
Content-Type: application/vnd.api+json

{
  "errors": [
    {
      "status": "403",
      "source": { "pointer": "/data/attributes/secret-powers" },
      "detail": "Editing secret powers is not authorized on Sundays."
    },
    {
      "status": "422",
      "source": { "pointer": "/data/attributes/volume" },
      "detail": "Volume does not, in fact, go to 11."
    },
    {
      "status": "500",
      "source": { "pointer": "/data/attributes/reputation" },
      "title": "The backend responded with an error",
      "detail": "Reputation service not responding after three requests."
    }
  ]
}
```

{% comment %}
The only uniqueness constraint on error objects is the `id` field. Thus,
multiple errors on the same attribute can each be given their own error
object. The example below shows multiple errors on the `"first-name"` attribute:
{% endcomment %}
对于error对象，只有`id`字段需要唯一性约束。那样，同一属性的多个错误可以单独分配它们自己的error对象。以下示例显示了有关`"first name"`属性的多个错误：

```http
HTTP/1.1 422 Unprocessable Entity
Content-Type: application/vnd.api+json

{
  "errors": [
    {
      "source": { "pointer": "/data/attributes/first-name" },
      "title": "Invalid Attribute",
      "detail": "First name must contain at least three characters."
    },
    {
      "source": { "pointer": "/data/attributes/first-name" },
      "title": "Invalid Attribute",
      "detail": "First name must contain an emoji."
    }
  ]
}
```
{% comment %}
> Note: in the responses above with a 422 status code, `400 Bad Request` would
also be acceptable. ([More details.](http://stackoverflow.com/a/20215807/1261879))
JSON API doesn't take a position on 400 vs. 422.
{% endcomment %}
> 注：以上是包含422状态码的响应，400 Bad Request也是可以接受的。（[更多细节](http://stackoverflow.com/a/20215807/1261879))。）JSON API没有对400和422作比对。

### <a href="#error-objects-error-codes" id="error-objects-error-codes" class="headerlink"></a> {% comment %}Error Codes{% endcomment　%}错误码

{% comment %}
The `code` member of an error object contains an application-specific code
representing the type of problem encountered. `code` is similar to `title`
in that both identify a general type of problem (unlike `detail`, which is
specific to the particular instance of the problem), but dealing with `code`
is easier programatically, because the "same" `title` may appear in different
forms due to localization.
{% endcomment %}
Error对象的`code`成员包含一个特定于应用的代码，它代表所遇问题的类型。在识别一个问题的一般类型（不像`detail`，只用于特定实例问题的详细信息），c`code`和t`title`相似。不过能用`code`以相对容易的编程方式处理，因为“相同”`title`由于本地化，会出现在不同的格式中。

{% comment %}
For the example below, imagine the API docs specifed the following mapping:
{% endcomment %}

{% comment %}
| Code | Problem                                                   |
|------|-----------------------------------------------------------|
|  123 | Value too short                                           |
|  225 | Password lacks a letter, number, or punctuation character |
|  226 | Passwords do not match                                    |
|  227 | Password cannot be one of last five passwords             |
{% endcomment %}
|  代码 | 问题                                                      |
|------|-----------------------------------------------------------|
|  123 | 值太短                                                     |
|  225 | 密码缺少一个字母、数字或标点字符                               |
|  226 | 密码不匹配                                                 |
|  227 | 密码不能为最近使用过的五个密码中的一个                          |

{% comment %}
Multiple errors on `"password"` attribute, with error `code`:
{% endcomment %}
包含错误`code`的有关`"password"`属性的多个错误：

```http
HTTP/1.1 422 Unprocessable Entity
Content-Type: application/vnd.api+json

{
  "jsonapi": { "version": "1.0" },
  "errors": [
    {
      "code":   "123",
      "source": { "pointer": "/data/attributes/first-name" },
      "title":  "Value is too short",
      "detail": "First name must contain at least three characters."
    },
    {
      "code":   "225",
      "source": { "pointer": "/data/attributes/password" },
      "title": "Passwords must contain a letter, number, and punctuation character.",
      "detail": "The password provided is missing a punctuation character."
    },
    {
      "code":   "226",
      "source": { "pointer": "/data/attributes/password" },
      "title": "Password and password confirmation do not match."
    }
  ]
}
```

{% comment %}
Notice that this response includes not only the `errors` top-level member,
but the `jsonapi` top-level member. Error responses may not contain the
top-level `data` member, but can include all the other top-level members
JSON API defines.
{% endcomment %}
注意这个响应不仅包含`errors`顶层（top-level）成员，也包括`jsonapi`顶层成员。错误响应不能包含顶层`data`成员，但可以包含所有其他JSON API 定义的顶层成员。

{% comment %}
Also, notice that the third error object lacks a `detail` member (perhaps
for security). Again, all error object members are optional.
{% endcomment %}
同样，注意第三个`error`对象缺少`detail`成员（或许是为了安全起见）。再次，所有`error`对象成员都是可选的。

### <a href="#error-objects-source-usage" id="error-objects-source-usage" class="headerlink"></a> {% comment %}Advanced `source` Usage{% endcomment %}`source`高级应用

{% comment %}
In the example below, the user is sending an invalid JSON API
request, because it's missing the `data` member:
{% endcomment %}
在以下示例中，用户是在发送一个无效JSON API请求，因为它丢失了`data`成员。

```http
PATCH /posts/1 HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{ "datum": [ ] }
```

{% comment %}
Therefore, the server responds:
{% endcomment %}
因此，服务器回应：

```http
HTTP/1.1 422 Unprocesssable Entity
Content-Type: application/vnd.api+json

{
  "errors": [
    {
      "source": { "pointer": "" },
      "detail":  "Missing `data` Member at document's top level."
    }
  ]
}
```

{% comment %]}
It uses `source` to point to the top-level of the document (`""`).
(Pointing to "/" would be an appropriate reference to the string
`"some value"` in the request document `{"": "some value"}`.
Pointing to `"/data"` would be invalid because the request document did
not have a value at `"/data"`, and `source` is always given with reference
to the request document.)
{% endcomment %}
它使用`source`来指向文档的顶层(`""`)。（在请求文档``{"": `"some value"`}``中，指向”/”是对字符串``“some value”``一个合理的引用。指向``“/data”``是无效的，因为在`“/data”`，请求文档没有值，并且`source`总是以到请求文档的引用形式给出。）

{% comment %}
If the server cannot parse the request as valid JSON, including
`source` doesn't make sense (because there's no JSON document for `source` to
refer to). Here's how the server might respond to an invalid JSON document:
{% endcomment %]}
如果服务器不能解析请求为有效的JSON数据格式，包括`source`在内，都没有意义（因为没有JSON文档来给`source`引用）。此处是服务器如何回应一个无效的JSON文档：

```json
{
  "errors": [{
    "status": "400",
    "detail": "JSON parse error - Expecting property name at line 1 column 2 (char 1)."
  }]
}
```

#### <a href="#error-objects-invalid-query-parameters" id="error-objects-invalid-query-parameters" class="headerlink"></a> {% comment %}Invalid Query Parameters{% endcomment %}无效查询参数

{% comment %}
The `source` member can also be used to indicate that the error originated
from a problem with a URI query parameter, like so:
{% endcomment %}
`source`成员也可以用来表明错误源自一个URI查询参数问题，就像这样：

```http
GET /api/posts/1?include=auther HTTP/1.1
```

```http
HTTP/1.1 400 Bad Request
Content-Type: application/vnd.api+json

{
  "errors": [
    {
      "source": { "parameter": "include" },
      "title":  "Invalid Query Parameter",
      "detail": "The resource does not have an `auther` relationship path."
    }
  ]
}
```

{% comment %}
In most cases, JSON API requires the server to return an error when it encounters
an invalid value for a JSON API–defined query parameter. However, for API-specific
query parameters (i.e. those not defined by JSON API), a server may choose to
ignore an invalid parameter and have the request succeed, rather than respond with
an error. [API-specific query parameters must contain one non a-z
character.](http://jsonapi.org/format/#query-parameters)
{% endcomment %}
多数情况下，当服务器遇到一个对于定义了的JSON API查询参数无效值时，JSON API需要服务器返回一个错误。然而，对于特定API查询参数（如，那些JSON API 没有定义的），服务器要选择忽略一个无效的参数，并请求成功，而非回应一个错误。[特定API的查询参数必须包含一个非’a’到’z’的字符。](http://jsonapi.org/format/#query-parameters)

{% comment %}
Other examples of invalid parameters include: `?felds[people]=` (invalid parameter name;
should be `fields[people]`) and `?redirect_to=http%3A%2F%2Fwww.owasp.org` (invalid parameter,
in this case, a phishing attack), etc.
{% endcomment %}
其他无效参数示例包括：`?felds[people]=` （无效的参数名；应该是f`fields[people]`）
及 `?redirect_to=http%3A%2F%2Fwww.owasp.org` （无效参数，在这个例子中，是一个网络钓鱼攻击），等等。


> 本页由Rehair翻译。
