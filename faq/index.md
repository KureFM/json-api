---
layout: page
title: 常见问题
show_sidebar: true
---

## <a href="#what-is-the-meaning-of-json-apis-version" id="what-is-the-meaning-of-json-apis-version" class="headerlink"></a>{% comment %}What is the meaning of JSON API's version?{% endcomment %}JSON API的版本是什么意思？

{% comment %}
Now that JSON API has reached a stable version 1.0, it will always be
backwards compatible using a _never remove, only add_ strategy.
{% endcomment %}
如今的JSON API是一个稳定的1.0版本，它会一直遵循“_永不移除，只添加_”的策略向后兼容。

{% comment %}
A version is maintained in order to:
{% endcomment %}
版本主要是为了：

{% comment %}
* allow tracking of additive changes to the specification.
* know what features a particular implementation *may potentially* support.
{% endcomment %}
* 可以跟踪增加到规范中的变化。
* 可以知道一个实际项目的实现可能潜在的支持。

## <a href="#why-not-use-the-hal-specification" id="why-not-use-the-hal-specification" class="headerlink"></a> {% comment %}Why not use the HAL specification?{% endcomment %}为什么不使用HAL规范？

{% comment %}
There are several reasons:
{% endcomment %}
有几个原因：

{% comment %}
* HAL embeds child documents recursively, while JSON API flattens the entire
graph of objects at the top level. This means that if the same "people" are
referenced from different kinds of objects (say, the author of both posts and
comment), this format ensures that there is only a single representation of
each person document in the payload.
{% endcomment %}
* HAL递归嵌入子文档，而JSON API在顶层扁平化对象结构，意味着同一“people”被不同对象引用（如，“posts”和“comment”的“author”）时，此格式能保证每个“person”文档仅存在一个有效实例。

{% comment %}
* Similarly, JSON API uses IDs for linkage, which makes it possible to cache
documents from compound responses and then limit subsequent requests to only
the documents that aren't already present locally. If you're lucky, this can
even completely eliminate HTTP requests.
{% endcomment %}
* 类似的，JSON API 使用IDs作为连接，使得从复合响应中缓存文档成为可能，仅当本地不存在对应文档，才会发出后续请求。幸运的话，甚至可以完全无需HTTP请求。

{% comment %}
* HAL is a serialization format, but says nothing about how to update
documents. JSON API thinks through how to update existing records (leaning on
PATCH and JSON Patch), and how those updates interact with compound documents
returned from GET requests. It also describes how to create and delete
documents, and what 200 and 204 responses from those updates mean.
{% endcomment %}
* HAL 是序列化格式，但完全未定义文档更新操作。JSON API 则仔细考虑如何更新已存在文档（依赖 PATCH 和 JSON PATCH），以及更新操作与GET请求返回的复合文档交互方式。同时定义如何创建，删除文档，以及更新操作的 200,204 响应。

{% comment %}
In short, JSON API is an attempt to formalize similar ad hoc client-server
interfaces that use JSON as an interchange format. It is specifically focused
around using those APIs with a smart client that knows how to cache documents it
has already seen and avoid asking for them again.
{% endcomment %}
简单来说，JSON API 尝试格式化相似的，特殊的client-server通讯接口，使用 JSON 作为数据交换格式。专注于使用成熟的客户端来调用相关API，客户端能够缓存已经获取到的文档，避免再次请求已缓存信息。

{% comment %}
It is extracted from a real-world library already used by a number of projects,
which has informed both the request/response aspects (absent from HAL) and the
interchange format itself.
{% endcomment %}
JSON API 从大量实际项目所使用的库中抽象而出。同时定义请求/响应（HAL 未定义），以及对应数据交互格式。

## <a href="#how-to-discover-resource-possible-actions" id="how-to-discover-resource-possible-actions" class="headerlink"></a> {% comment %}How can I discover a resource's supported actions?{% endcomment %}如何获取资源可能的行为？

{% comment %}
Whenever a client requests a URI, the server can include an [`Allow` header](https://tools.ietf.org/html/rfc7231#section-7.4.1)
in its response to indicate the methods the requested resource supports. Servers
wishing to support method discovery should include this header. The client can
then send a [`HEAD`](https://tools.ietf.org/html/rfc7231#section-4.3.2) request
(or any other type of request) to the URI in order to discover its supported methods.
{% endcomment %}
当一个客户端请求一个URI时，服务器端可以包含一个[`Allow header`](https://tools.ietf.org/html/rfc7231#section-7.4.1) 在它的响应中，表明请求的资源支持该方法。服务器端希望支持方法发现应包含这个[头](https://tools.ietf.org/html/rfc7231#section-4.3.2)。然后，为了发现它所支持的方法，客户端可以发送一个到URI的“HEAD”请求。

{% comment %}
For instance, a client might request `HEAD /articles`, and the response could
contain the header `Allow: GET,POST`, indicating that the client can GET the
collection and also POST to it to create new resources.
{% endcomment %}
例如，客户端请求`“HEAD/articles”`，且响应包含`”Alow:GET.POST”`头，以表明客户端可以“GET”集合并“POST”到它，以生成新的资源。

{% comment %}
JSON API is still working on a way to for resources to advertise and detail
non-standard actions they support. Feel free to
{% endcomment %}
JSON API仍以它们对于资源的宣传和细节的非标准操作支持方式工作。可以随时加入这个[讨论](https://github.com/json-api/json-api/issues/745)!！


## <a href="#wheres-put" id="wheres-put" class="headerlink"></a> {% comment %}Where's PUT?{% endcomment %}


{% comment %}
Using PUT to partially update a resource (i.e. to change only some of its state)
is not allowed by the
[HTTP specification](https://tools.ietf.org/html/rfc7231#section-4.3.4).
Instead, PUT is supposed to completely replace the state of a resource:
{% endcomment %}
使用PUT来部分更新一个资源（例如，只改变一些它的状态），HTTP规范是不允许的。反之，PUT可以完全取代一个资源的状态。

{% comment %}
> “The PUT method requests that the state of the target resource be **created
  or replaced** with the state…in the request message payload. A successful PUT
  of a given representation would suggest that a subsequent GET on that same
  target resource will result in an equivalent representation being sent…”
{% endcomment %}
> “PUT方法请求以状态 **生成或替换** 目标资源……在一个请求的有效载荷内，给定的‘representation’
的成功的‘PUT’表明随后有同一目标资源的‘GET’将导致发送一个等价的‘representation’……”

{% comment %}
The correct method for partial updates, therefore, is [PATCH](http://tools.ietf.org/html/rfc5789),
which is what JSON API uses. And because PATCH can also be used compliantly for
full resource replacement, JSON API hasn't needed to define any behavior for
PUT so far. However, it may define PUT semantics in the future.
{% endcomment %}
局部更新的正确方式是JSON API 所用的[PATCH](http://tools.ietf.org/html/rfc5789)，因为PATCH可以使用全资源置换，目前，JSON API不必为PUT定义任何行为。然而，PUT语义可能在以后定义。

{% comment %}
In the past, many APIs used PUT for partial updates because PATCH wasn’t yet
well-supported. However, almost all clients now support PATCH, and those that
don’t can be easily [worked around]({{ site.github.url }}/recommendations/#patchless-clients).
{% endcomment %}
过去，许多API使用PUT来进行部分更新，因为PATCH不像如今支持得这么好。然而，如今几乎所有客户端都支持PATCH，包括那些[以前不容易处理的]({{ site.github.url }}/recommendations/#patchless-clients)。

## <a href="#is-there-a-json-schema-describing-json-api" id="is-there-a-json-schema-describing-json-api" class="headerlink"></a> {% comment %}Is there a JSON Schema describing JSON API?{% endcomment %}有没有JSON 规范来定义JSON API？

{% comment %}
Yes, you can find the JSON Schema definition at
[http://jsonapi.org/schema](http://jsonapi.org/schema). This schema is as
restrictive as possible, but has flexibility to be extended within your
documentation. Validation will not yield false negatives, but could yield false
positives for the sake of flexibility.
{% endcomment %}
当然，你可以在[http://jsonapi.org/schema](http://jsonapi.org/schema)找到 JSON 规范的定义。这个规范尽可能做了限制的，不过在你的文档中可以灵活地扩展。验证不会产生漏报率，但为了灵活性却可以产生误报率。

{% comment %}
You can find more information about the JSON Schema format at
[http://json-schema.org](http://json-schema.org).
{% endcomment %}
可以在[ttp://json-schema.org](http://json-schema.org)找到更多关于JSON 规范格式的信息。

## <a href="#resource-collections-returned-as-arrays" id="resource-collections-returned-as-arrays" class="headerlink"></a> {% comment %}Why are resource collections returned as arrays instead of sets keyed by ID?{% endcomment %}为什么资源集合作为数组返回，而不是ID索引集合？

{% comment %}
A JSON array is naturally ordered while sets require metadata to specify order
among members. Therefore, arrays allow for more natural sorting by default or
specified criteria.
{% endcomment %}
JSON数组是自然排序，而集合需要元数据进行成员排序。因此，默认情况下，数组能够实现更自然的排序或者特殊方式排序。

## <a href="#why-related-resources-included-compound-document" id="why-related-resources-included-compound-document" class="headerlink"></a> {% comment %}Why are related resources nested in an `included` object in a compound document?{% endcomment %}为什么关联资源嵌套在一个复合文档的`included`对象中？

{% comment %}
Primary resources should be isolated because their order and number is often
significant. It's necessary to separate primary and related resources by more
than type because it's possible that a primary resource may have related
resources of the same type (e.g. the "parents" of a "person"). Nesting related
resources in `included` prevents this possible conflict.
{% endcomment %}
主要资源应是分离开来的，因为他们的顺序和数字通常是比较重要的。有必要分离主要资源和关联资源为多种类型，因为主要资源和关联资源有可能有相同的类型（例如，一个“person”的“parents”）。将关联资源嵌入`included`对象中以避免这种可能的冲突。

## <a href="#position-uri-structure-custom-endpoints" id="position-uri-structure-custom-endpoints" class="headerlink"></a>{% comment %} Does JSON API take any position on URI structure, on rules for custom endpoints, which do not fit the paradigm of GET/POST/PATCH/DELETE on the resource URI, etc.?{% endcomment %}JSON API对于URI结构，定制端点的规则作何看法？哪个不适合资源URI的GET/POST/PATCH/DELETE等的范式？

{% comment %}
JSON API has no requirements about URI structure, implementations are free to use whatever form they wish.
{% endcomment %}
JSON API 对于URI结构没有要求，他们可以自由的使用所希望的任何格式实现。
