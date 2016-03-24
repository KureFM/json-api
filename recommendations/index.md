---
layout: page
title: "建议"
show_sidebar: true
---

本节是JSON API实现的建议。这些建议是为了建立一致性，其描述的内容超出了JSON API规范的范围。
{% comment %}
This section contains recommendations for JSON API implementations. These
recommendations are intended to establish a level of consistency in areas that
are beyond the scope of the base JSON API specification.
{% endcomment %}

## <a href="#naming" id="naming" class="headerlink"></a> 命名{% comment %}Naming{% endcomment %}
成员的命名应该使用URL安全命名，其允许和推荐的字符被定义在格式规范中。同样是成员命名规范，推荐遵循下面的规则（更严格）。
{% comment %}
The allowed and recommended characters for an URL safe naming of members are defined in the format spec. To also standardize member names, the following (more restrictive) rules are recommended:
{% endcomment %}

- 成员命名**应该**以字符“a-z”（U+0061到U+007A）开始和结束。
- 成员命名**应该**只包含字符“a-z”（U+0061到U+007A），“0-9”（U+0030 to U+0039），和作为多个单词分隔符的连字符减号（U+002D HYPHEN-MINUS, "-”）。
{% comment %} 
- Member names **SHOULD** start and end with the characters "a-z" (U+0061 to U+007A)
- Member names **SHOULD** contain only the characters "a-z" (U+0061 to U+007A), "0-9" (U+0030 to U+0039), and the hyphen minus (U+002D HYPHEN-MINUS, "-") as separator between multiple words.
{% endcomment %}

## <a href="#urls" id="urls" class="headerlink"></a> URL设计{% comment %}URL Design{% endcomment %}

### <a href="#urls-reference-document" id="urls-reference-document" class="headerlink"></a> 参考文献{% comment %}Reference Document{% endcomment %}

当确定一个API的网址结构，它是有帮助的考虑其所有的资源的一个单一的“参考文件”，其中每个资源是在一个唯一的路径寻址中存在。 资源按类型在这个文档的顶层分组。 个人资源由ID这些类型集合内的关键字。 各个资源中的属性和链接是唯一可寻址根据上述资源对象的结构。
{% comment %}
When determining an API's URL structure, it is helpful to consider that all of
its resources exist in a single "reference document" in which each resource is
addressable at a unique path. Resources are grouped by type at the top level of
this document. Individual resources are keyed by ID within these typed
collections. Attributes and links within individual resources are uniquely
addressable according to the resource object structure described above.
{% endcomment %}

参考文件的这个概念被用​​于确定资源的适当的URL以及它们之间的关系。 要明白，该参考文献略结构从用于资源传输由于不同的目标和约束条件的文档的不同是很重要的。 例如，参考文档中的集合被表示为套因为成员必须按ID寻址的，而因为顺序是显著集合被表示为在运输文件阵列。
{% comment %}
This concept of a reference document is used to determine appropriate URLs for
resources as well as their relationships. It is important to understand that
this reference document differs slightly in structure from documents used to
transport resources due to different goals and constraints. For instance,
collections in the reference document are represented as sets because members
must be addressable by ID, while collections are represented as arrays in
transport documents because order is significant.
{% endcomment %}

### <a href="#urls-resource-collections" id="urls-resource-collections" class="headerlink"></a> 资源集合的URL{% comment %}URLs for Resource Collections{% endcomment %}

建议根据资源类型来建立资源集合的URL。
{% comment %}
It is recommended that the URL for a collection of resources be formed from
the resource type.
{% endcomment %}

例如，`photos`类型的资源集合URL应该像这样：
For example, a collection of resources of type `photos` will have the URL:

```text
/photos
```

### <a href="#urls-individual-resources" id="urls-individual-resources" class="headerlink"></a> 单个资源的URL{% comment %}URLs for Individual Resources{% endcomment %}

Treat collections of resources as sets keyed by resource ID. The URL for an
individual resource can be formed by appending the resource's ID to the
collection URL.

For example, a photo with an ID of `"1"` will have the URL:

```text
/photos/1
```

### <a href="#urls-relationships" id="urls-relationships" class="headerlink"></a> Relationship URLs and Related Resource URLs

As described in the base specification, there are two URLs that can be exposed
for each relationship:

* the "relationship URL" - a URL for the relationship itself, which is
identified with the `self` key in a relationship's `links` object. This URL
allows the client to directly manipulate the relationship. For example, it would
allow a client to remove an `author` from a `post` without deleting the `people`
resource itself.

* the "related resource URL" - a URL for the related resource(s), which is
identified with the `related` key within a relationship's `links` object. When
fetched, it returns the related resource object(s) as the response's primary data.

It is recommended that a relationship URL be formed by appending `/relationships/`
and the name of the relationship to the resource's URL.

For example, a photo's `comments` relationship will have the URL:

```text
/photos/1/relationships/comments
```

And a photo's `photographer` relationship will have the URL:

```text
/photos/1/relationships/photographer
```

It is recommended that a related resource URL be formed by appending the name
of the relationship to the resource's URL.

For example, the URL for a photo's `comments` will be:

```text
/photos/1/comments
```

And the URL for a photo's `photographer` will be:

```text
/photos/1/photographer
```

Because these URLs represent resources in relationships, they should not be
used as `self` links for the resources themselves. Instead the recommendations
for individual resource URLs should still apply when forming `self` links.

## <a href="#filtering" id="filtering" class="headerlink"></a> 过滤{% comment %}Filtering{% endcomment %}


The base specification is agnostic about filtering strategies supported by a
server. The `filter` query parameter is reserved to be used as the basis for
any filtering strategy.

It's recommended that servers that wish to support filtering of a resource
collection based upon associations do so by allowing query parameters that
combine `filter` with the association name.

例如，下面是一个获取指定post的所有评论的请求：
{% comment %}
For example, the following is a request for all comments associated with a
particular post:
{% endcomment %}

```http
GET /comments?filter[post]=1 HTTP/1.1
```

多个过滤器的值可以使用逗号分隔符列表组合起来，例如：
{% comment %}
Multiple filter values can be combined in a comma-separated list. For example:
{% endcomment %}

```http
GET /comments?filter[post]=1,2 HTTP/1.1
```

此外，一个请求中也可以使用多个过滤器。
{% comment %}
Furthermore, multiple filters can be applied to a single request:
{% endcomment %}

```http
GET /comments?filter[post]=1,2&filter[author]=12 HTTP/1.1
```

## <a href="#patchless-clients" id="patchless-clients" class="headerlink"></a>为缺乏`PATCH`的客户端提供支持 {% comment %}Supporting Clients Lacking `PATCH`{% endcomment %}

某些客户端，比如IE8，缺乏对`PATH`方法的支持。
如果服务器希望支持这些客户端，
那么建议将包含`X-HTTP-Method-Override: PATCH`标头的`POST`请求视为`PATCH`请求。
这使得缺乏对`PATCH`方法支持的客户端可以简单地通过添加标头来升级现有的请求方法。
{% comment %}
Some clients, like IE8, lack support for HTTP's `PATCH` method. API servers
that wish to support these clients are recommended to treat `POST` requests as
`PATCH` requests if the client includes the `X-HTTP-Method-Override: PATCH`
header. This allows clients that lack `PATCH` support to have their update
requests honored, simply by adding the header.
{% endcomment %}

## <a href="#date-and-time-fields" id="date-and-time-fields" class="headerlink"></a> 格式化日期和时间字段{% comment %}Formatting Date and Time Fields{% endcomment %}
尽管JSON API不对日期和时间字段的格式作要求，但建议服务器遵循ISO 8601规范。[W3C的说明](http://www.w3.org/TR/NOTE-datetime)提供了对推荐格式的概述。
{% comment %}
Although JSON API does not specify the format of date and time fields, it is
recommended that servers align with ISO 8601. [This W3C
NOTE](http://www.w3.org/TR/NOTE-datetime) provides an overview of the
recommended formats.
{% endcomment %}

## <a href="#asynchronous-processing" id="asynchronous-processing" class="headerlink"></a> 异步处理{% comment %}Asynchronous Processing{% endcomment %}

考虑一种情况：你需要创建一个资源，该操作需要很长时间才能完成。
{% comment %}
Consider a situation when you need to create a resource and the operation takes long time to complete.
{% endcomment %}

```http
POST /photos HTTP/1.1
```

该请求**应该**返回`202 Accepted`状态，并将一个链接放在`Content-Location`标头中。
{% comment %}
The request **SHOULD** return a status `202 Accepted` with a link in the `Content-Location` header.
{% endcomment %}

```http
HTTP/1.1 202 Accepted
Content-Type: application/vnd.api+json
Content-Location: https://example.com/photos/queue-jobs/5234

{
  "data": {
    "type": "queue-jobs",
    "id": "5234",
    "attributes": {
      "status": "Pending request, waiting other process"
    },
    "links": {
      "self": "/photos/queue-jobs/5234"
    }
  }
}
```

为了检查工作进程的状态，客户端可以发送向之前给出的地址发生一个请求。
{% comment %}
To check the status of the job process, a client can send a request to the location given earlier.
{% endcomment %}

```http
GET /photos/queue-jobs/5234 HTTP/1.1
Accept: application/vnd.api+json
```
当工作进程完成后，该请求**应该**返回`303 See other`状态，并将一个链接放在`Location`标头中。
{% comment %}
When job process is done, the request **SHOULD** return a status `303 See other` with a link in `Location` header.
{% endcomment %}

```http
HTTP/1.1 303 See other
Content-Type: application/vnd.api+json
Location: https://example.com/photos/4577
```
