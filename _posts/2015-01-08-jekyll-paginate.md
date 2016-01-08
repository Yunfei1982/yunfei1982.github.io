---
layout: post
title:  "jekyll-paginate 的使用"
date:   2016-01-08 21:11:00 +0800
categories: Jekyll
---

配置 _config.yml
{% highlight yaml %}
gems: [jekyll-paginate, jekyll-gist]
{% endhighlight %}

找到 index.html 中的
{% highlight html %}
{% raw %}
{% for post in site.posts %}
{% endraw %}
{% endhighlight %}

修改为：
{% highlight html %}
{% raw %}
{% for post in paginator.posts %}
{% endraw %}
{% endhighlight %}

加入分页连接
{% highlight html %}
{% raw %}
{% if paginator.total_pages > 1 %}
<div class="pagination">
  {% if paginator.previous_page %}
    <a href="{{ paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/' }}">&laquo; 上一页</a>
  {% else %}
    <span>&laquo; 上一页</span>
  {% endif %}

  {% for page in (1..paginator.total_pages) %}
    {% if page == paginator.page %}
      <em>{{ page }}</em>
    {% elsif page == 1 %}
      <a href="{{ paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/' }}">{{ page }}</a>
    {% else %}
      <a href="{{ site.paginate_path | prepend: site.baseurl | replace: '//', '/' | replace: ':num', page }}">{{ page }}</a>
    {% endif %}
  {% endfor %}

  {% if paginator.next_page %}
    <a href="{{ paginator.next_page_path | prepend: site.baseurl | replace: '//', '/' }}">下一页 &raquo;</a>
  {% else %}
    <span>下一页 &raquo;</span>
  {% endif %}
</div>
{% endif %}
{% endraw %}
{% endhighlight %}

在 post.html 底部加入上一篇、下一篇的连接
{% highlight html %}
{% raw %}
{% if page.previous %}
    上一篇：<a href="{{ page.previous.url | prepend: site.baseurl }}">{{ page.previous.title }}</a><br>
{% endif %}

{% if page.next %}
    下一篇：<a href="{{ page.next.url | prepend: site.baseurl }}">{{ page.next.title }}</a>
{% endif %}
{% endraw %}
{% endhighlight %}