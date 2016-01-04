---
layout: post
title:  "Gem 版本维护"
date:   2016-01-04 16:50:00 +0800
categories: Ruby
---

今天在公司项目中使用 [Grape Entity][Grape Entity] 的时候，发现一个奇怪的问题，当渲染的结果为单个对象时是正常的，渲染结果是集合的时候一直报 `no implicit conversion of Array into Hash` 错误。之前在其他项目中使用的时候，并没有遇到类似的情况。尝试调试了半个多小时，一直没有找到解决办法。

后来想到项目中原先用的 [Grape][Grape] 版本比较陈旧，[Grape Entity][Grape Entity] 则是目前的最新版，便尝试更新。

{% highlight ruby %}
# 改成最新的 0.14.0
gem 'grape', '~> 0.14.0'
{% endhighlight %}

{% highlight ruby %}
bundle update grape
{% endhighlight %}

再测试，一切正常。。。

一个小 gem 就会引起这样的问题，我们的项目中总共使用了 *236个gem*，关键在于很久没有进行过维护更新了，想想都可怕。

如果是一个长期进行开发维护的项目，还是应该经常的维护和更新 Gemfile，否则很容易遇到下面几种常见的情况：

- 有些过时的 gem 使项目结构无法很好的重构
- 新引入的 gem 和原有的 gem 之间出现兼容问题
- 想升级更新的时候，由于错过太多版本，导致升级无从下手
- 无法及时享受Rails新版本的安全更新

当增加一个新的 gem 依赖时，考虑一下在你项目周期中升级这个 gem 的成本。gem 的作者是否在接下来的两年仍然会维护这个项目？如果该 gem 不随 Rails 版本更新，那么迫不得已时你能否替换掉这个 gem 或者继续维护它？
其实不需要在第一时间跟随 Rails 新版本升级。直到大版本已经发了一些小布丁之后再升级比较好。例如：不要直接立刻升级到 Rails 4.0.0，等到 4.0.5 发布的时候再升级。
这会给你的 gem 的维护者一些时间让代码支持 Rails 新版本。


[Grape]: https://github.com/ruby-grape/grape
[Grape Entity]: https://github.com/ruby-grape/grape-entity