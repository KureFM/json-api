---
version: 1.0
---

## <a href="#introduction" id="introduction" class="headerlink"></a> 介绍{% comment %}Introduction{% endcomment %}

是一个规范，它定义了客户端应该如何获取与修改资源，以及服务器应该如何响应客户端的请求。
{% comment %}
JSON API is a specification for how a client should request that resources be fetched or modified, and how a server should respond to those requests.
{% endcomment %}

JSON API 的设计尽可能减少请求量和客户端与服务器间传输的数据量。而且在实现高效的同时，无需牺牲可读性、灵活性和可发现性。
{% comment %}
JSON API is designed to minimize both the number of requests and the amount of
data transmitted between clients and servers. This efficiency is achieved
without compromising readability, flexibility, or discoverability.
{% endcomment %}

JSON API需要使用JSON API媒体类型([`application/vnd.api+json`](http://www.iana.org/assignments/media-types/application/vnd.api+json)) 进行数据交互。
{% comment %}
JSON API requires use of the JSON API media type
([`application/vnd.api+json`](http://www.iana.org/assignments/media-types/application/vnd.api+json))
for exchanging data.
{% endcomment %}

## <a href="#conventions" id="conventions" class="headerlink"></a> 约定{% comment %}Conventions{% endcomment %}

文档中的关键词，*MUST*，*MUST NOT*，*REQUIRED*，*SHALL*，*SHALL NOT*，*SHOULD*，*SHOULD NOT*，*RECOMMENDED*，*MAY*和*OPTIONAL*的具体描述在[RFC2119](http://tools.ietf.org/html/rfc2119)中。
{% comment %}
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in RFC 2119
[[RFC2119](http://tools.ietf.org/html/rfc2119)].
{% endcomment %}

### 附RFC 2119中对表示要求的动词的说明
1. **MUST**
表示绝对要这样做，**REQUIRED**或者**SHALL**与其同义，在翻译时应统一译为：**必须**。

2. **MUST NOT**
表示绝对不要这样做，**SHALL NOT**与其同义，在翻译时应统一译为：**不能**。

3. **SHOULD**
表示一般情况下应该这样做，但是某些特定情况下可以忽视这个要求。**RECOMMENDED**与其同义，在翻译时统一译为：**应该**。

4. **SHOULD NOT**
表示一般情况下不应该这样做，但是在某些特定情况下可以忽视这个要求。**NOT RECOMMENDED**与之同义，在翻译时统一译为：**不应该**。

5. **MAY**
表示这个要求完全是可选的，你可以这样做，也可以不这样做。**OPTIONAL**与之同义，在翻译时统一译为：**可以**

## <a href="#content-negotiation" id="content-negotiation" class="headerlink"></a> 内容协定{% comment %}Content Negotiation{% endcomment%}

### <a href="#content-negotiation-clients" id="content-negotiation-clients" class="headerlink"></a> 客户端的职责{% comment %}Client Responsibilities{% endcomment %}

客户端在发送JSON API数据的请求文档时，**必须**在HTTP头附加上`Content-Type: application/vnd.api+json`，但不能添加任何的媒体类型参数。
{% comment %}
Clients **MUST** send all JSON API data in request documents with the header
`Content-Type: application/vnd.api+json` without any media type parameters.
{% endcomment %}

在`Accept`头中包含了JSON API媒体类型的客户端**必须**至少指定一次媒体类型，但不能添加任何的媒体类型参数。
{% comment %}
Clients that include the JSON API media type in their `Accept` header **MUST**
specify the media type there at least once without any media type parameters.
{% endcomment %}

客户端在接收到`Content-Type`头含有`application/vnd.api+json`媒体类型的响应文档时，**必须**忽略其所有的参数。
{% comment %}
Clients **MUST** ignore any parameters for the `application/vnd.api+json`
media type received in the `Content-Type` header of response documents.
{% endcomment %}

### <a href="#content-negotiation-servers" id="content-negotiation-servers" class="headerlink"></a> 服务器的职责{% comment %}Server Responsibilities{% endcomment %}

服务器发送的所有包含JSON API数据的文档**必须**在HTTP头中添加`Content-Type: application/vnd.api+json`，但不能添加任何的媒体类型参数。
{% comment %}
Servers **MUST** send all JSON API data in response documents with the header
`Content-Type: application/vnd.api+json` without any media type parameters.
{% endcomment %}

服务器若收到了在指定`Content-Type: application/vnd.api+json`的同时，还包含了任意媒体类型参数的请求，**必须**返回`415 Unsupported Media Type`状态码。
{% comment %}
Servers **MUST** respond with a `415 Unsupported Media Type` status code if
a request specifies the header `Content-Type: application/vnd.api+json`
with any media type parameters.
{% endcomment %}

服务器若收到了`Accept`头中含有JSON API或其他媒体类型，但却被媒体类型参数修改的请求时，**必须**返回`406 Not Acceptable`状态码。
{% comment %}
Servers **MUST** respond with a `406 Not Acceptable` status code if a
request's `Accept` header contains the JSON API media type and all instances
of that media type are modified with media type parameters.
{% endcomment %}

> 说明：内容协定需要存在于该规范的将来的版本中，需要使用媒体参数来扩展协定或协商版本。
{% comment %}
> Note: The content negotiation requirements exist to allow future versions
of this specification to use media type parameters for extension negotiation
and versioning.
{% endcomment %}

## <a href="#document-structure" id="document-structure" class="headerlink"></a> 文档结构{% comment %}Document Structure{% endcomment %}

这一章节描述JSON API文档结构，该结构通过媒体类型([application/vnd.api+json](http://www.iana.org/assignments/media-types/application/vnd.api+json)) 标识。JSON API文档使用JavaScript Object Notation[[RFC7159](http://tools.ietf.org/html/rfc7159)]定义。
{% comment %}
This section describes the structure of a JSON API document, which is identified
by the media type [`application/vnd.api+json`]
(http://www.iana.org/assignments/media-types/application/vnd.api+json).
JSON API documents are defined in JavaScript Object Notation (JSON)
[[RFC7159](http://tools.ietf.org/html/rfc7159)].
{% endcomment %}

尽管请求和响应文档都使用同样的媒体类型，但某些特性只适用于其中一种。下面描述它们之间的差异。
{% comment %}
Although the same media type is used for both request and response documents,
certain aspects are only applicable to one or the other. These differences are
called out below.
{% endcomment %}

除非另有说明，否则本规范中定义的对象**不能**包含任何附加的成员，客户端和服务器的实现也**必须**忽略规范不认可的成员。
{% comment %}
Unless otherwise noted, objects defined by this specification **MUST NOT**
contain any additional members. Client and server implementations **MUST**
ignore members not recognized by this specification.
{% endcomment %}

> 说明：这些条件允许通过本规范通过累积修改来衍变。
{% comment %}
> Note: These conditions allow this specification to evolve through additive
changes.
{% endcomment %}

### <a href="#document-top-level" id="document-top-level" class="headerlink"></a> 顶层{% comment %}Top Level{% endcomment %}

每个JSON API请求或响应数据的根**必须**有一个JSON对象。这个对象定义文档的“顶层”。
{% comment %}
A JSON object **MUST** be at the root of every JSON API request and response
containing data. This object defines a document's "top level".
{% endcomment %}

文档**必须**至少包含一个下列的顶层成员：
{% comment %}
A document **MUST** contain at least one of the following top-level members:
{% endcomment %}

- `data`：文档的“主数据”
- `errors`：[错误对象](#errors)的数组
- `meta`：[元对象][meta]，它包含非标准的元信息
{% comment %}
* `data`: the document's "primary data"
* `errors`: an array of [error objects](#errors)
* `meta`: a [meta object][meta] that contains non-standard
  meta-information.
{% endcomment %}

成员`data`和`errors`**不能**同时存在一个文档中。
{% comment %}
The members `data` and `errors` **MUST NOT** coexist in the same document.
{% endcomment %}

文档**可以**包含下列几个顶层成员：
{% comment %}
A document **MAY** contain any of these top-level members:
{% endcomment %}

- `jsonapi`：描述服务器实现的对象
- `links`：与主数据有关的[链接对象][links]
- `included`：与主数据 和/或 彼此有关的[资源对象][resource objects]数组（包含资源）
{% comment %}
* `jsonapi`: an object describing the server's implementation
* `links`: a [links object][links] related to the primary data.
* `included`: an array of [resource objects] that are related to the primary
  data and/or each other ("included resources").
{% endcomment %}

如果文档没有包含顶层`data`键（成员），那么`include`成员则**不能**出现在文档中。
{% comment %}
If a document does not contain a top-level `data` key, the `included` member
**MUST NOT** be present either.
{% endcomment %}

顶层[链接对象][links]**可以**包含下列成员：
{% comment %}
The top-level [links object][links] **MAY** contain the following members:
{% endcomment %}

- `self`：当前响应文档的[链接][links]
- `related`：当主数据可以表示成一种资源关联，使用[相关资源链接][related resource link]来描述它
- 主数据的[分页][pagination]链接。
{% comment %}
* `self`: the [link][links] that generated the current response document.
* `related`: a [related resource link] when the primary data represents a
  resource relationship.
* [pagination] links for the primary data.
{% endcomment %}

文档中的“主数据”指请求的资源或资源集合。
{% comment %}
The document's "primary data" is a representation of the resource or collection
of resources targeted by a request.
{% endcomment %}

主数据**必须**是：
{% comment %}
Primary data **MUST** be either:
{% endcomment %}

- 对于单个资源的请求：单一[资源对象][resource objects]，单一[资源标识对象][resource identifier object]，或者`null`
- 对于资源集合的请求：[资源对象][resource objects]数组，[资源标识对象][resource identifier object]数组，或者空数组（`[]`）
{% comment %}
* a single [resource object][resource objects], a single [resource identifier object], or `null`,
  for requests that target single resources
* an array of [resource objects], an array of
  [resource identifier objects][resource identifier object], or
  an empty array (`[]`), for requests that target resource collections
{% endcomment %}

例如，下面的主数据是单一*资源对象*：
{% comment %}
For example, the following primary data is a single resource object:
{% endcomment %}

``` json
{
  "data": {
    "type": "articles",
    "id": "1",
    "attributes": {
      // ... this article's attributes
    },
    "relationships": {
      // ... this article's relationships
    }
  }
}
```

下面的主数据是单一[资源标识对象][resource identifier object]，它表示了和上面同样的资源：
{% comment %}
The following primary data is a single [resource identifier object] that
references the same resource:
{% endcomment %}

```json
{
  "data": {
    "type": "articles",
    "id": "1"
  }
}
```

资源的逻辑集合**必须**表示为一个数组，即使它有一项或为空。
{% comment %}
A logical collection of resources **MUST** be represented as an array, even if
it only contains one item or is empty.
{% endcomment %}

### <a href="#document-resource-objects" id="document-resource-objects" class="headerlink"></a> 资源对象{% comment %}Resource Objects{% endcomment %}

“资源对象”被用来在JSON API文档中表示资源。
{% comment %}
"Resource objects" appear in a JSON API document to represent resources.
{% endcomment %}

资源对象**必须**至少包含以下顶层成员：
{% comment %}
A resource object **MUST** contain at least the following top-level members:
{% endcomment %}

* `id`
* `type`

例外：当资源对象源于客户端或者是用于表示即将要在服务器上创建的新资源时，`id`成员不是必须的。
{% comment %}
Exception: The `id` member is not required when the resource object originates at
the client and represents a new resource to be created on the server.
{% endcomment %}

此外，*资源对象***可以**包含下列任意的顶层成员：
{% comment %}
In addition, a resource object **MAY** contain any of these top-level members:
{% endcomment %}

- `attributes`：[属性对象][attributes]表示该资源的一些数据
- `relationships`：[关联对象][relationships]描述该资源与其他JSON API资源的关联
- `links`：[连接对象][links]包含与该资源有关的链接
- `meta`：[元对象][meta]包含与资源有关，但不能表示为属性或关联的非标准元信息
{% comment %}
* `attributes`: an [attributes object][attributes] representing some of the resource's data.
* `relationships`: a [relationships object][relationships] describing relationships between
 the resource and other JSON API resources.
* `links`: a [links object][links] containing links related to the resource.
* `meta`: a [meta object][meta] containing non-standard meta-information about a
  resource that can not be represented as an attribute or relationship.
{% endcomment %}

下面展示了一篇文章（即类型为“articles”的资源）在文档中是怎么样的：
{% comment %}
Here's how an article (i.e. a resource of type "articles") might appear in a document:
{% endcomment %}

```json
// ...
{
  "type": "articles",
  "id": "1",
  "attributes": {
    "title": "Rails is Omakase"
  },
  "relationships": {
    "author": {
      "links": {
        "self": "/articles/1/relationships/author",
        "related": "/articles/1/author"
      },
      "data": { "type": "people", "id": "9" }
    }
  }
}
// ...
```

#### <a href="#document-resource-object-identification" id="document-resource-object-identification" class="headerlink"></a> 标识符{% comment %}Identification{% endcomment %}

每个[资源对象][resource objects]**必须**包含`id`成员和`type`成员。同时**必须**设置`id`和`type`成员的值。
{% comment %}
Every [resource object][resource objects] **MUST** contain an `id` member and a `type` member.
The values of the `id` and `type` members **MUST** be strings.
{% endcomment %}

在给定的API中，每个资源对象`type`与`id`对**必须**表示一个唯一的资源。（URL的集合由一台服务器或者服务器集群控制，这些URL的集合构成了API。）
{% comment %}
Within a given API, each resource object's `type` and `id` pair **MUST**
identify a single, unique resource. (The set of URIs controlled by a server,
or multiple servers acting as one, constitute an API.)
{% endcomment %}

`type`成员用来描述拥有共同属性和关联的[资源对象][resource objects]。
{% comment %}
The `type` member is used to describe [resource objects] that share common
attributes and relationships.
{% endcomment %}

`type`成员的值**必须**遵循与[成员名称][member names]一样的原则。
{% comment %}
The values of `type` members **MUST** adhere to the same constraints as
[member names].
{% endcomment %}

> 说明：该规范无法预知所有的情况，所以`type`的值既可以是单数，也可以是复数。但应该始终使用同样的值。
{% comment %}
> Note: This spec is agnostic about inflection rules, so the value of `type`
can be either plural or singular. However, the same value should be used
consistently throughout an implementation.
{% endcomment %}

#### <a href="#document-resource-object-fields" id="document-resource-object-fields" class="headerlink"></a> 字段{% comment %}Fields{%  endcomment %}

资源对象的属性[attributes]和关联[relationships]统称为字段[fields]
{% comment %}
A resource object's [attributes] and its [relationships] are collectively called
its "[fields]".
{% endcomment %}

资源对象[resource objects]的所有字段**必须**共用一个命名空间，同时`type`和`id`也处在该命名空间中。换句话说，资源对象不能有相同名称的属性或关联，也不能将属性或者关联命名为`type`或者`id`。
{% comment %}
Fields for a [resource object][resource objects] **MUST** share a common namespace with each
other and with `type` and `id`. In other words, a resource can not have an
attribute and relationship with the same name, nor can it have an attribute
or relationship named `type` or `id`.
{% endcomment %}

#### <a href="#document-resource-object-attributes" id="document-resource-object-attributes" class="headerlink"></a> 属性{% comment %}Attributes{% endcomment %}

属性的值**必须**是对象（属性对象）。属性对象（属性）的成员表示它所定义的资源对象[resource objects]的信息。
{% comment %}
The value of the `attributes` key **MUST** be an object (an "attributes
object"). Members of the attributes object ("attributes") represent information
about the [resource object][resource objects] in which it's defined.
{% endcomment %}

属性可以包含任何有效的JSON值。
{% comment %}
Attributes may contain any valid JSON value.
{% endcomment %}

可以将JSON对象或者数组等复合数据结构作为属性的值。但是构成属性的任何对象都**不能**含有`relationships`和`links`成员，因为这些成员被留作将来使用。
{% comment %}
Complex data structures involving JSON objects and arrays are allowed as
attribute values. However, any object that constitutes or is contained in an
attribute **MUST NOT** contain a `relationships` or `links` member, as those
members are reserved by this specification for future use.
{% endcomment %}

尽管外键（例如：`author_id`）通常与其他信息存储在一起，但这些键**不应该**出现在属性中。
{% comment %}
Although has-one foreign keys (e.g. `author_id`) are often stored internally
alongside other information to be represented in a resource object, these keys
**SHOULD NOT** appear as attributes.
{% endcomment %}

> 说明：请参见[字段][fields]和[成员命名][member names]来了解更多的约束条件。
{% comment %}
> Note: See [fields] and [member names] for more restrictions on this container.
{% endcomment %}

#### <a href="#document-resource-object-relationships" id="document-resource-object-relationships" class="headerlink"></a> 关联{% comment %}Relationships{% endcomment %}

`relationships`的值必须是一个对象（关联对象）。关联对象（"关联"）的成员表示从当前[资源对象][resource objects]到它所定义的那个资源对象的引用。
{% comment %}
The value of the `relationships` key **MUST** be an object (a "relationships
object"). Members of the relationships object ("relationships") represent
references from the [resource object][resource objects] in which it's defined to other resource
objects.
{% endcomment %}

关联可以是一对一也可以是一对多
{% comment %}
Relationships may be to-one or to-many.
{% endcomment %}

一个关联对象**必须**至少包含下列成员中的一个：
{% comment %}
A "relationship object" **MUST** contain at least one of the following:
{% endcomment %}

- `links`：[链接对象][links]至少包含下列成员中的一个：
    - `self`：关联自身的链接（“关联链接”）。该链接允许客户端直接操纵关联。
    例如：通过`author`的关联URL来移除一个`author`，会导致`preson`和`author`之间的连接断开，但并不会删除`person`。
    当获取成功后，链接会返回以相关资源为主数据的[联动][resource linkage]。（参见[获取关联](#fetching-relationships)。）
    - `related`：[相关资源链接][related resource link]
- `data`：[资源联动][resource linkage]
- `meta`： 与关联有关的不规则的元信息构成的[元对象][meta]
{% comment %}
* `links`: a [links object][links] containing at least one of the following:
  * `self`: a link for the relationship itself (a "relationship link"). This
    link allows the client to directly manipulate the relationship. For example,
    removing an `author` through an `article`'s relationship URL would disconnect
    the person from the `article` without deleting the `people` resource itself.
    When fetched successfully, this link returns the [linkage][resource linkage]
    for the related resources as its primary data.
    (See [Fetching Relationships](#fetching-relationships).)
  * `related`: a [related resource link]
* `data`: [resource linkage]
* `meta`: a [meta object][meta] that contains non-standard meta-information about the
  relationship.
{% endcomment %}

表示一对多关联的关联对象，还**可以**像下面描述的那样，在`links`成员里加上分页[pagination]。
{% comment %}
A relationship object that represents a to-many relationship **MAY** also contain
[pagination] links under the `links` member, as described below.
{% endcomment %}

> 说明：请参见[字段][fields]和[成员命名][member names]来了解更多的约束条件。
{% comment %}
> Note: See [fields] and [member names] for more restrictions on this container.
{% endcomment %}

#### <a href="#document-resource-object-related-resource-links" id="document-resource-object-related-resource-links" class="headerlink"></a> 相关资源链接{% comment %}Related Resource Links{% endcomment %}

“相关资源链接”提供了访问[关联][relationships]中的[资源对象][resource objects][链接][links]的方法。在访问的时候，相关资源对象会作为响应的主数据返回。
{% comment %}
A "related resource link" provides access to [resource objects]
 [linked][links]
in a [relationship][relationships]. When fetched, the related resource object(s)
are returned as the response's primary data.
{% endcomment %}

例如，`article`的'comments'[关联][relationships]可以指定一个链接，当通过`GET`请求该链接时，返回一个评论集合的资源对象。
{% comment %}
For example, an `article`'s `comments` [relationship][relationships] could
specify a [link][links] that returns a collection of comment [resource objects]
when retrieved through a `GET` request.
{% endcomment %}

如果存在相关资源链接，就**必须**引用有效的URL，即使关联不与任何目标资源关联。
此外，一个相关资源链接**不能**因关联的内容的变化而改变。
{% comment %}
If present, a related resource link **MUST** reference a valid URL, even if the
relationship isn't currently associated with any target resources. Additionally,
a related resource link **MUST NOT** change because its relationship's content
changes.
{% endcomment %}

#### <a href="#document-resource-object-linkage" id="document-resource-object-linkage" class="headerlink"></a> 资源联动{% comment %}Resource Linkage{% endcomment %}

复合文档[compound document]中的资源联动允许客户端将所有包含的资源对象[resource objects]连在一起，无需通过`GET`访问链接[links]中的任何URL。
{% comment %}
Resource linkage in a [compound document] allows a client to link together all
of the included [resource objects] without having to `GET` any URLs via [links].
{% endcomment %}

资源联动**必须**是下列中的一种
{% comment %}
Resource linkage **MUST** be represented as one of the following:
{% endcomment %}

* 用`null`表示的空对一关联。
* 用空数组（`[]`）表示的空对多关联。
* 用一个[资源标识符对象][resource identifier object]来表示非空对一的关联。
* 用[资源标识符对象][resource identifier object]数组来表示非空对多的关联。
{% comment %}
* `null` for empty to-one relationships.
* an empty array (`[]`) for empty to-many relationships.
* a single [resource identifier object] for non-empty to-one relationships.
* an array of [resource identifier objects][resource identifier object] for non-empty to-many relationships.
{% endcomment %}

> 说明：该规范不赋予资源标识符对象在多关系联动数组中顺序的意义，虽然实现这一点并不困难。资源标识符对象数组可以表示有序或无序的关系，并且这两种类型可以在响应对象中混合使用。
{% comment %}
> Note: The spec does not impart meaning to order of resource identifier
objects in linkage arrays of to-many relationships, although implementations
may do that. Arrays of resource identifier objects may represent ordered
or unordered relationships, and both types can be mixed in one response
object.
{% endcomment %}

例如，下面的文章是与`author` 关联在一起的：
{% comment %}
For example, the following article is associated with an `author`:
{% endcomment %}

```json
// ...
{
  "type": "articles",
  "id": "1",
  "attributes": {
    "title": "Rails is Omakase"
  },
  "relationships": {
    "author": {
      "links": {
        "self": "http://example.com/articles/1/relationships/author",
        "related": "http://example.com/articles/1/author"
      },
      "data": { "type": "people", "id": "9" }
    }
  },
  "links": {
    "self": "http://example.com/articles/1"
  }
}
// ...
```

`author`关联包含了关联本身的一个链接（允许客户端直接更改相关的作者），以及一个可以直接获取资源对象和联动信息的相关资源链接。
{% comment %}
The `author` relationship includes a link for the relationship itself (which
allows the client to change the related author directly), a related resource
link to fetch the resource objects, and linkage information.
{% endcomment %}

#### <a href="#document-resource-object-links" id="document-resource-object-links" class="headerlink"></a> 资源链接{% comment %}Resource Links{% endcomment %}

[资源对象][resource objects]中的可选`links`成员含有与[链接][links]相关的资源。
{% comment %}
The optional `links` member within each [resource object][resource objects] contains [links]
related to the resource.
{% endcomment %}

链接对象**可以**包含一个`self`链接[links]，该链接标识了该资源对象所表示的资源。
{% comment %}
If present, this links object **MAY** contain a `self` [link][links] that
identifies the resource represented by the resource object.
{% endcomment %}

```json
// ...
{
  "type": "articles",
  "id": "1",
  "attributes": {
    "title": "Rails is Omakase"
  },
  "links": {
    "self": "http://example.com/articles/1"
  }
}
// ...
```

服务器**必须**响应指定URL的`GET`请求，并返回以资源为主数据的响应。
{% comment %}
A server **MUST** respond to a `GET` request to the specified URL with a
response that includes the resource as the primary data.
{% endcomment %}

### <a href="#document-resource-identifier-objects" id="document-resource-identifier-objects" class="headerlink"></a> 资源标识对象{% comment %}Resource Identifier Objects{% endcomment %}

“资源标识对象”用于标识一个唯一的资源。
{% comment %}
A "resource identifier object" is an object that identifies an individual
resource.
{% endcomment %}

“资源标识对象”**必须**包含`type`和`id`成员。
{% comment %}
A "resource identifier object" **MUST** contain `type` and `id` members.
{% endcomment %}

“资源标识对象”也**可以**包含`mate`成员，它的值是一个包含非标准元信息的[元][meta]对象。
{% comment %}
A "resource identifier object" **MAY** also include a `meta` member, whose value is a [meta] object that
contains non-standard meta-information.
{% endcomment %}

### <a href="#document-compound-documents" id="document-compound-documents" class="headerlink"></a> 复合文档{% comment %}Compound Documents{% endcomment %}

为了减少HTTP的请求数量，服务器**可以**将相关资源以及所需的主要资源放在同一个响应中返回给客户端。这种响应方式称为“复合文档”。
{% comment %}
To reduce the number of HTTP requests, servers **MAY** allow responses that
include related resources along with the requested primary resources. Such
responses are called "compound documents".
{% endcomment %}

在复合文档中，所有的资源**必须**被表示成一个[资源对象][resource objects]数组，并放在顶层`included`成员中。
{% comment %}
In a compound document, all included resources **MUST** be represented as an
array of [resource objects] in a top-level `included` member.
{% endcomment %}

符合文档需要“全联动”，这意味着每个在文档中的资源**必须**至少被[资源标识对象][resource identifier object]标识一次。这些资源标识符要么是主数据，要么就是包含资源联动的主资源或者包含资源。
{% comment %}
Compound documents require "full linkage", meaning that every included
resource **MUST** be identified by at least one [resource identifier object]
in the same document. These resource identifier objects could either be
primary data or represent resource linkage contained within primary or
included resources.
{% endcomment %}

全联动要求唯一可以例外的是，原本包含联动的关系字段通过被[稀疏字段集](#fetching-sparse-fieldsets)排除在外。
{% comment %}
The only exception to the full linkage requirement is when relationship fields
that would otherwise contain linkage data are excluded via [sparse fieldsets](#fetching-sparse-fieldsets).
{% endcomment %}

> 说明：全联动确保被包含的资源是与主数据（可能是[资源对象][resource objects]或者[资源标识对象][resource identifier object]）或者彼此相关的。
{% comment %}
> Note: Full linkage ensures that included resources are related to either
the primary data (which could be [resource objects] or [resource identifier
objects][resource identifier object]) or to each other.
{% endcomment %}

多包含关系的完整示例文档：
{% comment %}
A complete example document with multiple included relationships:
{% endcomment %}
```json
{
  "data": [{
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "JSON API paints my bikeshed!"
    },
    "links": {
      "self": "http://example.com/articles/1"
    },
    "relationships": {
      "author": {
        "links": {
          "self": "http://example.com/articles/1/relationships/author",
          "related": "http://example.com/articles/1/author"
        },
        "data": { "type": "people", "id": "9" }
      },
      "comments": {
        "links": {
          "self": "http://example.com/articles/1/relationships/comments",
          "related": "http://example.com/articles/1/comments"
        },
        "data": [
          { "type": "comments", "id": "5" },
          { "type": "comments", "id": "12" }
        ]
      }
    }
  }],
  "included": [{
    "type": "people",
    "id": "9",
    "attributes": {
      "first-name": "Dan",
      "last-name": "Gebhardt",
      "twitter": "dgeb"
    },
    "links": {
      "self": "http://example.com/people/9"
    }
  }, {
    "type": "comments",
    "id": "5",
    "attributes": {
      "body": "First!"
    },
    "relationships": {
      "author": {
        "data": { "type": "people", "id": "2" }
      }
    },
    "links": {
      "self": "http://example.com/comments/5"
    }
  }, {
    "type": "comments",
    "id": "12",
    "attributes": {
      "body": "I like XML better"
    },
    "relationships": {
      "author": {
        "data": { "type": "people", "id": "9" }
      }
    },
    "links": {
      "self": "http://example.com/comments/12"
    }
  }]
}
```

[复合文档][compound document]的每个`type`和`id`键值对**不能**表示超过一个[资源对象][resource objects]
{% comment %}
A [compound document] **MUST NOT** include more than one [resource object][resource objects] for
each `type` and `id` pair.
{% endcomment %}
> 注：在单个文档中，你会想到将将`type`和`id`作为组合键来唯一地索引另一个文档中的[资源对象][resource objects]。
{% comment %}
> Note: In a single document, you can think of the `type` and `id` as a
composite key that uniquely references [resource objects] in another part of
the document.
{% endcomment %}

> 注：这种方法可以确保每个响应都能返回一个规范的[资源对象][resource objects]，即便是在同样资源被多次引用的情况下。
{% comment %}
> Note: This approach ensures that a single canonical [resource object][resource objects] is
returned with each response, even when the same resource is referenced
multiple times.
{% endcomment %}

### <a href="#document-meta" id="document-meta" class="headerlink"></a> 元信息{% comment %}Meta Information{% endcomment %}
当需要的时候，`meta`成员可以用于存放多个非标准的元信息。每个`meta`成员的值必须是一个对象（“元对象”）。
{% comment %}
Where specified, a `meta` member can be used to include non-standard
meta-information. The value of each `meta` member **MUST** be an object (a
"meta object").
{% endcomment %}

任何成员都可以放在`meta`对象中。
{% comment %}
Any members **MAY** be specified within `meta` objects.
{% endcomment %}

例如：
{% comment %}
For example:
{% endcomment %}

```json
{
  "meta": {
    "copyright": "Copyright 2015 Example Corp.",
    "authors": [
      "Yehuda Katz",
      "Steve Klabnik",
      "Dan Gebhardt",
      "Tyler Kellen"
    ]
  },
  "data": {
    // ...
  }
}
```

### <a href="#document-links" id="document-links" class="headerlink"></a> 链接{% comment %}Links{% endcomment %}
当需要的时候，`links`成员可以用来表示链接。`links`成员的值必须是一个对象（“链接对象”）。
{% comment %}
Where specified, a `links` member can be used to represent links. The value
of each `links` member **MUST** be an object (a "links object").
{% endcomment %}

链接对象的每个成员都是一个“链接”，链接必须是下面的任一种表示方式：
{% comment %}
Each member of a links object is a "link". A link **MUST** be represented as
either:
{% endcomment %}

- 一个包含链接URL的字符串
- 一个包含下列成员的对象（链接对象）：
  - `href`：一个包含链接URL的字符串。
  - `meta`：一个包含关于链接的非标准元信息的元对象。
{% comment %}
* a string containing the link's URL.
* an object ("link object") which can contain the following members:
  * `href`: a string containing the link's URL.
  * `meta`: a meta object containing non-standard meta-information about the
    link.
{% endcomment %}

{%comment%}
The following `self` link is simply a URL:
{%endcomment%}
以下”self”链接仅仅是一个URL：

```json
"links": {
  "self": "http://example.com/posts",
}
```

{%comment%}
The following `related` link includes a URL as well as meta-information
about a related resource collection:
{%endcomment%}
以下`reltated`链接包含一个URL，同时包含相关资源集合的元信息：

```json
"links": {
  "related": {
    "href": "http://example.com/articles/1/comments",
    "meta": {
      "count": 10
    }
  }
}
```

{%comment%}
> Note: Additional members may be specified for links objects and link
objects in the future. It is also possible that the allowed values of
additional members will be expanded (e.g. a `collection` link may support an
array of values, whereas a `self` link does not).
{%endcomment%}
> 注：附加的成员是将来指定给links对象和link对象的。可能附加成员的允许值会在以后扩展（如，一个collection链接或许支持一个值数组，然而self链接却不是）。

### <a href="#document-jsonapi-object" id="document-jsonapi-object" class="headerlink"></a> {%comment%}JSON API Object{%endcomment%}JSON API对象

{%comment%}
A JSON API document **MAY** include information about its implementation
under a top level `jsonapi` member. If present, the value of the `jsonapi`
member **MUST** be an object (a "jsonapi object"). The jsonapi object **MAY**
contain a `version` member whose value is a string indicating the highest JSON
API version supported. This object **MAY** also contain a `meta` member, whose
value is a [meta] object that contains non-standard meta-information.
{%endcomment%}
JSON API文档 **可以** 包含其实现在一个顶层`jsonapi`成员下的信息。`Jsonapi`成员的值 **必须** 以一个对象（jsonapi对象）的形式出现。Jsonapi对象 **可以** 包含一个`version`对象，它的值是一个表明支持最高高版本JSON API的字符串。此对象也 **可以** 包含一个`meta`成员，它的值是一个包含非标准元信息的[meta]对象。

```json
{
  "jsonapi": {
    "version": "1.0"
  }
}
```

{%comment%}
If the `version` member is not present, clients should assume the server
implements at least version 1.0 of the specification.
{%endcomment%}
如果没有`version`成员，客户端应假定服务器端实现至少符合的规范版本为1.0。

{%comment%}
> Note: Because JSON API is committed to making additive changes only, the
version string primarily indicates which new features a server may support.

{%endcomment%}
> 注：因为JSON API只致力于作添加的改变，版本字符串主要表明一个服务器可能支持的新特性。

### <a href="#document-member-names" id="document-member-names" class="headerlink"></a>{%comment%} Member Names{%endcomment%}成员名

{%comment%}
All member names used in a JSON API document **MUST** be treated as case sensitive
by clients and servers, and they **MUST** meet all of the following conditions:
{%endcomment%}
一个JSON API文档中的所有成员名命名 **必须** 视客户端和服务器端对大小写敏感，且 **必须** 符合以下条件：

{%comment%}
- Member names **MUST** contain at least one character.
- Member names **MUST** contain only the allowed characters listed below.
- Member names **MUST** start and end with a "globally allowed character",
  as defined below.
{%endcomment%}

- 成员名 **必须** 至少包含一个字符。
- 成员名 **必须** 只能包含以下列出的字符：
- 成员名 **必须** 以如下定义的一个全局允许的字符起始和结束。

{%comment%}
To enable an easy mapping of member names to URLs, it is **RECOMMENDED** that
member names use only non-reserved, URL safe characters specified in [RFC 3986](http://tools.ietf.org/html/rfc3986#page-13).
{%endcomment%}
为了使一个简单的成员名到URL的映射可用，成员名 **应该** 只用非保留字符，URL安全字符指定在[RFC 3386](http://tools.ietf.org/html/rfc3986#page-13)文档中。

#### <a href="#document-member-names-allowed-characters" id="document-member-names-allowed-characters" class="headerlink"></a> {%comment%}Allowed Characters{%endcomment%}允许使用的字符

{%comment%}
The following "globally allowed characters" **MAY** be used anywhere in a member name:

- U+0061 to U+007A, "a-z"
- U+0041 to U+005A, "A-Z"
- U+0030 to U+0039, "0-9"
- U+0080 and above (non-ASCII Unicode characters; _not recommended, not URL safe_)
{%endcomment%}
以下全局可用的字符可以使用于一个成员名的任何地方：

- Unicode 0061到007A，即”a”到“z”
- Unicode 0041到 005A，即”A“到”Z”
- Unicode 0030到0039，即“0”到“9”
- Unicode 0080及之后的字符（非ASCII Unicode字符，不建议、非URL安全字符）


Additionally, the following characters are allowed in member names, except as the
first or last character:

- U+002D HYPHEN-MINUS, "-"
- U+005F LOW LINE, "_"
- U+0020 SPACE, " " _(not recommended, not URL safe)_

#### <a href="#document-member-names-reserved-characters" id="document-member-names-reserved-characters" class="headerlink"></a> {%comment%}Reserved Characters{%endcomment%}保留字符

{%comment%}
The following characters **MUST NOT** be used in member names:
{%endcomment%}
以下字符不能用在成员名中：


- U+002B 加号, "+" _(用于排序)_
- U+002C 逗号, "," _(作为关联路径间的分隔符)_
- U+002E 句号, "." _(作为关联路径内的分隔符)_
- U+005B 左中括号, "[" _(用于稀疏字段中)_
- U+005D 右中括号, "]" _(用于稀疏字段中)_
- U+0021 感叹号, "!"
- U+0022 引号, '"'
- U+0023 数字符号（井号）, "#"
- U+0024 美元符, "$"
- U+0025 百分号, "%"
- U+0026 与号, "&"
- U+0027 省略号, "'"
- U+0028 左括号, "("
- U+0029 右括号, ")"
- U+002A 星号, "&#x2a;"
- U+002F 斜杠, "/"
- U+003A 冒号, ":"
- U+003B 分号, ";"
- U+003C 小于号, "<"
- U+003D 等号, "="
- U+003E 大于号, ">"
- U+003F 问号, "?"
- U+0040 商用AT符号, "@"
- U+005C 反斜杠, "\"
- U+005E 抑扬音符号, "^"
- U+0060 沉音符, "&#x60;"
- U+007B 左波形括号, "{"
- U+007C 竖线, "|"
- U+007D 右波形括号, "}"
- U+007E 波浪号, "~"
- U+007F 删除
- U+0000 到 U+001F (联合控制)
>译者注：以上符号均为英文中使用的符号。

{%comment%}
- U+002B PLUS SIGN, "+" _(used for ordering)_
- U+002C COMMA, "," _(used as a separator between relationship paths)_
- U+002E PERIOD, "." _(used as a separator within relationship paths)_
- U+005B LEFT SQUARE BRACKET, "[" _(used in sparse fieldsets)_
- U+005D RIGHT SQUARE BRACKET, "]" _(used in sparse fieldsets)_
- U+0021 EXCLAMATION MARK, "!"
- U+0022 QUOTATION MARK, '"'
- U+0023 NUMBER SIGN, "#"
- U+0024 DOLLAR SIGN, "$"
- U+0025 PERCENT SIGN, "%"
- U+0026 AMPERSAND, "&"
- U+0027 APOSTROPHE, "'"
- U+0028 LEFT PARENTHESIS, "("
- U+0029 RIGHT PARENTHESIS, ")"
- U+002A ASTERISK, "&#x2a;"
- U+002F SOLIDUS, "/"
- U+003A COLON, ":"
- U+003B SEMICOLON, ";"
- U+003C LESS-THAN SIGN, "<"
- U+003D EQUALS SIGN, "="
- U+003E GREATER-THAN SIGN, ">"
- U+003F QUESTION MARK, "?"
- U+0040 COMMERCIAL AT, "@"
- U+005C REVERSE SOLIDUS, "\"
- U+005E CIRCUMFLEX ACCENT, "^"
- U+0060 GRAVE ACCENT, "&#x60;"
- U+007B LEFT CURLY BRACKET, "{"
- U+007C VERTICAL LINE, "|"
- U+007D RIGHT CURLY BRACKET, "}"
- U+007E TILDE, "~"
- U+007F DELETE
- U+0000 to U+001F (C0 Controls)
{%endcomment%}

## <a href="#fetching" id="fetching" class="headerlink"></a> {%comment%}Fetching Data{%endcomment%}获取数据
{%comment%}
Data, including resources and relationships, can be fetched by sending a
`GET` request to an endpoint.
{%endcomment%}
数据，包括资源和关联，可以通过发送GET请求岛一个端点获取。

{%comment%}
Responses can be further refined with the optional features described below.
{%endcomment%}
响应可以细化为以下描述的可选的特性。

### <a href="#fetching-resources" id="fetching-resources" class="headerlink"></a> {%comment%}Fetching Resources{%endcomment%}获取资源

{%comment%}
A server **MUST** support fetching resource data for every URL provided as:
{%endcomment%}
服务器端必须支持获取如下提供给每个URL的资源数据：

{%comment%}
* a `self` link as part of the top-level links object
* a `self` link as part of a resource-level links object
* a `related` link as part of a relationship-level links object
{%endcomment%}

* 一个self链接作为顶层”links“对象的一部分
* 一个self链接作为资源层”links”对象的一部分
* 一个related链接作为关联层”links”对象的一部分

{%comment%}
For example, the following request fetches a collection of articles:
{%endcomment%}
例如，下列请求获取一个”articles”集合：

```http
GET /articles HTTP/1.1
Accept: application/vnd.api+json
```

{%comment%}
The following request fetches an article:
{%endcomment%}
以下请求获取一个“article”：

```http
GET /articles/1 HTTP/1.1
Accept: application/vnd.api+json
```

{%comment%}
And the following request fetches an article's author:
{%endcomment%}
而以下请求获取一个”article”的“author”：

```http
GET /articles/1/author HTTP/1.1
Accept: application/vnd.api+json
```

#### <a href="#fetching-resources-responses" id="fetching-resources-responses" class="headerlink"></a>{%comment%} Responses{%endcomment%}响应

##### <a href="#fetching-resources-responses-200" id="fetching-resources-responses-200" class="headerlink"></a> 200 OK

{%comment%}
A server **MUST** respond to a successful request to fetch an individual
resource or resource collection with a `200 OK` response.
{%endcomment%}
服务器必须回应“200 OK”响应一个成功的请求来获取单个资源或者资源集合。

{%comment%}
A server **MUST** respond to a successful request to fetch a resource
collection with an array of [resource objects] or an empty array (`[]`) as
the response document's primary data.
{%endcomment%}
服务器必须以一个资源对象数组或空数组作为响应文档的主要数据，响应一个成功的请求来获取一个资源集合。

{%comment%}
For example, a `GET` request to a collection of articles could return:
{%endcomment%}
例如，一个对“articles”集合的GET请求可以如此响应返回：

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "links": {
    "self": "http://example.com/articles"
  },
  "data": [{
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "JSON API paints my bikeshed!"
    }
  }, {
    "type": "articles",
    "id": "2",
    "attributes": {
      "title": "Rails is Omakase"
    }
  }]
}
```

{%comment%}
A similar response representing an empty collection would be:
{%endcomment%}
表明为一个空集合的相似响应应当是：

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "links": {
    "self": "http://example.com/articles"
  },
  "data": []
}
```

{%comment%}
A server **MUST** respond to a successful request to fetch an individual
resource with a [resource object][resource objects] or `null` provided as
the response document's primary data.
{%endcomment%}
服务器以一个资源对象或null作为响应文档的主要数据，响应一个成功的请求来获取单个资源。

{%comment%}
`null` is only an appropriate response when the requested URL is one that
might correspond to a single resource, but doesn't currently.
{%endcomment%}
当一个请求的URL对应一个资源，而这个资源目前没有时，`null`只是一个合理的响应

{%comment%}
> Note: Consider, for example, a request to fetch a to-one related resource link.
This request would respond with `null` when the relationship is empty (such that
the link is corresponding to no resources) but with the single related resource's
[resource object][resource objects] otherwise.

{%endcomment%}
> 注：例如，考虑请求到一个到相关资源的链接。当关联为空时，这个请求将回应null（这样对应的链接是没有资源的），但是单独相关的资源的resource对象则相反。

{%comment%}
For example, a `GET` request to an individual article could return:
{%endcomment%}
例如，一个对单个“article”的`GET`请求可以如下返回：

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "links": {
    "self": "http://example.com/articles/1"
  },
  "data": {
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "JSON API paints my bikeshed!"
    },
    "relationships": {
      "author": {
        "links": {
          "related": "http://example.com/articles/1/author"
        }
      }
    }
  }
}
```

{%comment%}
If the above article's author is missing, then a `GET` request to that related
resource would return:
{%endcomment%}
如果以上“article”的“author”丢失了，那么对那个相关资源的`GET`请求会如下返回：

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "links": {
    "self": "http://example.com/articles/1/author"
  },
  "data": null
}
```

##### <a href="#fetching-resources-responses-404" id="fetching-resources-responses-404" class="headerlink"></a> 404 Not Found

{%comment%}
A server **MUST** respond with `404 Not Found` when processing a request to
fetch a single resource that does not exist, except when the request warrants a
`200 OK` response with `null` as the primary data (as described above).
{%endcomment%}
当处理一个请求来获取不存在的单个资源时，除了保证一个以`null`为主要数据 `200 OK`响应外，服务器还必须以`404 Not found`响应，

##### <a href="#fetching-resources-responses-other" id="fetching-resources-responses-other" class="headerlink"></a>{%comment%} Other Responses{%endcomment%}其他响应

{%comment%}
A server **MAY** respond with other HTTP status codes.
{%endcomment%}
服务器 **可以** 以其他HTTP状态码响应。

{%comment%}
A server **MAY** include [error details] with error responses.
{%endcomment%}
服务器 **可以** 响应包含错误细节的错误。

{%comment%}
A server **MUST** prepare responses, and a client **MUST** interpret
responses, in accordance with
[`HTTP semantics`](http://tools.ietf.org/html/rfc7231).
{%endcomment%}
服务器 **必须** 准备响应，且客户端必须根据[HTTP语义](http://tools.ietf.org/html/rfc7231)解释响应。

### <a href="#fetching-relationships" id="fetching-relationships" class="headerlink"></a> {%comment%}Fetching Relationships{%endcomment%}获取关联

{%comment%}
A server **MUST** support fetching relationship data for every relationship URL
provided as a `self` link as part of a relationship's `links` object.
{%endcomment%}
服务器 **必须** 支持获取关联数据，包含`self`链接作为一个关联的`links`对象的一部分。

{%comment%}
For example, the following request fetches data about an article's comments:
{%endcomment%}
例如，以下获取对一个“article”的评论数据的请求：

```http
GET /articles/1/relationships/comments HTTP/1.1
Accept: application/vnd.api+json
```
{%comment%}
And the following request fetches data about an article's author:
{%endcomment%}
以下请求获取关于一个”article”的”author”的数据：

```http
GET /articles/1/relationships/author HTTP/1.1
Accept: application/vnd.api+json
```

#### <a href="#fetching-relationships-responses" id="fetching-relationships-responses" class="headerlink"></a> {%comment%}Responses{%endcomment%}响应

##### <a href="#fetching-relationships-responses-200" id="fetching-relationships-responses-200" class="headerlink"></a> 200 OK

{%comment%}
A server **MUST** respond to a successful request to fetch a relationship
with a `200 OK` response.
{%endcomment%}
服务器端b **必须** 回应以一个`200 OK`响应一个来获取关联的成功请求。

{%comment%}
The primary data in the response document **MUST** match the appropriate
value for [resource linkage], as described above for
[relationship objects][relationships].
{%endcomment%}
响应文档中的主要数据 **必须** 符合资源链接的合理值，对于[关联对象][relationships]的描述见上。

{%comment%}
The top-level [links object][links] **MAY** contain `self` and `related` links,
as described above for [relationship objects][relationships].
{%endcomment%}
顶层[链接对象][links] **可以** 包含`self`和`related`链接，对于[关联对象][relationships]的描述见上。

{%comment%}
For example, a `GET` request to a URL from a to-one relationship link could
return:
{%endcomment%}
例如，一个对来自单向关联链接的URL的`GET`请求可以返回：

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "links": {
    "self": "/articles/1/relationships/author",
    "related": "/articles/1/author"
  },
  "data": {
    "type": "people",
    "id": "12"
  }
}
```

{%comment%}
If the above relationship is empty, then a `GET` request to the same URL would
return:
{%endcomment%}
如果以上关联是空的，那么到相同URL的一个`GET`请求将如下返回：

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "links": {
    "self": "/articles/1/relationships/author",
    "related": "/articles/1/author"
  },
  "data": null
}
```

{%comment%}
A `GET` request to a URL from a to-many relationship link could return:
{%endcomment%}
从对多关联链接`GET`请求到URL发送可以返回 ︰

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "links": {
    "self": "/articles/1/relationships/tags",
    "related": "/articles/1/tags"
  },
  "data": [
    { "type": "tags", "id": "2" },
    { "type": "tags", "id": "3" }
  ]
}
```

{%comment%}
If the above relationship is empty, then a `GET` request to the same URL would
return:
{%endcomment%}
如果以上的关联为空，则对同一个URL 发送`GET`请求将返回︰

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "links": {
    "self": "/articles/1/relationships/tags",
    "related": "/articles/1/tags"
  },
  "data": []
}
```

##### <a href="#fetching-relationships-responses-404" id="fetching-relationships-responses-404" class="headerlink"></a> 404 Not Found

{%comment%}
A server **MUST** return `404 Not Found` when processing a request to fetch
a relationship link URL that does not exist.
{%endcomment%}
服务器必须返回404 Not Found来处理获取不存在关联链接URL的请求。

{%comment%}
> Note: This can happen when the parent resource of the relationship
does not exist. For example, when `/articles/1` does not exist, request to`/articles/1/relationships/tags` returns `404 Not Found`.

{%endcomment%}
> 注︰这可以发生在关联的父资源不存在时。例如，当`/articles/1`不存在，请求`/articles/1/relationships/tags`时就会返回`404 Not Found`.


{%comment%}
If a relationship link URL exists but the relationship is empty, then
`200 OK` **MUST** be returned, as described above.
{%endcomment%}
如果关联链接URL存在，但关联是空的，那么 **必须** 返回`200 OK` ，如上文所述。

##### <a href="#fetching-relationships-responses-other" id="fetching-relationships-responses-other" class="headerlink"></a> {%comment%}Other Responses{%endcomment%}其他响应

{%comment%}
A server **MAY** respond with other HTTP status codes.
{%endcomment%}
服务器 **可以** 用其他 HTTP 状态代码作为响应。

{%comment%}
A server **MAY** include [error details] with error responses.
{%endcomment%}
服务器 **可以** 使用错误响应包含错误的详细信息。

{%comment%}
A server **MUST** prepare responses, and a client **MUST** interpret
responses, in accordance with
[`HTTP semantics`](http://tools.ietf.org/html/rfc7231).
{%endcomment%}
服务器 **必须** 准备响应，且客户端 **必须** 根据[HTTP语义](http://tools.ietf.org/html/rfc7231)解释响应。

### <a href="#fetching-includes" id="fetching-includes" class="headerlink"></a>{%comment%} Inclusion of Related Resources{%endcomment%}相关资源的包含

{%comment%}
An endpoint **MAY** return resources related to the primary data by default.
{%endcomment%}
端点n **可以** 返回默认情况下与主数据相联的资源。

{%comment%}
An endpoint **MAY** also support an `include` request parameter to allow the
client to customize which related resources should be returned.
{%endcomment%}
端点 **可能** 还支持`included`请求参数，以允许客户端应该返回自定义的相关资源。

{%comment%}
If an endpoint does not support the `include` parameter, it **MUST** respond
with `400 Bad Request` to any requests that include it.
{%endcomment%}
如果端点不支持`included`参数，那么任何包含它的请求，服务器 **必须** 响应`400 Bad Request`。

{%comment%}
If an endpoint supports the `include` parameter and a client supplies it,
the server **MUST NOT** include unrequested [resource objects] in the `included`
section of the [compound document].
{%endcomment%}
如果端点支持`included`参数并且由客户端提供它，服务器在[复合文档]中`included`节中 **不能** 包含未请求的[资源对象].

{%comment%}
The value of the `include` parameter **MUST** be a comma-separated (U+002C
COMMA, ",") list of relationship paths. A relationship path is a dot-separated
(U+002E FULL-STOP, ".") list of [relationship][relationships] names.
{%endcomment%}
`include`参数的值 **必须** 是英文逗号分隔 （U+002C + 逗号,"，"） 的关联路径列表。关联路径是圆点分隔 (U + 002E 句号,".") 的[关联][relationships]名称列表。

{%comment%}
If a server is unable to identify a relationship path or does not support
inclusion of resources from a path, it **MUST** respond with 400 Bad Request.
{%endcomment%}
如果服务器无法识别关联路径或不支持从一个路径包含资源，它 **必须** 做出`400 Bad Request`响应。

{%comment%}
> Note: For example, a relationship path could be `comments.author`, where
`comments` is a relationship listed under a `articles` [resource object][resource objects], and
`author` is a relationship listed under a `comments` [resource object][resource objects].

{%endcomment%}
>注︰例如，关联路径可以是`comments.author`，其中`comments`是`articles`[资源对象][resource objects]下列出的关联, `author`是`comments`[资源对象][resource objects]下列出的关联。

{%comment%}
For instance, comments could be requested with an article:
{%endcomment%}
例如，“comments”可以请求一篇“article”︰

```http
GET /articles/1?include=comments HTTP/1.1
Accept: application/vnd.api+json
```

{%comment%}
In order to request resources related to other resources, a dot-separated path
for each relationship name can be specified:
{%endcomment%}
为了请求资源可以关联到其他资源，可以用圆点分隔的路径指定每个关系名称︰

```http
GET /articles/1?include=comments.author HTTP/1.1
Accept: application/vnd.api+json
```

{%comment%}
> Note: Because [compound documents][compound document] require full linkage
(except when relationship linkage is excluded by sparse fieldsets), intermediate
resources in a multi-part path must be returned along with the leaf nodes. For
example, a response to a request for `comments.author` should include `comments`
as well as the `author` of each of those `comments`.

{%endcomment%}
> 注︰由于[复合文档][compound document]需要充分联动 （除了当关联联动不被由稀疏的字段集所包含），多部分路径的中间资源必须伴随着叶节点返回。例如， `comments.author`请求的响应应该包括`comments`，以及`author`，对于每个`comments`都一样.

{%comment%}
> Note: A server may choose to expose a deeply nested relationship such as
`comments.author` as a direct relationship with an alias such as
`comment-authors`. This would allow a client to request
`/articles/1?include=comment-authors` instead of
`/articles/1?include=comments.author`. By abstracting the nested
relationship with an alias, the server can still provide full linkage in
compound documents without including potentially unwanted intermediate
resources.

{%endcomment%}
> 注︰服务器可以选择公开嵌套很深的关联如`comments.author`作为一个直接关联`comment-authors`的别名。这将允许客户端请求`/articles/1?include=comment-authors`而不是`/articles/1?include=comments.author`。通过抽象与别名办税伴随嵌套的关联，服务器仍能提供在复合文档中的充分联动，且不包含可能有害的中间资源。

{%comment%}
Multiple related resources can be requested in a comma-separated list:
{%endcomment%}
多个相关的资源可以以逗号分隔的列表作为请求︰

```http
GET /articles/1?include=author,comments.author HTTP/1.1
Accept: application/vnd.api+json
```

{%comment%}
Furthermore, related resources can be requested from a relationship endpoint:
{%endcomment%}
此外，相关的资源可以从关联端点请求︰

```http
GET /articles/1/relationships/comments?include=comments.author HTTP/1.1
Accept: application/vnd.api+json
```

{%comment%}
In this case, the primary data would be a collection of
[resource identifier objects][resource identifier object] that represent linkage to comments for an article,
while the full comments and comment authors would be returned as included data.
{%endcomment%}
在这种情况下，主数据将代表关联到文章的评论的[资源标识符对象][resource identifier object]集合，而完整的评论和评论作者将作为包含数据返回。

{%comment%}
> Note: This section applies to any endpoint that responds with primary
data, regardless of the request type. For instance, a server could support
the inclusion of related resources along with a `POST` request to create a
resource or relationship.

{%endcomment%}
>注︰本条适用于任何以主数据作为响应，而不考虑请求类型的端点。例如，服务器可以支持包含的相关资源伴随一个`POST`请求去创建资源或关联。

### <a href="#fetching-sparse-fieldsets" id="fetching-sparse-fieldsets" class="headerlink"></a>{%comment%} Sparse Fieldsets{%endcomment%}稀疏字段集

{%comment%}
A client **MAY** request that an endpoint return only specific [fields] in the
response on a per-type basis by including a `fields[TYPE]` parameter.
{%endcomment%}
客户端 **可以** 请求一个端点返回，只能在响应中每种类型的基础上指定[fields]中包含特定`fields[TYPE]`参数。

{%comment%}
The value of the `fields` parameter **MUST** be a comma-separated (U+002C
COMMA, ",") list that refers to the name(s) of the fields to be returned.
{%endcomment%}
`fields`的值 **必须** 是用逗号分隔（U +002 C + 逗号,"，"）的列表 ，该列表指的是要返回字段的名称列表。

{%comment%}
If a client requests a restricted set of [fields] for a given resource type,
an endpoint **MUST NOT** include additional [fields] in resource objects of
that type in its response.
{%endcomment%}
如果客户端请求一组限定资源类型的[fields]，端点的响应 **不能** 包含附加[fields]在该类型的资源对象中。

```http
GET /articles?include=author&fields[articles]=title,body&fields[people]=name HTTP/1.1
Accept: application/vnd.api+json
```

{%comment%}
>Note: The above example URI shows unencoded `[` and `]` characters simply for
readability. In practice, these characters must be percent-encoded, per the
requirements [in RFC 3986](http://tools.ietf.org/html/rfc3986#section-3.4).

{%endcomment%}
>注︰上面的示例中的 URI 显示未编码的`[`和 `]`字符只是为了提高可读性。在实践中，这些字符必须是百分号编码的，将根据在 [RFC 3986](http://tools.ietf.org/html/rfc3986#section-3.4)的要求.

{%comment%}
> Note: This section applies to any endpoint that responds with resources as
primary or included data, regardless of the request type. For instance, a
server could support sparse fieldsets along with a `POST` request to create
a resource.

{%endcomment%}
>注︰本条适用于任何以资源作为主体或包含数据，而不是考虑请求类型的端点。例如，服务器可以支持稀疏字段集伴随着一个`POST`请求去创建资源。

### <a href="#fetching-sorting" id="fetching-sorting" class="headerlink"></a> {%comment%}Sorting{%endcomment%}排序

{%comment%}
A server **MAY** choose to support requests to sort resource collections
according to one or more criteria ("sort fields").
{%endcomment%}
服务器 **可以** 支持对请求根据一个或多个标准（"排序字段"）对资源集合进行排序。

{%comment%}
> Note: Although recommended, sort fields do not necessarily need to
correspond to resource attribute and association names.

{%endcomment%}
> 注︰尽管推荐，但是排序字段对于资源属性和关联的名称来说不是必需的。

{%comment%}
> Note: It is recommended that dot-separated (U+002E FULL-STOP, ".") sort
fields be used to request sorting based upon relationship attributes. For
example, a sort field of `author.name` could be used to request that the
primary data be sorted based upon the `name` attribute of the `author`
relationship.

{%endcomment%}
> 注︰建议用圆点分隔 (U + 002E 句号,".") 排序字段来请求基于关联属性的排序。例如， `author.name`排序字段可以用于对请求的主数据进行根据`author`关系的`name`属性的排序。

{%comment%}
An endpoint **MAY** support requests to sort the primary data with a `sort`
query parameter. The value for `sort` **MUST** represent sort fields.
{%endcomment%}
端点 **可以** 支持通过包含`sort`的查询参数请求进行主要数据的排序。`sort` **必须** 代表排序字段的值。

```http
GET /people?sort=age HTTP/1.1
Accept: application/vnd.api+json
```

{%comment%}
An endpoint **MAY** support multiple sort fields by allowing comma-separated
(U+002C COMMA, ",") sort fields. Sort fields **SHOULD** be applied in the
order specified.
{%endcomment%}
端点 **可以** 支持多个排序字段，通过允许逗号分隔 （U+002 C + 逗号, "，"） 排序字段。排序字段 **应该** 应用于指定排序。

```http
GET /people?sort=age,name HTTP/1.1
Accept: application/vnd.api+json
```

{%comment%}
The sort order for each sort field **MUST** be ascending unless it is prefixed
with a minus (U+002D HYPHEN-MINUS, "-"), in which case it **MUST** be descending.
{%endcomment%}
每个排序字段的排序顺序 **必须** 升序，除非它以减号作为前缀 (U + 002D 连字符-负号，"-")，在这种情况下它 **必须** 降序。

```http
GET /articles?sort=-created,title HTTP/1.1
Accept: application/vnd.api+json
```

{%comment%}
The above example should return the newest articles first. Any articles
created on the same date will then be sorted by their title in ascending
alphabetical order.
{%endcomment%}
上面的例子应该先返回最新的"articles"。在同一日期上创建的任何"articles"将会按其"title"按字母顺序升序排序。

{%comment%}
If the server does not support sorting as specified in the query parameter
`sort`, it **MUST** return `400 Bad Request`.
{%endcomment%}
如果服务器不支持指定查询参数`sort`，它必须返回4`00 Bad Request`响应。

{%comment%}
If sorting is supported by the server and requested by the client via query
parameter `sort`, the server **MUST** return elements of the top-level
`data` array of the response ordered according to the criteria specified.
The server **MAY** apply default sorting rules to top-level `data` if
request parameter `sort` is not specified.
{%endcomment%}
如果排序是服务器所支持的并且查询参数`sort`通过客户端请求，服务器 **必须** 返回根据指定条件排序的顶层元素`data`数组响应。如果请求不指定`sort`参数，服务器 **可以** 应用默认排序规则对顶级`data`进行排序。

{%comment%}
> Note: This section applies to any endpoint that responds with a resource
collection as primary data, regardless of the request type.

{%endcomment%}
>注︰本条适用于任何以资源集合作为主要数据，而不考虑请求类型的响应。

### <a href="#fetching-pagination" id="fetching-pagination" class="headerlink"></a>{%comment%} Pagination{%endcomment%}分页

{%comment%}
A server **MAY** choose to limit the number of resources returned in a response
to a subset ("page") of the whole set available.
{%endcomment%}
为了让整个集合的一个子集 （"页面"） 可用，服务器 **可以** 选择限制响应中返回的资源数量。

{%comment%}
A server **MAY** provide links to traverse a paginated data set ("pagination
links").
{%endcomment%}
服务器 **可以** 提供链接来遍历一个分页的数据集 （"分页链接"）。

{%comment%}
Pagination links **MUST** appear in the links object that corresponds to a
collection. To paginate the primary data, supply pagination links in the
top-level `links` object. To paginate an included collection returned in
a [compound document], supply pagination links in the corresponding links
object.
{%endcomment%}
分页链接 **必须** 出现在对应于一个集合的链接对象中。为了对主要数据进行分页，在顶级`links`对象中提供分页链接。要分页在返回的复合文档中所包含的集合，提供分页链接中对应的链接对象。

{%comment%}
The following keys **MUST** be used for pagination links:

* `first`: the first page of data
* `last`: the last page of data
* `prev`: the previous page of data
* `next`: the next page of data
{%endcomment%}
分页链接对象 **必须** 用以下关键字︰

* `first`︰ 数据的第一页
* `last`︰ 数据的最后一页
* `prev`︰ 数据的前一页
* `next`︰ 数据的下一个页面

{%comment%}
Keys **MUST** either be omitted or have a `null` value to indicate that a
particular link is unavailable.
{%endcomment%}
关键词 **必须** 省略或者为空值，以表示特定的链接是不可用的。

{%comment%}
Concepts of order, as expressed in the naming of pagination links, **MUST**
remain consistent with JSON API's [sorting rules](#fetching-sorting).
{%endcomment%}
正如表示在分页链接的命名中那样，顺序的概念 **必须** 与JSON API[排序规则](#fetching-sorting)保持一致。

{%comment%}
The `page` query parameter is reserved for pagination. Servers and clients
**SHOULD** use this key for pagination operations.
{%endcomment%}
`page`查询参数是为分页保留的。服务器和客户端 **应该** 将此关键词用于分页操作。

{%comment%}
> Note: JSON API is agnostic about the pagination strategy used by a server.
Effective pagination strategies include (but are not limited to):
page-based, offset-based, and cursor-based. The `page` query parameter can
be used as a basis for any of these strategies. For example, a page-based
strategy might use query parameters such as `page[number]` and `page[size]`,
an offset-based strategy might use `page[offset]` and `page[limit]`, while a
cursor-based strategy might use `page[cursor]`.

{%endcomment%}
注︰JSON API与服务器所使用的分页策略无关。有效的分页策略包括 （但不是限于）︰基于页面的、基于偏移量，和基于指针的。`page`查询参数可以用作任何这些策略的基础。例如，基于页面的策略可能会使用查询参数，如`page[number]`和`page[size]`，偏移量为基础的策略可能使用`page[offset]`和`page[limit]`，而基于游标的策略可能使用`page[cursor]`.

{%comment%}
> Note: The example query parameters above use unencoded `[` and `]` characters
simply for readability. In practice, these characters must be percent-encoded,
per the requirements in [RFC 3986](http://tools.ietf.org/html/rfc3986#section-3.4).

{%endcomment%}
注︰上面的示例查询参数使用未编码的`[`和`]`字符只是为了提高可读性。在实践中，这些字符必须是%编码，将根据在[RFC 3986](http://tools.ietf.org/html/rfc3986#section-3.4)的要求。

{%comment%}
> Note: This section applies to any endpoint that responds with a resource
collection as primary data, regardless of the request type.

{%endcomment%}
>注︰本条适用于任何以资源集合作为主要数据，而不考虑请求类型的响应。

### <a href="#fetching-filtering" id="fetching-filtering" class="headerlink"></a> {%comment%}Filtering{%endcomment%}过滤

{%comment%}
The `filter` query parameter is reserved for filtering data. Servers and clients
**SHOULD** use this key for filtering operations.
{%endcomment%}
`filter`查询参数为筛选数据保留。服务器和客户端 **应该** 使用这个关键词进行过滤操作。

{%comment%}
> Note: JSON API is agnostic about the strategies supported by a server. The
`filter` query parameter can be used as the basis for any number of filtering
strategies.

{%endcomment%}
注︰JSON API与服务器所支持的策略无关。`filter`查询参数可以作为任意数量的过滤策略的基础。

## <a href="#crud" id="crud" class="headerlink"></a> {%comment%}Creating, Updating and Deleting Resources{%endcomment%}创建、更新和删除资源

{%comment%}
A server **MAY** allow resources of a given type to be created. It **MAY**
also allow existing resources to be modified or deleted.
{%endcomment%}
服务器可以允许创建给定类型的资源。它也可以允许对现有的资源进行修改或删除。

{%comment%}
A request **MUST** completely succeed or fail (in a single "transaction"). No
partial updates are allowed.
{%endcomment%}
请求必须是完全成功或失败（在单次"处理"中）。不允许部分更新。

{%comment%}
> Note: The `type` member is required in every [resource object][resource objects] throughout requests and
responses in JSON API. There are some cases, such as when `POST`ing to an
endpoint representing heterogenous data, when the `type` could not be inferred
from the endpoint. However, picking and choosing when it is required would be
confusing; it would be hard to remember when it was required and when it was
not. Therefore, to improve consistency and minimize confusion, `type` is
always required.

{%endcomment%}
注︰`type`被要求贯穿在整个JSON API请求和响应的每一个[资源对象][resource objects]中。某些情况下，如正在`POST`到一个代表易购数据的端点时，无法从断点推断类型时，当它需要挑选和选择时会困惑，它会难以记住何时需要，何时不需要。因此，需要`type`提高一致性，减少迷惑性。

### <a href="#crud-creating" id="crud-creating" class="headerlink"></a> {%comment%}Creating Resources{%endcomment%}创建资源

{%comment%}
A resource can be created by sending a `POST` request to a URL that represents
a collection of resources. The request **MUST** include a single [resource object][resource objects]
as primary data. The [resource object][resource objects] **MUST** contain at least a `type` member.
{%endcomment%}
通过发送`POST`请求到资源集合的URL，可以创建一个资源。这个请求 **必须** 包含一个单独的[资源对象][resource objects]作为主要数据。这个[资源对象][resource objects] **必须** 包含至少一个`type`成员。

{%comment%}
For instance, a new photo might be created with the following request:
{%endcomment%}
例如，以下的请求可能会创建一张新"photo"︰

```http
POST /photos HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": {
    "type": "photos",
    "attributes": {
      "title": "Ember Hamster",
      "src": "http://example.com/images/productivity.png"
    },
    "relationships": {
      "photographer": {
        "data": { "type": "people", "id": "9" }
      }
    }
  }
}
```

{%comment%}
If a relationship is provided in the `relationships` member of the
[resource object][resource objects], its value **MUST** be a relationship object with a `data`
member. The value of this key represents the linkage the new resource is to
have.
{%endcomment%}
如果[资源对象][resource objects]中的`relationships`成员被提供了一种关联，那么它的值 **必须** 是一个关联对象与一个`data`成员。此关键字的值代表新的资源是有关联的。

#### <a href="#crud-creating-client-ids" id="crud-creating-client-ids" class="headerlink"></a>{%comment%} Client-Generated IDs{%endcomment%}客户端生成的ID

{%comment%}
A server **MAY** accept a client-generated ID along with a request to create
a resource. An ID **MUST** be specified with an `id` key, the value of
which **MUST** be a universally unique identifier. The client **SHOULD** use
a properly generated and formatted *UUID* as described in RFC 4122
[[RFC4122](http://tools.ietf.org/html/rfc4122.html)].
{%endcomment%}
服务器 **可以** 接受客户端生成的ID伴随在请求中去创建资源。ID **必须** 指定`id`关键词，它的值 **必须** 是一个通用唯一标识符。客户端应该使用正确生成和格式化的 *UUID* 如RFC 4122 [[RFC4122](http://tools.ietf.org/html/rfc4122.html)]中所述.

{%comment%}
> NOTE: In some use-cases, such as importing data from another source, it
may be possible to use something other than a UUID that is still guaranteed
to be globally unique. Do not use anything other than a UUID unless you are
100% confident that the strategy you are using indeed generates globally
unique identifiers.

For example:
{%endcomment%}
>注︰在一些用例，如从其他源导入数据中有可能使用其他代替UUID，但仍然保证是全局唯一的。请不要使用 UUID 之外的任何内容，除非你100%的确定你正在使用的策略确实生成全局唯一标识符。

例如︰

```http
POST /photos HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": {
    "type": "photos",
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "attributes": {
      "title": "Ember Hamster",
      "src": "http://example.com/images/productivity.png"
    }
  }
}
```
{%comment%}
A server **MUST** return `403 Forbidden` in response to an unsupported request
to create a resource with a client-generated ID.
{%endcomment%}
服务器 **必须** 返回`403 Forbidden`以响应不支持使用客户端生成的 ID请求创建资源。

#### <a href="#crud-creating-responses" id="crud-creating-responses" class="headerlink"></a> {%comment%}Responses{%endcomment%}响应

##### <a href="#crud-creating-responses-201" id="crud-creating-responses-201" class="headerlink"></a> 201 Created

{%comment%}
If a `POST` request did not include a [Client-Generated
ID](#crud-creating-client-ids) and the requested resource has been created
successfully, the server **MUST** return a `201 Created` status code.
{%endcomment%}
如果一个`POST`请求不包含一个客户端生成的ID并且所请求的资源已成功创建过的，服务器 **必须** 返回一个`201 Created`状态代码。

{%comment%}
The response **SHOULD** include a `Location` header identifying the location
of the newly created resource.
{%endcomment%}
响应 **应该** 包含确定新创建资源位置的Location标头。

{%comment%}
The response **MUST** also include a document that contains the primary
resource created.
{%endcomment%}
响应 **必须** 还包含创建主要资源的文档。

{%comment%}
If the [resource object][resource objects] returned by the response contains a `self` key in its
`links` member and a `Location` header is provided, the value of the `self`
member **MUST** match the value of the `Location` header.
{%endcomment%}
如果返回响应的资源对象在其`links`成员中包含`self`关键词，并且提供`Location`标头，s`elf`成员的值 **必须** 与`Location`标头的值相一致。

```http
HTTP/1.1 201 Created
Location: http://example.com/photos/550e8400-e29b-41d4-a716-446655440000
Content-Type: application/vnd.api+json

{
  "data": {
    "type": "photos",
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "attributes": {
      "title": "Ember Hamster",
      "src": "http://example.com/images/productivity.png"
    },
    "links": {
      "self": "http://example.com/photos/550e8400-e29b-41d4-a716-446655440000"
    }
  }
}
```

##### <a href="#crud-creating-responses-202" id="crud-creating-responses-202" class="headerlink"></a> 202 Accepted

{%comment%}
If a request to create a resource has been accepted for processing, but the
processing has not been completed by the time the server responds, the
server **MUST** return a `202 Accepted` status code.
{%endcomment%}
如果要创建资源的请求已被接受处理，但处理尚未完成的时候，服务器 **必须** 返回`202 Accepted`状态代码。

##### <a href="#crud-creating-responses-204" id="crud-creating-responses-204" class="headerlink"></a> 204 No Content

{%comment%}
If a `POST` request *did* include a [Client-Generated
ID](#crud-creating-client-ids) and the requested resource has been created
successfully, the server **MUST** return either a `201 Created` status code
and response document (as described above) or a `204 No Content` status code
with no response document.
{%endcomment%}
如果`POST`请求包括一个客户端生成的 ID 并且所请求的资源已成功创建，服务器 **必须** 返回`201 Created`状态代码和响应文档 （如上文所述） 或`204 No Content`状态代码不响应文档。

{%comment%}
> Note: If a `204` response is received the client should consider the resource
object sent in the request to be accepted by the server, as if the server
had returned it back in a `201` response.

{%endcomment%}
> 注︰如果收到`204`响应，客户端应该认为发送请求的资源对象已经被服务器所接受，就好像服务器已经在`201`响应中返回了。

##### <a href="#crud-creating-responses-403" id="crud-creating-responses-403" class="headerlink"></a> 403 Forbidden

{%comment%}
A server **MAY** return `403 Forbidden` in response to an unsupported request
to create a resource.
{%endcomment%}
服务器 **可以** 对不支持创建资源的请求来响应`403 Forbidden`。

##### <a href="#crud-creating-responses-409" id="crud-creating-responses-409" class="headerlink"></a> 409 Conflict

{%comment%}
A server **MUST** return `409 Conflict` when processing a `POST` request to
create a resource with a client-generated ID that already exists.
{%endcomment%}
服务器 **必须** 返回`409 Conflict`来处理通过一个已经存在的客户端生成ID 来发送`POST`请求创建资源的情况。

{%comment%}
A server **MUST** return `409 Conflict` when processing a `POST` request in
which the [resource object][resource objects]'s `type` is not among the type(s) that constitute the
collection represented by the endpoint.
{%endcomment%}
服务器 **必须** 返回`409 Conflict`处理`POST`请求资源对象的`type`不是构成该端点所表示的集合类型的情况。

{%comment%}
A server **SHOULD** include error details and provide enough information to
recognize the source of the conflict.
{%endcomment%}
服务器 **应该** 包含错误详细信息并提供足够的信息来识别冲突的来源。

##### <a href="#crud-creating-responses-other" id="crud-creating-responses-other" class="headerlink"></a> {%comment%}Other Responses{%endcomment%}其他响应

{%comment%}
A server **MAY** respond with other HTTP status codes.

A server **MAY** include [error details] with error responses.

A server **MUST** prepare responses, and a client **MUST** interpret
responses, in accordance with
[`HTTP semantics`](http://tools.ietf.org/html/rfc7231).
{%endcomment%}
服务器 **可以** 用其他 HTTP 状态代码作为响应。
服务器 **可以** 使用包含错误的详细信息的错误响应。
服务器 **必须** 准备响应，且客户端 **必须** 根据[HTTP语义](http://tools.ietf.org/html/rfc7231)解释响应。

### <a href="#crud-updating" id="crud-updating" class="headerlink"></a> {%comment%}Updating Resources{%endcomment%}更新资源

{%comment%}
A resource can be updated by sending a `PATCH` request to the URL that
represents the resource.
{%endcomment%}
可以通过发送`PATCH`请求到代表资源的URL更新一个资源。

{%comment%}
The URL for a resource can be obtained in the `self` link of the resource
object. Alternatively, when a `GET` request returns a single [resource object][resource objects] as
primary data, the same request URL can be used for updates.
{%endcomment%}
资源URL可以通过[资源对象][resource objects]中的`self`链接获得。或者，当`GET`请求中返回单独的资源对象作为主数据时，相同URL请求可以用于更新。

{%comment%}
The `PATCH` request **MUST** include a single [resource object][resource objects] as primary data.
The [resource object][resource objects] **MUST** contain `type` and `id` members.

For example:
{%endcomment%}
`PATCH`请求 **必须** 包含单一的[资源对象][resource objects]作为主要数据。[资源对象][resource objects] **必须** 包含`type`和`id`的成员。

例如：

```http
PATCH /articles/1 HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": {
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "To TDD or Not"
    }
  }
}
```

#### <a href="#crud-updating-resource-attributes" id="crud-updating-resource-attributes" class="headerlink"></a> {%comment%}Updating a Resource's Attributes{%endcomment%}更新资源属性

{%comment%}
Any or all of a resource's [attributes] **MAY** be included in the resource
object included in a `PATCH` request.
{%endcomment%}
资源的任何或所有的[attributes] **可以** 包含在`PATCH`请求的资源对象中。

{%comment%}
If a request does not include all of the [attributes] for a resource, the server
**MUST** interpret the missing [attributes] as if they were included with their
current values. The server **MUST NOT** interpret missing attributes as `null`
values.
{%endcomment%}
如果请求不包含资源的所有[attributes]，服务器必须解释所缺少的[attributes]，就好像它们已经被包含，附带其当前值。服务器不能将`null`解释为缺少属性的值。

{%comment%}
For example, the following `PATCH` request is interpreted as a request to
update only the `title` and `text` attributes of an article:
{%endcomment%}
例如，以下`PATCH`请求被解释为一个只更新"article"的`title`和`text`属性的请求︰

```http
PATCH /articles/1 HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": {
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "To TDD or Not",
      "text": "TLDR; It's complicated... but check your test coverage regardless."
    }
  }
}
```

#### <a href="#crud-updating-resource-relationships" id="crud-updating-resource-relationships" class="headerlink"></a> {%comment%}Updating a Resource's Relationships{%endcomment%}更新资源关联

{%comment%}
Any or all of a resource's [relationships] **MAY** be included in the resource
object included in a `PATCH` request.
{%endcomment%}
资源的任何或所有relationships **可以** 包含在PATCH请求中的资源对象中。

{%comment%}
If a request does not include all of the [relationships] for a resource, the server
**MUST** interpret the missing [relationships] as if they were included with their
current values. It **MUST NOT** interpret them as `null` or empty values.
{%endcomment%}
如果请求没有包含资源的所有关联，服务器 **必须** 解释缺失关联与其当前值，就好像包含了一样。服务器 **不能** 把它们解释为空或空值。

{%comment%}
If a relationship is provided in the `relationships` member of a resource
object in a `PATCH` request, its value **MUST** be a relationship object
with a `data` member. The relationship's value will be replaced with the
value specified in this member.
{%endcomment%}
如果资源对象在PATCH请求中为关联成员提供了关联，它的值必须是一个数据成员的关联对象。该关联的值将被替换为该成员所指定的值。

{%comment%}
For instance, the following `PATCH` request will update the `author` relationship of an article:
{%endcomment%}
例如，以下的`PATCH`请求将更新"article"的`author`关联：

```http
PATCH /articles/1 HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": {
    "type": "articles",
    "id": "1",
    "relationships": {
      "author": {
        "data": { "type": "people", "id": "1" }
      }
    }
  }
}
```

{%comment%}
Likewise, the following `PATCH` request performs a complete replacement of
the `tags` for an article:
{%endcomment%}
同样，下面的PATCH请求执行一个"article"的`tags`的完全替换：

```http
PATCH /articles/1 HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": {
    "type": "articles",
    "id": "1",
    "relationships": {
      "tags": {
        "data": [
          { "type": "tags", "id": "2" },
          { "type": "tags", "id": "3" }
        ]
      }
    }
  }
}
```

{%comment%}
A server **MAY** reject an attempt to do a full replacement of a to-many
relationship. In such a case, the server **MUST** reject the entire update,
and return a `403 Forbidden` response.
{%endcomment%}
服务器 **可以** 拒绝执行完全的一到多关联的替换。在这种情况下，服务器 **必须** 拒绝整个更新，并返回一个`403 Forbidden`响应信号。

{%comment%}
> Note: Since full replacement may be a very dangerous operation, a server
may choose to disallow it. For example, a server may reject full replacement if
it has not provided the client with the full list of associated objects, and
does not want to allow deletion of records the client has not seen.

{%endcomment%}
> 注：因为完全替换可能是一个非常危险的操作，服务器可以选择禁用它。例如，如果未提供客户端相关对象的完整列表，则服务器可以拒绝完全替换，而且不会允许删除客户端没有的记录。

#### <a href="#crud-updating-responses" id="crud-updating-responses" class="headerlink"></a>{%comment%} Responses{%endcomment%}响应

##### <a href="#crud-updating-responses-202" id="crud-updating-responses-202" class="headerlink"></a> 202 Accepted

{%comment%}
If an update request has been accepted for processing, but the processing
has not been completed by the time the server responds, the server **MUST**
return a `202 Accepted` status code.
{%endcomment%}
如果更新请求已被接受处理，但是服务器响应期间的处理还没有结束，服务器 **必须** 返回一个`202 Accepted`状态码。

##### <a href="#crud-updating-responses-200" id="crud-updating-responses-200" class="headerlink"></a> 200 OK

{%comment%}
If a server accepts an update but also changes the resource(s) in ways other
than those specified by the request (for example, updating the `updated-at`
attribute or a computed `sha`), it **MUST** return a `200 OK` response. The
response document **MUST** include a representation of the updated
resource(s) as if a `GET` request was made to the request URL.
{%endcomment%}
如果服务器接受了更新，但也（除按请求所指定的方式）对资源做了修改（例如，更新`updated-at`属性或计算sha值),它 **必须** 返回一个`200 OK`响应。响应文档 **必须** 包括已更新资源实例，就好像向请求的URL发出了`GET`请求。

{%comment%}
A server **MUST** return a `200 OK` status code if an update is successful,
the client's current attributes remain up to date, and the server responds
only with top-level [meta] data. In this case the server **MUST NOT**
include a representation of the updated resource(s).
{%endcomment%}
如果更新成功，服务器 **必须** 返回一个`200 OK`的状态码，保持客户端的当前属性为最新，服务器只响应顶层的元[meta]数据。这种情况下，服务器 **不能** 包含已更新资源的实例。

##### <a href="#crud-updating-responses-204" id="crud-updating-responses-204" class="headerlink"></a> 204 No Content

{%comment%}
If an update is successful and the server doesn't update any attributes besides
those provided, the server **MUST** return either a `200 OK` status code and
response document (as described above) or a `204 No Content` status code with no
response document.
{%endcomment%}
如果更新成功且服务器没有更新除提供更新之外的任何属性，服务器 **必须** 返回`200 OK`状态码和响应文档（如上所述）或 没有响应文档的`204 No Content`状态码。

##### <a href="#crud-updating-relationship-responses-403" id="crud-updating-relationship-responses-403" class="headerlink"></a> 403 Forbidden

{%comment%}
A server **MUST** return `403 Forbidden` in response to an unsupported request
to update a resource or relationship.
{%endcomment%}
服务器 **必须** 返回`403 Forbidden`来响应一个不支持更新资源或关联的请求。

##### <a href="#crud-updating-responses-404" id="crud-updating-responses-404" class="headerlink"></a> 404 Not Found

{%comment%}
A server **MUST** return `404 Not Found` when processing a request to modify
a resource that does not exist.
{%endcomment%}
当处理修改不存在的资源请求时，服务器 **必须** 返回`404 Not Found`。

{%comment%}
A server **MUST** return `404 Not Found` when processing a request that
references a related resource that does not exist.
{%endcomment%}
当处理引用不存在的相关资源请求时，服务器 **必须** 返回`404 Not Found`。

##### <a href="#crud-updating-responses-409" id="crud-updating-responses-409" class="headerlink"></a> 409 Conflict

{%comment%}
A server **MAY** return `409 Conflict` when processing a `PATCH` request to
update a resource if that update would violate other server-enforced
constraints (such as a uniqueness constraint on a property other than `id`).
{%endcomment%}
当处理一个更新资源的PATCH请求时，如果该更新会违反其他服务器的强制约束（如唯一性约束以外的属性标识）规则时，服务器可以返回`409 Conflict`。

{%comment%}
A server **MUST** return `409 Conflict` when processing a `PATCH` request in
which the resource object's `type` and `id` do not match the server's endpoint.
{%endcomment%}
在处理一个`PATCH`请求时，资源对象的`type`和`id`和服务器的端点数据不匹配，
服务器 **必须** 返回`409 Conflict`。

{%comment%}
A server **SHOULD** include error details and provide enough information to
recognize the source of the conflict.
{%endcomment%}
服务器应该包含错误详细信息并提供足够的信息来确定冲突的根源。

##### <a href="#crud-updating-responses-other" id="crud-updating-responses-other" class="headerlink"></a> {%comment%}Other Responses{%endcomment%}其他响应

{%comment%}
A server **MAY** respond with other HTTP status codes.

A server **MAY** include [error details] with error responses.

A server **MUST** prepare responses, and a client **MUST** interpret
responses, in accordance with
[`HTTP semantics`](http://tools.ietf.org/html/rfc7231).
{%endcomment%}
服务器 **可以** 响应其他HTTP状态码。

服务器 **可以** 包含错误的详细信息。

服务器 **必须** 准备响应，客户端 **必须** 根据[HTTP语义](http://tools.ietf.org/html/rfc7231)解释这些错误信息。

### <a href="#crud-updating-relationships" id="crud-updating-relationships" class="headerlink"></a>{%comment%} Updating Relationships{%endcomment%}更新关联

{%comment%}
Although relationships can be modified along with resources (as described
above), JSON API also supports updating of relationships independently at
URLs from [relationship links][relationships].
{%endcomment%}
尽管关联可随资源被修改（如上所述），但是JSON API还支持独立于[关联链接][relationships]URL的关联更新。

{%comment%}
> Note: Relationships are updated without exposing the underlying server
semantics, such as foreign keys. Furthermore, relationships can be updated
without necessarily affecting the related resources. For example, if an article
has many authors, it is possible to remove one of the authors from the article
without deleting the person itself. Similarly, if an article has many tags, it
is possible to add or remove tags. Under the hood on the server, the first
of these examples might be implemented with a foreign key, while the second
could be implemented with a join table, but the JSON API protocol would be
the same in both cases.

{%endcomment%}
>注：关联的更新不暴露底层服务器语义，如洋钥匙。此外，关联可以被更新，而不影响相关资源。例如，如果一篇文章有许多作者，这可以删除其中一个作者，而不删除作者人其本身。相似地，如果一篇文章有许多标签，可以添加或删除标签。在服务器的端罩下，这些例子中的第一个可以用一个洋钥匙来实现
，第二个可以用一个连接表来实现，但JSON API协议在这两种情况下是相同的
。

{%comment%}
> Note: A server may choose to delete the underlying resource if a
relationship is deleted (as a garbage collection measure).

{%endcomment%}
>注：如果关联已被删除（作为垃圾收集措施），服务器可以选择删除底层资源。

#### <a href="#crud-updating-to-one-relationships" id="crud-updating-to-one-relationships" class="headerlink"></a> {%comment%}Updating To-One Relationships{%endcomment%}更新对一关联

{%comment%}
A server **MUST** respond to `PATCH` requests to a URL from a to-one
[relationship link][relationships] as described below.
{%endcomment%}
服务器 **必须** 响应一个来自多[关联链接][relationships]的网址的`PATCH`请求，如下面所描述的。

{%comment%}
The `PATCH` request **MUST** include a top-level member named `data` containing
one of:
{%endcomment%}
该`PATCH`请求 **必须** 包含一个顶层成员命名的`data`,包含:

{%comment%}
* a [resource identifier object] corresponding to the new related resource.
* `null`, to remove the relationship.
{%endcomment%}

* 一个与新相关资源相对应的[资源标识符对象]。
* `null`，以消除关联。

{%comment%}
For example, the following request updates the author of an article:
{%endcomment%}
例如，下面的请求更新一个"article"的"author"：

```http
PATCH /articles/1/relationships/author HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": { "type": "people", "id": "12" }
}
```

{%comment%}
And the following request clears the author of the same article:
{%endcomment%}
下面的请求将清除同一个“article”的"author"：

```http
PATCH /articles/1/relationships/author HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": null
}
```

{%comment%}
If the relationship is updated successfully then the server **MUST** return
a successful response.
{%endcomment%}
如果关联更新成功，则服务器 **必须** 返回一个表示成功的响应.

#### <a href="#crud-updating-to-many-relationships" id="crud-updating-to-many-relationships" class="headerlink"></a> {%comment%}Updating To-Many Relationships{%endcomment%}更新多个关联

{%comment%}
A server **MUST** respond to `PATCH`, `POST`, and `DELETE` requests to a
URL from a to-many [relationship link][relationships] as described below.
{%endcomment%}
服务器 **必须** 响应`PATCH`、`POST`和`DELETE`请求至一个URL，它来自到多[关联链接][relationships]，如下所述.

{%comment%}
For all request types, the body **MUST** contain a `data` member whose value
is an empty array or an array of [resource identifier objects][resource identifier object].
{%endcomment%}
对于所有请求类型,主体 **必须** 包含一个`data`成员，其值是空数组或[资源标识符对象][resource identifier object]数组。

{%comment%}
If a client makes a `PATCH` request to a URL from a to-many
[relationship link][relationships], the server **MUST** either completely
replace every member of the relationship, return an appropriate error response
if some resources can not be found or accessed, or return a `403 Forbidden`
response if complete replacement is not allowed by the server.
{%endcomment%}
如果客户端向一个到多[关联链接][relationships]的URL发出`PATCH`请求,则服务器 **必须** 完全替换关联中的每一个成员，并且，如果无法找到或访问某些资源，则服务器返回一个适当的错误响应，或者如果服务器不允许完全替换，则返回一个`403 Forbidden`响应。

{%comment%}
For example, the following request replaces every tag for an article:
{%endcomment%}
例如，下列请求替换了一"article"的每一个“tag”：

```http
PATCH /articles/1/relationships/tags HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": [
    { "type": "tags", "id": "2" },
    { "type": "tags", "id": "3" }
  ]
}
```

{%comment%}
And the following request clears every tag for an article:
{%endcomment%}
以下要求为一个"article"清楚每一个"tag"：

```http
PATCH /articles/1/relationships/tags HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": []
}
```

{%comment%}
If a client makes a `POST` request to a URL from a
[relationship link][relationships], the server **MUST** add the specified
members to the relationship unless they are already present. If a given `type`
and `id` is already in the relationship, the server **MUST NOT** add it again.
{%endcomment%}
如果客户端向一个[关联链接][relationships]的URL发出`POST`请求，服务器 **必须** 将不存在的指定成员添加到关联中。如果一个给定的`type`和`id`已在关联中，则服务器 **不能** 重复添加。

{%comment%}
> Note: This matches the semantics of databases that use foreign keys for
has-many relationships. Document-based storage should check the has-many
relationship before appending to avoid duplicates.

{%endcomment%}
注：这符合使用外键多关联的语义数据库，为了避免重复冗余，基于文档的存储在添加前应检查多关联。

{%comment%}
If all of the specified resources can be added to, or are already present
in, the relationship then the server **MUST** return a successful response.
{%endcomment%}
如果所有的指定资源都可以添加到关联，或则其已经存在于关联中，则服务器 **必须** 返回的表示成功的响应。

{%comment%}
> Note: This approach ensures that a request is successful if the server's
state matches the requested state, and helps avoid pointless race conditions
caused by multiple clients making the same changes to a relationship.

{%endcomment%}
注：如果服务器的状态与请求的状态相匹配，则确保了一个请求是成功的，
并有助于避免多个客户端对关联的相同更改引起的无意义的竞争状况。

{%comment%}
In the following example, the comment with ID `123` is added to the list of
comments for the article with ID `1`:
{%endcomment%}
以下例子中，对ID`123`的"comments"添加到"article"的"comments"列表中的ID为`1`：

```http
POST /articles/1/relationships/comments HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": [
    { "type": "comments", "id": "123" }
  ]
}
```

{%comment%}
If the client makes a `DELETE` request to a URL from a
[relationship link][relationships] the server **MUST** delete the specified
members from the relationship or return a `403 Forbidden` response. If all of
the specified resources are able to be removed from, or are already missing
from, the relationship then the server **MUST** return a successful response.
{%endcomment%}
如果客户端向一个[关联链接][relationships]中的URL发起`DELETE`请求，服务器 **必须** 从关联中删除指定的成员，或返回 `403 Forbidden`响应。如果所有的指定资源都被移除，或者已经在关联中丢失了，服务器 **必须** 返回一个表示成功的响应。

{%comment%}
> Note: As described above for `POST` requests, this approach helps avoid
pointless race conditions between multiple clients making the same changes.

{%endcomment%}
注：如上所述的POST请求，这种方法有助于避免因多个客户端之间做出的相同更改无意义竞争状况。

{%comment%}
Relationship members are specified in the same way as in the `POST` request.
{%endcomment%}
在POST请求中关联成员以同样的方式指定。

{%comment%}
In the following example, comments with IDs of `12` and `13` are removed
from the list of comments for the article with ID `1`:
{%endcomment%}
以下例子中，在ID为`1`的"article"中，"comments"列表删除了ID为`12`和`13`的"comments"：
```http
DELETE /articles/1/relationships/comments HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": [
    { "type": "comments", "id": "12" },
    { "type": "comments", "id": "13" }
  ]
}
```

{%comment%}
> Note: RFC 7231 specifies that a DELETE request may include a body, but
that a server may reject the request. This spec defines the semantics of a
server, and we are defining its semantics for JSON API.

{%endcomment%}
注：RFC 7231指定删除请求可以包括本体，但服务器可以拒绝该请求，这个规则定义了一个服务器的语义，我们为JSON API 定义了这种语义。

#### <a href="#crud-updating-relationship-responses" id="crud-updating-relationship-responses" class="headerlink"></a> {%comment%}Responses{%endcomment%}响应

##### <a href="#crud-updating-relationship-responses-202" id="crud-updating-relationship-responses-202" class="headerlink"></a> 202 Accepted

{%comment%}
If a relationship update request has been accepted for processing, but the
processing has not been completed by the time the server responds, the
server **MUST** return a `202 Accepted` status code.
{%endcomment%}
如果一个关联更新请求已被接受处理，但是服务器响应处理还没有完成，服务器必须返回一个202 Accepted的状态码。

##### <a href="#crud-updating-relationship-responses-204" id="crud-updating-relationship-responses-204" class="headerlink"></a> 204 No Content

{%comment%}
A server **MUST** return a `204 No Content` status code if an update is
successful and the representation of the resource in the request matches the
result.
{%endcomment%}
 如果更新成功并且请求中的资源和结果匹配，服务器必须返回一个204 No Content 状态码。

 {%comment%}
> Note: This is the appropriate response to a `POST` request sent to a URL
from a to-many [relationship link][relationships] when that relationship already
exists. It is also the appropriate response to a `DELETE` request sent to a URL
from a to-many [relationship link][relationships] when that relationship does
not exist.

{%endcomment%}
注：当这种关联已经存在时，这是对发送一个到多[关联链接][relationships]的URL的`POST`请求的合理响应，当这种关联不存在时，对发送一个到多[关联链接][relationships]的URL的`DELET`请求的合理响应。

##### <a href="#crud-updating-relationship-responses-200" id="crud-updating-relationship-responses-200" class="headerlink"></a> 200 OK

{%comment%}
If a server accepts an update but also changes the targeted relationship(s)
in other ways than those specified by the request, it **MUST** return a `200
OK` response. The response document **MUST** include a representation of the
updated relationship(s).
{%endcomment%}
如果服务器接受更新，但也（除按请求所指明的方式）对资源做了更改，它 **必须** 返回一个`200 OK` 的响应。响应文档 **必须** 包含已更新关联的实例表示。

{%comment%}
A server **MUST** return a `200 OK` status code if an update is successful,
the client's current data remain up to date, and the server responds
only with top-level [meta] data. In this case the server **MUST NOT**
include a representation of the updated relationship(s).
{%endcomment%}
如果更新成功，服务器 **必须** 返回`200 OK`状态码，客户端当前数据保持最新，服务器只响应顶层的[meta]数据。在这种情况下，服务器 **不能** 包含已更新关联的实例表示。

##### <a href="#crud-updating-relationship-responses-403" id="crud-updating-relationship-responses-403" class="headerlink"></a> 403 Forbidden

{%comment%}
A server **MUST** return `403 Forbidden` in response to an unsupported request
to update a relationship.
{%endcomment%}
对于用不支持的请求来更新关联时，服务器 **必须** 返回`403 Forbidden`响应。

##### <a href="#crud-updating-relationship-responses-other" id="crud-updating-relationship-responses-other" class="headerlink"></a> {%comment%}Other Responses{%endcomment%}其他响应

{%comment%}
A server **MAY** respond with other HTTP status codes.

A server **MAY** include [error details] with error responses.

A server **MUST** prepare responses, and a client **MUST** interpret
responses, in accordance with
[`HTTP semantics`](http://tools.ietf.org/html/rfc7231).
{%endcomment%}
服务器 **可以** 响应其他HTTP状态码。

服务器 **可以** 包括错误资料与错误响应。

服务器 **必须** 做好响应准备，客户端 **必须** 依据[HTTP语义](http://tools.ietf.org/html/rfc7231)解释响应。

### <a href="#crud-deleting" id="crud-deleting" class="headerlink"></a> {%comment%}Deleting Resources{%endcomment%}删除资源

{%comment%}
An individual resource can be *deleted* by making a `DELETE` request to the
resource's URL:
{%endcomment%}
通过对资源的URL发出`DELETE`请求，可以 *删除* 单个资源：

```http
DELETE /photos/1 HTTP/1.1
Accept: application/vnd.api+json
```

#### <a href="#crud-deleting-responses" id="crud-deleting-responses" class="headerlink"></a> {%comment%}Responses{%endcomment%}响应

##### <a href="#crud-deleting-responses-202" id="crud-deleting-responses-202" class="headerlink"></a> 202 Accepted

{%comment%}
If a deletion request has been accepted for processing, but the processing has
not been completed by the time the server responds, the server **MUST**
return a `202 Accepted` status code.
{%endcomment%}
如果接受处理一个删除请求，但是服务器响应处理还没有完成，则服务器 **必须** 返回一个`202 Accepted`的状态码。

##### <a href="#crud-deleting-responses-204" id="crud-deleting-responses-204" class="headerlink"></a> 204 No Content

{%comment%}
A server **MUST** return a `204 No Content` status code if a deletion
request is successful and no content is returned.
{%endcomment%}
如果删除请求处理成功，且没有内容返回，那么服务器 **必须** 返回一个`204 No Content`状态码。

##### <a href="#crud-deleting-responses-200" id="crud-deleting-responses-200" class="headerlink"></a> 200 OK

{%comment%}
A server **MUST** return a `200 OK` status code if a deletion request is
successful and the server responds with only top-level [meta] data.
{%endcomment%}
如果删除请求成功，且服务器只回应以顶层[meta]数据，那么服务器 **必须** 返回一个`200 OK`状态码。

##### <a href="#crud-deleting-responses-other" id="crud-deleting-responses-other" class="headerlink"></a>{%comment%} Other Responses{%endcomment%}其他响应

{%comment%}
A server **MAY** respond with other HTTP status codes.

A server **MAY** include [error details] with error responses.

A server **MUST** prepare responses, and a client **MUST** interpret
responses, in accordance with
[`HTTP semantics`](http://tools.ietf.org/html/rfc7231).
{%endcomment%}
服务器 **可以** 响应其他HTTP状态码。

服务器 **可以** 包括错误信息的错误响应。

服务器 **必须** 做好响应准备，客户端 **必须** 依据[HTTP语义](http://tools.ietf.org/html/rfc7231)解释响应。

## <a href="#query-parameters" id="query-parameters" class="headerlink"></a> {%comment%}Query Parameters{%endcomment%}查询参数

{%comment%}
Implementation specific query parameters **MUST** adhere to the same constraints
as [member names] with the additional requirement that they **MUST** contain at
least one non a-z character (U+0061 to U+007A). It is **RECOMMENDED** that a
U+002D HYPHEN-MINUS, "-", U+005F LOW LINE, "_", or capital letter is used
(e.g. camelCasing).
{%endcomment%}
实现特定的查询参数 **必须** 遵循相同的约束，附加要求是作为成员姓名字段 **必须** 至少包含一个非a-z字母字符（U+0061到U+007a）。**可以** 用 U+002D 连字号-负号"-"， U+005F 下划线"_"，或者使用大写字母（如camelCasing）。

{%comment%}
If a server encounters a query parameter that does not follow the naming
conventions above, and the server does not know how to process it as a query
parameter from this specification, it **MUST** return `400 Bad Request`.
{%endcomment%}
如果服务器处理遇到一个不遵循以上命名规则的查询参数，而服务器不知道如何按此规范处理该查询参数，则服务器 **必须** 返回`400 Bad Request`响应。

{%comment%}
> Note: This is to preserve the ability of JSON API to make additive additions
to standard query parameters without conflicting with existing implementations.
{%endcomment%}
>注：这是保护JSONAPI对标准查询参数处理与现有实现冲突而增加的功能。

## <a href="#errors" id="errors" class="headerlink"></a> 错误{% comment %}Errors{% endcomment %}

### <a href="#errors-processing" id="errors-processing" class="headerlink"></a> 错误处理{% comment %}Processing Errors{% endcomment %}

{% comment %}
A server **MAY** choose to stop processing as soon as a problem is encountered,
or it **MAY** continue processing and encounter multiple problems. For instance,
a server might process multiple attributes and then return multiple validation
problems in a single response.
{% endcomment %}
服务器 **可以** 选择在遇到问题时尽快停止处理进程，也 **可以** 选择继续处理并遇到更多的问题。
例如，服务器可能会处理多个属性，然后会将多个验证错误放在同一个响应中返回。

{%comment%}
When a server encounters multiple problems for a single request, the most
generally applicable HTTP error code **SHOULD** be used in the response. For
instance, `400 Bad Request` might be appropriate for multiple 4xx errors
or `500 Internal Server Error` might be appropriate for multiple 5xx errors.
{%endcomment%}
当服务器遇到一个请求的多个问题时，响应 **应该** 是最普遍适用的HTTP错误代码。例如，`400 Bad Request`可以适用于4xx错误，或者`500 Internal Server Error`可以适用于5xx错误。

### <a href="#error-objects" id="error-objects" class="headerlink"></a> {%comment%}Error Objects{%endcomment%}错误对象

{%comment%}
Error objects provide additional information about problems encountered while
performing an operation. Error objects **MUST** be returned as an array
keyed by `errors` in the top level of a JSON API document.
{%endcomment%}
错误对象在执行操作时提供了有关问题的附加信息。在JSON API文档的顶层中，错误对象 **必须** 作为一个错误键数组返回。

{%comment%}
An error object **MAY** have the following members:

* `id`: a unique identifier for this particular occurrence of the problem.
* `links`: a [links object][links] containing the following members:
  * `about`: a [link][links] that leads to further details about this
    particular occurrence of the problem.
* `status`: the HTTP status code applicable to this problem, expressed as a
  string value.
* `code`: an application-specific error code, expressed as a string value.
* `title`: a short, human-readable summary of the problem that **SHOULD NOT**
  change from occurrence to occurrence of the problem, except for purposes of
  localization.
* `detail`: a human-readable explanation specific to this occurrence of the
  problem. Like `title`, this field's value can be localized.
* `source`: an object containing references to the source of the error,
  optionally including any of the following members:
  * `pointer`: a JSON Pointer [[RFC6901](https://tools.ietf.org/html/rfc6901)]
    to the associated entity in the request document [e.g. `"/data"` for a
    primary data object, or `"/data/attributes/title"` for a specific attribute].
  * `parameter`: a string indicating which URI query parameter caused
    the error.
* `meta`: a [meta object][meta] containing non-standard meta-information about the
  error.
{%endcomment%}
一个错误对象 **可以** 有以下成员：

* `id`: 特定问题的唯一标示符。
* `links`:一个包含下列成员的[链接对象][links] ：
  * `about`:导致这个特殊问题发生的进一步细节的一个[链接][links] 。
* `status`:适用于这个问题的HTTP状态码，使用字符串表示。
* `code`:应用程序特定的错误代码，表示为字符串值。
* `title`:简短的，可读性高的问题总结，除了国际化本地化处理之外，不同场景下，相同的问题，值是 **不应该** 变动的。
* `detail`:针对该问题的高可读性解释，就像Title，这个字段的值可以被本地化。
* `source`:一个包含错误源引用的对象，任选地包含下列任何成员：
  * `pointer`:一个指向请求文档中的关联实体的JSON指针[[RFC6901](https://tools.ietf.org/html/rfc6901)]（例如`”/data”`是对应于一个主要数据对象，或`”/data/attributes/title”` 是对应于一个特定的属性）。
  * `parameter`:一个是由URL查询参数引起错误的字符串。
* `meta`:一个包含非标准元信息的[元对象][meta]。

[resource objects]: #document-resource-objects
[attributes]: #document-resource-object-attributes
[relationships]: #document-resource-object-relationships
[fields]: #document-resource-object-fields
[related resource link]: #document-resource-object-related-resource-links
[resource linkage]: #document-resource-object-linkage
[resource links]: #document-resource-object-links
[resource identifier object]: #document-resource-identifier-objects
[compound document]: #document-compound-documents
[meta]: #document-meta
[links]: #document-links
[error details]: #errors
[member names]: #document-member-names
[pagination]: #fetching-pagination
