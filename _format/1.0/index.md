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

[复合文档][compound document]中的资源联动允许客户端将所有包含的[资源对象][resource objects]连在一起，无需通过`GET`访问链接[links]中的任何URL。
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

The following `self` link is simply a URL:

```json
"links": {
  "self": "http://example.com/posts",
}
```

The following `related` link includes a URL as well as meta-information
about a related resource collection:

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

> Note: Additional members may be specified for links objects and link
objects in the future. It is also possible that the allowed values of
additional members will be expanded (e.g. a `collection` link may support an
array of values, whereas a `self` link does not).

### <a href="#document-jsonapi-object" id="document-jsonapi-object" class="headerlink"></a> JSON API Object

A JSON API document **MAY** include information about its implementation
under a top level `jsonapi` member. If present, the value of the `jsonapi`
member **MUST** be an object (a "jsonapi object"). The jsonapi object **MAY**
contain a `version` member whose value is a string indicating the highest JSON
API version supported. This object **MAY** also contain a `meta` member, whose
value is a [meta] object that contains non-standard meta-information.

```json
{
  "jsonapi": {
    "version": "1.0"
  }
}
```

If the `version` member is not present, clients should assume the server
implements at least version 1.0 of the specification.

> Note: Because JSON API is committed to making additive changes only, the
version string primarily indicates which new features a server may support.

### <a href="#document-member-names" id="document-member-names" class="headerlink"></a> Member Names

All member names used in a JSON API document **MUST** be treated as case sensitive
by clients and servers, and they **MUST** meet all of the following conditions:

- Member names **MUST** contain at least one character.
- Member names **MUST** contain only the allowed characters listed below.
- Member names **MUST** start and end with a "globally allowed character",
  as defined below.

To enable an easy mapping of member names to URLs, it is **RECOMMENDED** that
member names use only non-reserved, URL safe characters specified in [RFC 3986](http://tools.ietf.org/html/rfc3986#page-13).

#### <a href="#document-member-names-allowed-characters" id="document-member-names-allowed-characters" class="headerlink"></a> Allowed Characters

The following "globally allowed characters" **MAY** be used anywhere in a member name:

- U+0061 to U+007A, "a-z"
- U+0041 to U+005A, "A-Z"
- U+0030 to U+0039, "0-9"
- U+0080 and above (non-ASCII Unicode characters; _not recommended, not URL safe_)

Additionally, the following characters are allowed in member names, except as the
first or last character:

- U+002D HYPHEN-MINUS, "-"
- U+005F LOW LINE, "_"
- U+0020 SPACE, " " _(not recommended, not URL safe)_

#### <a href="#document-member-names-reserved-characters" id="document-member-names-reserved-characters" class="headerlink"></a> Reserved Characters

The following characters **MUST NOT** be used in member names:

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

## <a href="#fetching" id="fetching" class="headerlink"></a> Fetching Data

Data, including resources and relationships, can be fetched by sending a
`GET` request to an endpoint.

Responses can be further refined with the optional features described below.

### <a href="#fetching-resources" id="fetching-resources" class="headerlink"></a> Fetching Resources

A server **MUST** support fetching resource data for every URL provided as:

* a `self` link as part of the top-level links object
* a `self` link as part of a resource-level links object
* a `related` link as part of a relationship-level links object

For example, the following request fetches a collection of articles:

```http
GET /articles HTTP/1.1
Accept: application/vnd.api+json
```

The following request fetches an article:

```http
GET /articles/1 HTTP/1.1
Accept: application/vnd.api+json
```

And the following request fetches an article's author:

```http
GET /articles/1/author HTTP/1.1
Accept: application/vnd.api+json
```

#### <a href="#fetching-resources-responses" id="fetching-resources-responses" class="headerlink"></a> Responses

##### <a href="#fetching-resources-responses-200" id="fetching-resources-responses-200" class="headerlink"></a> 200 OK

A server **MUST** respond to a successful request to fetch an individual
resource or resource collection with a `200 OK` response.

A server **MUST** respond to a successful request to fetch a resource
collection with an array of [resource objects] or an empty array (`[]`) as
the response document's primary data.

For example, a `GET` request to a collection of articles could return:

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

A similar response representing an empty collection would be:

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

A server **MUST** respond to a successful request to fetch an individual
resource with a [resource object][resource objects] or `null` provided as
the response document's primary data.

`null` is only an appropriate response when the requested URL is one that
might correspond to a single resource, but doesn't currently.

> Note: Consider, for example, a request to fetch a to-one related resource link.
This request would respond with `null` when the relationship is empty (such that
the link is corresponding to no resources) but with the single related resource's
[resource object][resource objects] otherwise.

For example, a `GET` request to an individual article could return:

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

If the above article's author is missing, then a `GET` request to that related
resource would return:

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

A server **MUST** respond with `404 Not Found` when processing a request to
fetch a single resource that does not exist, except when the request warrants a
`200 OK` response with `null` as the primary data (as described above).

##### <a href="#fetching-resources-responses-other" id="fetching-resources-responses-other" class="headerlink"></a> Other Responses

A server **MAY** respond with other HTTP status codes.

A server **MAY** include [error details] with error responses.

A server **MUST** prepare responses, and a client **MUST** interpret
responses, in accordance with
[`HTTP semantics`](http://tools.ietf.org/html/rfc7231).

### <a href="#fetching-relationships" id="fetching-relationships" class="headerlink"></a> Fetching Relationships

A server **MUST** support fetching relationship data for every relationship URL
provided as a `self` link as part of a relationship's `links` object.

For example, the following request fetches data about an article's comments:

```http
GET /articles/1/relationships/comments HTTP/1.1
Accept: application/vnd.api+json
```

And the following request fetches data about an article's author:

```http
GET /articles/1/relationships/author HTTP/1.1
Accept: application/vnd.api+json
```

#### <a href="#fetching-relationships-responses" id="fetching-relationships-responses" class="headerlink"></a> Responses

##### <a href="#fetching-relationships-responses-200" id="fetching-relationships-responses-200" class="headerlink"></a> 200 OK

A server **MUST** respond to a successful request to fetch a relationship
with a `200 OK` response.

The primary data in the response document **MUST** match the appropriate
value for [resource linkage], as described above for
[relationship objects][relationships].

The top-level [links object][links] **MAY** contain `self` and `related` links,
as described above for [relationship objects][relationships].

For example, a `GET` request to a URL from a to-one relationship link could
return:

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

If the above relationship is empty, then a `GET` request to the same URL would
return:

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

A `GET` request to a URL from a to-many relationship link could return:

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

If the above relationship is empty, then a `GET` request to the same URL would
return:

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

A server **MUST** return `404 Not Found` when processing a request to fetch
a relationship link URL that does not exist.

> Note: This can happen when the parent resource of the relationship
does not exist. For example, when `/articles/1` does not exist, request to
`/articles/1/relationships/tags` returns `404 Not Found`.

If a relationship link URL exists but the relationship is empty, then
`200 OK` **MUST** be returned, as described above.

##### <a href="#fetching-relationships-responses-other" id="fetching-relationships-responses-other" class="headerlink"></a> Other Responses

A server **MAY** respond with other HTTP status codes.

A server **MAY** include [error details] with error responses.

A server **MUST** prepare responses, and a client **MUST** interpret
responses, in accordance with
[`HTTP semantics`](http://tools.ietf.org/html/rfc7231).

### <a href="#fetching-includes" id="fetching-includes" class="headerlink"></a> Inclusion of Related Resources

An endpoint **MAY** return resources related to the primary data by default.

An endpoint **MAY** also support an `include` request parameter to allow the
client to customize which related resources should be returned.

If an endpoint does not support the `include` parameter, it **MUST** respond
with `400 Bad Request` to any requests that include it.

If an endpoint supports the `include` parameter and a client supplies it,
the server **MUST NOT** include unrequested [resource objects] in the `included`
section of the [compound document].

The value of the `include` parameter **MUST** be a comma-separated (U+002C
COMMA, ",") list of relationship paths. A relationship path is a dot-separated
(U+002E FULL-STOP, ".") list of [relationship][relationships] names.

If a server is unable to identify a relationship path or does not support
inclusion of resources from a path, it **MUST** respond with 400 Bad Request.

> Note: For example, a relationship path could be `comments.author`, where
`comments` is a relationship listed under a `articles` [resource object][resource objects], and
`author` is a relationship listed under a `comments` [resource object][resource objects].

For instance, comments could be requested with an article:

```http
GET /articles/1?include=comments HTTP/1.1
Accept: application/vnd.api+json
```

In order to request resources related to other resources, a dot-separated path
for each relationship name can be specified:

```http
GET /articles/1?include=comments.author HTTP/1.1
Accept: application/vnd.api+json
```

> Note: Because [compound documents][compound document] require full linkage
(except when relationship linkage is excluded by sparse fieldsets), intermediate
resources in a multi-part path must be returned along with the leaf nodes. For
example, a response to a request for `comments.author` should include `comments`
as well as the `author` of each of those `comments`.

> Note: A server may choose to expose a deeply nested relationship such as
`comments.author` as a direct relationship with an alias such as
`comment-authors`. This would allow a client to request
`/articles/1?include=comment-authors` instead of
`/articles/1?include=comments.author`. By abstracting the nested
relationship with an alias, the server can still provide full linkage in
compound documents without including potentially unwanted intermediate
resources.

Multiple related resources can be requested in a comma-separated list:

```http
GET /articles/1?include=author,comments.author HTTP/1.1
Accept: application/vnd.api+json
```

Furthermore, related resources can be requested from a relationship endpoint:

```http
GET /articles/1/relationships/comments?include=comments.author HTTP/1.1
Accept: application/vnd.api+json
```

In this case, the primary data would be a collection of
[resource identifier objects][resource identifier object] that represent linkage to comments for an article,
while the full comments and comment authors would be returned as included data.

> Note: This section applies to any endpoint that responds with primary
data, regardless of the request type. For instance, a server could support
the inclusion of related resources along with a `POST` request to create a
resource or relationship.

### <a href="#fetching-sparse-fieldsets" id="fetching-sparse-fieldsets" class="headerlink"></a> Sparse Fieldsets

A client **MAY** request that an endpoint return only specific [fields] in the
response on a per-type basis by including a `fields[TYPE]` parameter.

The value of the `fields` parameter **MUST** be a comma-separated (U+002C
COMMA, ",") list that refers to the name(s) of the fields to be returned.

If a client requests a restricted set of [fields] for a given resource type,
an endpoint **MUST NOT** include additional [fields] in resource objects of
that type in its response.

```http
GET /articles?include=author&fields[articles]=title,body&fields[people]=name HTTP/1.1
Accept: application/vnd.api+json
```

> Note: The above example URI shows unencoded `[` and `]` characters simply for
readability. In practice, these characters must be percent-encoded, per the
requirements [in RFC 3986](http://tools.ietf.org/html/rfc3986#section-3.4).

> Note: This section applies to any endpoint that responds with resources as
primary or included data, regardless of the request type. For instance, a
server could support sparse fieldsets along with a `POST` request to create
a resource.

### <a href="#fetching-sorting" id="fetching-sorting" class="headerlink"></a> Sorting

A server **MAY** choose to support requests to sort resource collections
according to one or more criteria ("sort fields").

> Note: Although recommended, sort fields do not necessarily need to
correspond to resource attribute and association names.

> Note: It is recommended that dot-separated (U+002E FULL-STOP, ".") sort
fields be used to request sorting based upon relationship attributes. For
example, a sort field of `author.name` could be used to request that the
primary data be sorted based upon the `name` attribute of the `author`
relationship.

An endpoint **MAY** support requests to sort the primary data with a `sort`
query parameter. The value for `sort` **MUST** represent sort fields.

```http
GET /people?sort=age HTTP/1.1
Accept: application/vnd.api+json
```

An endpoint **MAY** support multiple sort fields by allowing comma-separated
(U+002C COMMA, ",") sort fields. Sort fields **SHOULD** be applied in the
order specified.

```http
GET /people?sort=age,name HTTP/1.1
Accept: application/vnd.api+json
```

The sort order for each sort field **MUST** be ascending unless it is prefixed
with a minus (U+002D HYPHEN-MINUS, "-"), in which case it **MUST** be descending.

```http
GET /articles?sort=-created,title HTTP/1.1
Accept: application/vnd.api+json
```

The above example should return the newest articles first. Any articles
created on the same date will then be sorted by their title in ascending
alphabetical order.

If the server does not support sorting as specified in the query parameter
`sort`, it **MUST** return `400 Bad Request`.

If sorting is supported by the server and requested by the client via query
parameter `sort`, the server **MUST** return elements of the top-level
`data` array of the response ordered according to the criteria specified.
The server **MAY** apply default sorting rules to top-level `data` if
request parameter `sort` is not specified.

> Note: This section applies to any endpoint that responds with a resource
collection as primary data, regardless of the request type.

### <a href="#fetching-pagination" id="fetching-pagination" class="headerlink"></a> Pagination

A server **MAY** choose to limit the number of resources returned in a response
to a subset ("page") of the whole set available.

A server **MAY** provide links to traverse a paginated data set ("pagination
links").

Pagination links **MUST** appear in the links object that corresponds to a
collection. To paginate the primary data, supply pagination links in the
top-level `links` object. To paginate an included collection returned in
a [compound document], supply pagination links in the corresponding links
object.

The following keys **MUST** be used for pagination links:

* `first`: the first page of data
* `last`: the last page of data
* `prev`: the previous page of data
* `next`: the next page of data

Keys **MUST** either be omitted or have a `null` value to indicate that a
particular link is unavailable.

Concepts of order, as expressed in the naming of pagination links, **MUST**
remain consistent with JSON API's [sorting rules](#fetching-sorting).

The `page` query parameter is reserved for pagination. Servers and clients
**SHOULD** use this key for pagination operations.

> Note: JSON API is agnostic about the pagination strategy used by a server.
Effective pagination strategies include (but are not limited to):
page-based, offset-based, and cursor-based. The `page` query parameter can
be used as a basis for any of these strategies. For example, a page-based
strategy might use query parameters such as `page[number]` and `page[size]`,
an offset-based strategy might use `page[offset]` and `page[limit]`, while a
cursor-based strategy might use `page[cursor]`.

> Note: The example query parameters above use unencoded `[` and `]` characters
simply for readability. In practice, these characters must be percent-encoded,
per the requirements in [RFC 3986](http://tools.ietf.org/html/rfc3986#section-3.4).

> Note: This section applies to any endpoint that responds with a resource
collection as primary data, regardless of the request type.

### <a href="#fetching-filtering" id="fetching-filtering" class="headerlink"></a> Filtering

The `filter` query parameter is reserved for filtering data. Servers and clients
**SHOULD** use this key for filtering operations.

> Note: JSON API is agnostic about the strategies supported by a server. The
`filter` query parameter can be used as the basis for any number of filtering
strategies.

## <a href="#crud" id="crud" class="headerlink"></a> Creating, Updating and Deleting Resources

A server **MAY** allow resources of a given type to be created. It **MAY**
also allow existing resources to be modified or deleted.

A request **MUST** completely succeed or fail (in a single "transaction"). No
partial updates are allowed.

> Note: The `type` member is required in every [resource object][resource objects] throughout requests and
responses in JSON API. There are some cases, such as when `POST`ing to an
endpoint representing heterogenous data, when the `type` could not be inferred
from the endpoint. However, picking and choosing when it is required would be
confusing; it would be hard to remember when it was required and when it was
not. Therefore, to improve consistency and minimize confusion, `type` is
always required.

### <a href="#crud-creating" id="crud-creating" class="headerlink"></a> Creating Resources

A resource can be created by sending a `POST` request to a URL that represents
a collection of resources. The request **MUST** include a single [resource object][resource objects]
as primary data. The [resource object][resource objects] **MUST** contain at least a `type` member.

For instance, a new photo might be created with the following request:

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

If a relationship is provided in the `relationships` member of the
[resource object][resource objects], its value **MUST** be a relationship object with a `data`
member. The value of this key represents the linkage the new resource is to
have.

#### <a href="#crud-creating-client-ids" id="crud-creating-client-ids" class="headerlink"></a> Client-Generated IDs

A server **MAY** accept a client-generated ID along with a request to create
a resource. An ID **MUST** be specified with an `id` key, the value of
which **MUST** be a universally unique identifier. The client **SHOULD** use
a properly generated and formatted *UUID* as described in RFC 4122
[[RFC4122](http://tools.ietf.org/html/rfc4122.html)].

> NOTE: In some use-cases, such as importing data from another source, it
may be possible to use something other than a UUID that is still guaranteed
to be globally unique. Do not use anything other than a UUID unless you are
100% confident that the strategy you are using indeed generates globally
unique identifiers.

For example:

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

A server **MUST** return `403 Forbidden` in response to an unsupported request
to create a resource with a client-generated ID.

#### <a href="#crud-creating-responses" id="crud-creating-responses" class="headerlink"></a> Responses

##### <a href="#crud-creating-responses-201" id="crud-creating-responses-201" class="headerlink"></a> 201 Created

If a `POST` request did not include a [Client-Generated
ID](#crud-creating-client-ids) and the requested resource has been created
successfully, the server **MUST** return a `201 Created` status code.

The response **SHOULD** include a `Location` header identifying the location
of the newly created resource.

The response **MUST** also include a document that contains the primary
resource created.

If the [resource object][resource objects] returned by the response contains a `self` key in its
`links` member and a `Location` header is provided, the value of the `self`
member **MUST** match the value of the `Location` header.

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

If a request to create a resource has been accepted for processing, but the
processing has not been completed by the time the server responds, the
server **MUST** return a `202 Accepted` status code.

##### <a href="#crud-creating-responses-204" id="crud-creating-responses-204" class="headerlink"></a> 204 No Content

If a `POST` request *did* include a [Client-Generated
ID](#crud-creating-client-ids) and the requested resource has been created
successfully, the server **MUST** return either a `201 Created` status code
and response document (as described above) or a `204 No Content` status code
with no response document.

> Note: If a `204` response is received the client should consider the resource
object sent in the request to be accepted by the server, as if the server
had returned it back in a `201` response.

##### <a href="#crud-creating-responses-403" id="crud-creating-responses-403" class="headerlink"></a> 403 Forbidden

A server **MAY** return `403 Forbidden` in response to an unsupported request
to create a resource.

##### <a href="#crud-creating-responses-409" id="crud-creating-responses-409" class="headerlink"></a> 409 Conflict

A server **MUST** return `409 Conflict` when processing a `POST` request to
create a resource with a client-generated ID that already exists.

A server **MUST** return `409 Conflict` when processing a `POST` request in
which the [resource object][resource objects]'s `type` is not among the type(s) that constitute the
collection represented by the endpoint.

A server **SHOULD** include error details and provide enough information to
recognize the source of the conflict.

##### <a href="#crud-creating-responses-other" id="crud-creating-responses-other" class="headerlink"></a> Other Responses

A server **MAY** respond with other HTTP status codes.

A server **MAY** include [error details] with error responses.

A server **MUST** prepare responses, and a client **MUST** interpret
responses, in accordance with
[`HTTP semantics`](http://tools.ietf.org/html/rfc7231).

### <a href="#crud-updating" id="crud-updating" class="headerlink"></a> Updating Resources

A resource can be updated by sending a `PATCH` request to the URL that
represents the resource.

The URL for a resource can be obtained in the `self` link of the resource
object. Alternatively, when a `GET` request returns a single [resource object][resource objects] as
primary data, the same request URL can be used for updates.

The `PATCH` request **MUST** include a single [resource object][resource objects] as primary data.
The [resource object][resource objects] **MUST** contain `type` and `id` members.

For example:

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

#### <a href="#crud-updating-resource-attributes" id="crud-updating-resource-attributes" class="headerlink"></a> Updating a Resource's Attributes

Any or all of a resource's [attributes] **MAY** be included in the resource
object included in a `PATCH` request.

If a request does not include all of the [attributes] for a resource, the server
**MUST** interpret the missing [attributes] as if they were included with their
current values. The server **MUST NOT** interpret missing attributes as `null`
values.

For example, the following `PATCH` request is interpreted as a request to
update only the `title` and `text` attributes of an article:

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

#### <a href="#crud-updating-resource-relationships" id="crud-updating-resource-relationships" class="headerlink"></a> Updating a Resource's Relationships

Any or all of a resource's [relationships] **MAY** be included in the resource
object included in a `PATCH` request.

If a request does not include all of the [relationships] for a resource, the server
**MUST** interpret the missing [relationships] as if they were included with their
current values. It **MUST NOT** interpret them as `null` or empty values.

If a relationship is provided in the `relationships` member of a resource
object in a `PATCH` request, its value **MUST** be a relationship object
with a `data` member. The relationship's value will be replaced with the
value specified in this member.

For instance, the following `PATCH` request will update the `author` relationship of an article:

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

Likewise, the following `PATCH` request performs a complete replacement of
the `tags` for an article:

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

A server **MAY** reject an attempt to do a full replacement of a to-many
relationship. In such a case, the server **MUST** reject the entire update,
and return a `403 Forbidden` response.

> Note: Since full replacement may be a very dangerous operation, a server
may choose to disallow it. For example, a server may reject full replacement if
it has not provided the client with the full list of associated objects, and
does not want to allow deletion of records the client has not seen.

#### <a href="#crud-updating-responses" id="crud-updating-responses" class="headerlink"></a> Responses

##### <a href="#crud-updating-responses-202" id="crud-updating-responses-202" class="headerlink"></a> 202 Accepted

If an update request has been accepted for processing, but the processing
has not been completed by the time the server responds, the server **MUST**
return a `202 Accepted` status code.

##### <a href="#crud-updating-responses-200" id="crud-updating-responses-200" class="headerlink"></a> 200 OK

If a server accepts an update but also changes the resource(s) in ways other
than those specified by the request (for example, updating the `updated-at`
attribute or a computed `sha`), it **MUST** return a `200 OK` response. The
response document **MUST** include a representation of the updated
resource(s) as if a `GET` request was made to the request URL.

A server **MUST** return a `200 OK` status code if an update is successful,
the client's current attributes remain up to date, and the server responds
only with top-level [meta] data. In this case the server **MUST NOT**
include a representation of the updated resource(s).

##### <a href="#crud-updating-responses-204" id="crud-updating-responses-204" class="headerlink"></a> 204 No Content

If an update is successful and the server doesn't update any attributes besides
those provided, the server **MUST** return either a `200 OK` status code and
response document (as described above) or a `204 No Content` status code with no
response document.

##### <a href="#crud-updating-relationship-responses-403" id="crud-updating-relationship-responses-403" class="headerlink"></a> 403 Forbidden

A server **MUST** return `403 Forbidden` in response to an unsupported request
to update a resource or relationship.

##### <a href="#crud-updating-responses-404" id="crud-updating-responses-404" class="headerlink"></a> 404 Not Found

A server **MUST** return `404 Not Found` when processing a request to modify
a resource that does not exist.

A server **MUST** return `404 Not Found` when processing a request that
references a related resource that does not exist.

##### <a href="#crud-updating-responses-409" id="crud-updating-responses-409" class="headerlink"></a> 409 Conflict

A server **MAY** return `409 Conflict` when processing a `PATCH` request to
update a resource if that update would violate other server-enforced
constraints (such as a uniqueness constraint on a property other than `id`).

A server **MUST** return `409 Conflict` when processing a `PATCH` request in
which the resource object's `type` and `id` do not match the server's endpoint.

A server **SHOULD** include error details and provide enough information to
recognize the source of the conflict.

##### <a href="#crud-updating-responses-other" id="crud-updating-responses-other" class="headerlink"></a> Other Responses

A server **MAY** respond with other HTTP status codes.

A server **MAY** include [error details] with error responses.

A server **MUST** prepare responses, and a client **MUST** interpret
responses, in accordance with
[`HTTP semantics`](http://tools.ietf.org/html/rfc7231).

### <a href="#crud-updating-relationships" id="crud-updating-relationships" class="headerlink"></a> Updating Relationships

Although relationships can be modified along with resources (as described
above), JSON API also supports updating of relationships independently at
URLs from [relationship links][relationships].

> Note: Relationships are updated without exposing the underlying server
semantics, such as foreign keys. Furthermore, relationships can be updated
without necessarily affecting the related resources. For example, if an article
has many authors, it is possible to remove one of the authors from the article
without deleting the person itself. Similarly, if an article has many tags, it
is possible to add or remove tags. Under the hood on the server, the first
of these examples might be implemented with a foreign key, while the second
could be implemented with a join table, but the JSON API protocol would be
the same in both cases.

> Note: A server may choose to delete the underlying resource if a
relationship is deleted (as a garbage collection measure).

#### <a href="#crud-updating-to-one-relationships" id="crud-updating-to-one-relationships" class="headerlink"></a> Updating To-One Relationships

A server **MUST** respond to `PATCH` requests to a URL from a to-one
[relationship link][relationships] as described below.

The `PATCH` request **MUST** include a top-level member named `data` containing
one of:

* a [resource identifier object] corresponding to the new related resource.
* `null`, to remove the relationship.

For example, the following request updates the author of an article:

```http
PATCH /articles/1/relationships/author HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": { "type": "people", "id": "12" }
}
```

And the following request clears the author of the same article:

```http
PATCH /articles/1/relationships/author HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": null
}
```

If the relationship is updated successfully then the server **MUST** return
a successful response.

#### <a href="#crud-updating-to-many-relationships" id="crud-updating-to-many-relationships" class="headerlink"></a> Updating To-Many Relationships

A server **MUST** respond to `PATCH`, `POST`, and `DELETE` requests to a
URL from a to-many [relationship link][relationships] as described below.

For all request types, the body **MUST** contain a `data` member whose value
is an empty array or an array of [resource identifier objects][resource identifier object].

If a client makes a `PATCH` request to a URL from a to-many
[relationship link][relationships], the server **MUST** either completely
replace every member of the relationship, return an appropriate error response
if some resources can not be found or accessed, or return a `403 Forbidden`
response if complete replacement is not allowed by the server.

For example, the following request replaces every tag for an article:

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

And the following request clears every tag for an article:

```http
PATCH /articles/1/relationships/tags HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
  "data": []
}
```

If a client makes a `POST` request to a URL from a
[relationship link][relationships], the server **MUST** add the specified
members to the relationship unless they are already present. If a given `type`
and `id` is already in the relationship, the server **MUST NOT** add it again.

> Note: This matches the semantics of databases that use foreign keys for
has-many relationships. Document-based storage should check the has-many
relationship before appending to avoid duplicates.

If all of the specified resources can be added to, or are already present
in, the relationship then the server **MUST** return a successful response.

> Note: This approach ensures that a request is successful if the server's
state matches the requested state, and helps avoid pointless race conditions
caused by multiple clients making the same changes to a relationship.

In the following example, the comment with ID `123` is added to the list of
comments for the article with ID `1`:

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

If the client makes a `DELETE` request to a URL from a
[relationship link][relationships] the server **MUST** delete the specified
members from the relationship or return a `403 Forbidden` response. If all of
the specified resources are able to be removed from, or are already missing
from, the relationship then the server **MUST** return a successful response.

> Note: As described above for `POST` requests, this approach helps avoid
pointless race conditions between multiple clients making the same changes.

Relationship members are specified in the same way as in the `POST` request.

In the following example, comments with IDs of `12` and `13` are removed
from the list of comments for the article with ID `1`:

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

> Note: RFC 7231 specifies that a DELETE request may include a body, but
that a server may reject the request. This spec defines the semantics of a
server, and we are defining its semantics for JSON API.

#### <a href="#crud-updating-relationship-responses" id="crud-updating-relationship-responses" class="headerlink"></a> Responses

##### <a href="#crud-updating-relationship-responses-202" id="crud-updating-relationship-responses-202" class="headerlink"></a> 202 Accepted

If a relationship update request has been accepted for processing, but the
processing has not been completed by the time the server responds, the
server **MUST** return a `202 Accepted` status code.

##### <a href="#crud-updating-relationship-responses-204" id="crud-updating-relationship-responses-204" class="headerlink"></a> 204 No Content

A server **MUST** return a `204 No Content` status code if an update is
successful and the representation of the resource in the request matches the
result.

> Note: This is the appropriate response to a `POST` request sent to a URL
from a to-many [relationship link][relationships] when that relationship already
exists. It is also the appropriate response to a `DELETE` request sent to a URL
from a to-many [relationship link][relationships] when that relationship does
not exist.

##### <a href="#crud-updating-relationship-responses-200" id="crud-updating-relationship-responses-200" class="headerlink"></a> 200 OK

If a server accepts an update but also changes the targeted relationship(s)
in other ways than those specified by the request, it **MUST** return a `200
OK` response. The response document **MUST** include a representation of the
updated relationship(s).

A server **MUST** return a `200 OK` status code if an update is successful,
the client's current data remain up to date, and the server responds
only with top-level [meta] data. In this case the server **MUST NOT**
include a representation of the updated relationship(s).

##### <a href="#crud-updating-relationship-responses-403" id="crud-updating-relationship-responses-403" class="headerlink"></a> 403 Forbidden

A server **MUST** return `403 Forbidden` in response to an unsupported request
to update a relationship.

##### <a href="#crud-updating-relationship-responses-other" id="crud-updating-relationship-responses-other" class="headerlink"></a> Other Responses

A server **MAY** respond with other HTTP status codes.

A server **MAY** include [error details] with error responses.

A server **MUST** prepare responses, and a client **MUST** interpret
responses, in accordance with
[`HTTP semantics`](http://tools.ietf.org/html/rfc7231).

### <a href="#crud-deleting" id="crud-deleting" class="headerlink"></a> Deleting Resources

An individual resource can be *deleted* by making a `DELETE` request to the
resource's URL:

```http
DELETE /photos/1 HTTP/1.1
Accept: application/vnd.api+json
```

#### <a href="#crud-deleting-responses" id="crud-deleting-responses" class="headerlink"></a> Responses

##### <a href="#crud-deleting-responses-202" id="crud-deleting-responses-202" class="headerlink"></a> 202 Accepted

If a deletion request has been accepted for processing, but the processing has
not been completed by the time the server responds, the server **MUST**
return a `202 Accepted` status code.

##### <a href="#crud-deleting-responses-204" id="crud-deleting-responses-204" class="headerlink"></a> 204 No Content

A server **MUST** return a `204 No Content` status code if a deletion
request is successful and no content is returned.

##### <a href="#crud-deleting-responses-200" id="crud-deleting-responses-200" class="headerlink"></a> 200 OK

A server **MUST** return a `200 OK` status code if a deletion request is
successful and the server responds with only top-level [meta] data.

##### <a href="#crud-deleting-responses-other" id="crud-deleting-responses-other" class="headerlink"></a> Other Responses

A server **MAY** respond with other HTTP status codes.

A server **MAY** include [error details] with error responses.

A server **MUST** prepare responses, and a client **MUST** interpret
responses, in accordance with
[`HTTP semantics`](http://tools.ietf.org/html/rfc7231).

## <a href="#query-parameters" id="query-parameters" class="headerlink"></a> Query Parameters

Implementation specific query parameters **MUST** adhere to the same constraints
as [member names] with the additional requirement that they **MUST** contain at
least one non a-z character (U+0061 to U+007A). It is **RECOMMENDED** that a
U+002D HYPHEN-MINUS, "-", U+005F LOW LINE, "_", or capital letter is used
(e.g. camelCasing).

If a server encounters a query parameter that does not follow the naming
conventions above, and the server does not know how to process it as a query
parameter from this specification, it **MUST** return `400 Bad Request`.

> Note: This is to preserve the ability of JSON API to make additive additions
to standard query parameters without conflicting with existing implementations.

## <a href="#errors" id="errors" class="headerlink"></a> 错误{% comment %}Errors{% endcomment %}

### <a href="#errors-processing" id="errors-processing" class="headerlink"></a> 处理错误{% comment %}Processing Errors{% endcomment %}
服务器**可以**选择在遇到问题时尽快停止处理进程，也**可以**选择继续处理并遇到更多的问题。
例如，一台服务器可能会处理多个属性，然后会将多个验证错误放在同一个响应中返回。
{% comment %}
A server **MAY** choose to stop processing as soon as a problem is encountered,
or it **MAY** continue processing and encounter multiple problems. For instance,
a server might process multiple attributes and then return multiple validation
problems in a single response.
{% endcomment %}

When a server encounters multiple problems for a single request, the most
generally applicable HTTP error code **SHOULD** be used in the response. For
instance, `400 Bad Request` might be appropriate for multiple 4xx errors
or `500 Internal Server Error` might be appropriate for multiple 5xx errors.

### <a href="#error-objects" id="error-objects" class="headerlink"></a> Error Objects

Error objects provide additional information about problems encountered while
performing an operation. Error objects **MUST** be returned as an array
keyed by `errors` in the top level of a JSON API document.

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
