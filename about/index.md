---
layout: page
title: 关于
---

## <a href="#channels" id="channels" class="headerlink"></a> {% comment %}Channels{% endcomment %}更多信息：

{% comment %}
JSON API is:

  * [@jsonapi](http://twitter.com/jsonapi) on
[Twitter](http://twitter.com)
  * _#jsonapi_ channel on [Freenode IRC](http://freenode.net)
  * [jsonapi discussion forum](http://discuss.jsonapi.org)
{% endcomment %}
 * 可以在推特上@jsonapi；
 * 访问freenode IRC上的#josnapi频道；
 * 上jsonapi论坛

## <a href="#editors" id="editors" class="headerlink"></a> {% comment %}Editors{% endcomment %}编辑器

{% comment %}
There are five primary editors of this specification:

- [Steve Klabnik](http://twitter.com/steveklabnik)
- [Yehuda Katz](http://twitter.com/wycats)
- [Dan Gebhardt](http://twitter.com/dgeb)
- [Tyler Kellen](http://twitter.com/tkellen)
- [Ethan Resnick](http://twitter.com/ethanresnick)
{% endcomment %}
支持此规范的五个主要的编辑器：

- [Steve Klabnik](http://twitter.com/steveklabnik)编辑器
- [Yehuda Katz](http://twitter.com/wycats)编辑器
- [Dan Gebhardt](http://twitter.com/dgeb)编辑器
- [Tyler Kellen](http://twitter.com/tkellen)编辑器
- [Ethan Resnick](http://twitter.com/ethanresnick)编辑器

## <a href="#roadmap" id="roadmap" class="headerlink"></a>{% comment %} Roadmap{% endcomment %}路线图

### <a href="#roadmap-1-1" id="roadmap-1-1" class="headerlink"></a> 1.1版本
{% comment %}**Targeted Release Date:** September 30th, 2015　{% endcomment %} **发布日期：** 2015年9月30日

{%comment%}
* Embedding / creating multiple related resources in a single request
* Extension support
{%endcomment%}
* 在一个请求中嵌入/创建多个相关的资源
* 支持扩展

## <a href="#history" id="history" class="headerlink"></a>{%comment%} History{%endcomment%}发展历史

{%comment%}
JSON API was originally drafted by [Yehuda Katz](http://twitter.com/wycats)
in May 2013. This first draft was extracted from the JSON transport
implicitly defined by [Ember](http://emberjs.com/) Data's REST adapter.
{%endcomment%}
JSON API最初由[Yehuda Katz](http://twitter.com/wycats)在2013年5月起草。最初的草案通过[Ember](http://emberjs.com/) Data的REST适配器于JSON隐式定义的传输中引出。

{%comment%}
In general, Ember Data's goal is to eliminate the need for ad-hoc code
per application to communicate with servers that communicate in a
well-defined way.
{%endcomment%}
Ember Data的目的是消除每个应用程序的ad-hoc代码的需要，用定义明确的方式与服务器通信。
{%comment%}
Some servers, like Firebase, Parse and CouchDB already define strict
communication protocols for clients, and were good fits for Ember Data.
In contrast, servers written in Rails, Node, and Django tend to be
written in a "REST-style" but lack the precision necessary for drop-in
client code.
{%endcomment%}
有的服务器，如Firebase,、Parse 和 CouchDB，已经为客户端定义了严格详细的通信协议，且适用于Ember Data。相比之下，用Rails，Node和Django编写的服务器往往是用一种“REST-style”方式，但其缺乏drop-in客户端代码的必要精度。

{%comment%}
The REST Adapter in Ember Data implicitly defined a protocol that
custom servers could implement to get a drop-in client for all of their
resources. [ActiveModel::Serializers][1] is a proof-of-concept library
for Rails that implemented the serialization format expected by Ember
Data.
{%endcomment%}
Ember-Date的REST适配器隐式定义了一个协议，即自定义服务器可以实现为其自身全部资源获取一个drop-in客户端。[ActiveModel::Serializers][1]是一个Rails的proof-of-concept库,它实现了Ember-Data所需的序列化格式。
[1]: https://github.com/rails-api/active_model_serializers

{%comment%}
Record creation, update, and deletion was defined implicitly by the
Ember Data library and was close to conventions already in wide use by
Rails, Django and Node developers.
{%endcomment%}
记录的创建，更新，和删除是Ember-Data库的隐式定义，类似于已被Rails 、Django和Node开发者所广泛使用的约定。

{%comment%}
The goals of the media type are to balance:

* A generic media type that can work across a broad set of use cases,
  including the generally used relationship types
* Similarity to existing server-side framework practices (and human
  readability for debugging)
* Ease of implementation on the server side
* Ease of implementation on the client side

This specification reached a stable version 1.0 on May 29, 2015.
{%endcomment%}
媒体类型的目标是为了平衡：
* 通过广泛用例集合工作的通用媒体类型，包括通常使用的关联类型。
* 类似于现有服务端框架实例（以及调试可读性）
* 易于服务端的实现
* 易于客户端的实现

此规范于2015年5月29日发展到了稳定的1.0版本。
