{% comment %}
  On the different version pages, make the status message dynamic in
  the same way the title is.
{% endcomment %}

{% assign version = include.version %}
{% assign is_latest_version_page = include.is_latest_version %}
{% assign is_upcoming_version_page = include.is_upcoming_version %}

## <a href="#status" id="status" class="headerlink"></a> 状态{% comment %}Status{% endcomment %}

{% comment %}
  The first paragraph in each case, below, aims to explain what content
  the page holds and how that may (or may not) change over time.

  Note: all the text below must be outdented and markdownified where it's
  included for it to be rendered correctly as markdown.
{% endcomment %}

{% if is_latest_version_page %}
这个页面是JSON API最新的发行版本，当前版本是{{ site.latest_version }}。新版本的JSON API使用了*不移除，只添加*策略，**永远是向后兼容的**。可在[论坛](http://discuss.jsonapi.org/)提出建议。
{% comment %}
  This page represents the latest published version of JSON API, which is
  currently version {{ site.latest_version }}. New versions of JSON API **will
  always be backwards compatible** using a _never remove, only add_ strategy.
  Additions can be proposed in our [discussion forum](http://discuss.jsonapi.org/).
{% endcomment %}
{% elsif is_upcoming_version_page %}
  This page represents the **working draft** for the next version of JSON API,
  which is currently expected to be {{ site.latest_version|plus:0.1 }}.

{% elsif version and version > site.latest_version %}
  This page will always present the most recent text for JSON API
  v{{ site.latest_version|plus:0.1 }}. Currently, version
  {{ site.latest_version|plus:0.1 }} is **still a draft**, so this text is
  provisional.

{% elsif version and version == site.latest_version %}
  This page presents an archived copy of JSON API version {{ version }}. None
  of the normative text on this page will change. **Subsequent versions of
  JSON API will remain compatible with this one**, as JSON API uses a _never
  remove, only add_ strategy.

{% else %}
  This page presents an archived copy of JSON API version {{ version }}. None
  of the normative text on this page will change. While {{ version }} is no
  longer the [latest version](/format/) of JSON API, **new versions will remain
  compatible with this one**, as JSON API uses a _never remove, only add_ strategy.

{% endif %}

{% comment %}
  Now, add a second (and sometimes third) paragraph for the different cases,
  indicating the type of feedback we want (user/implementor concerns, error
  reports, proposed additions, etc) for that case and the best way to submit it.
{% endcomment %}

{% if is_latest_version_page or (version and version <= site.latest_version) %}
如果你发现了规范中的文本错误，或者你想参与到其中，请在[GitHub repository](https://github.com/json-api/json-api)提出公开的`issue`或者`pull request`来让我们知道。
{% comment %}
  If you catch an error in the specification&rsquo;s text, or if you write an
  implementation, please let us know by opening an issue or pull request at our
  [GitHub repository](https://github.com/json-api/json-api).
{% endcomment %}
{% else %}
  If you have concerns about the changes in this draft, catch an error in the
  specification&rsquo;s text, or write an implementation, please let us know by
  opening an issue or pull request at our [GitHub repository](https://github.com/json-api/json-api).

  You can also propose additions to JSON API in our [discussion forum](http://discuss.jsonapi.org/).
  Keep in mind, though, that all new versions of JSON API **must be backwards
  compatible** using a _never remove, only add_ strategy.
{% endif %}
