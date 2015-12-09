---
layout: post
title:  "Getting Started Part 2, Liquid & Material Design Lite"
date:   2015-12-08 00:00:00
author: "Zyzle"
img: "drafts/dropplet.jpg"
img_link: "https://commons.wikimedia.org/wiki/File:Wassertropfen.jpg"
img_license: ["by", "sa"]
img_author: "Sven Hoppe"
tags: ["blog", "tools", "liquid", "meta", "material design"]
comments: false
---

Lets face it the while Jekyll is a great tool for static site generation the default template is pretty boring. In this post I'll go over the basics of the [Liquid](http://liquidmarkup.org/) template engine used by Jekyll as well as the [Material Design Lite](http://www.getmdl.io/) components.

## Liquid

> Ruby library for rendering safe templates which cannot affect the security of the server they are rendered on.

The description from the Liquid home page sums it up nicely. There isn't much I can add to that. Let's take a look at how we use it in our pages then.

If you've set up a basic blog the way we did in [part 1]({% post_url 2015-12-05-getting-started-part-1 %}) you'll notice in the front matter we define a `layout` entry in the yaml. This value corresponds to a filename in the `_layout` directory.

Open up `_layout/post.html` in your editor and have a look. It should be similar to the following:

{% highlight html linenos %}
{% raw %}
---
layout: default
---
<article class="post" itemscope itemtype="http://schema.org/BlogPosting">

  <header class="post-header">
    <h1 class="post-title" itemprop="name headline">{{ page.title }}</h1>
    <p class="post-meta">
      <time datetime="{{ page.date | date_to_xmlschema }}" itemprop="datePublished">
        {{ page.date | date: "%b %-d, %Y" }}
      </time>
      {% if page.author %}
        • <span itemprop="author" itemscope itemtype="http://schema.org/Person">
          <span itemprop="name">{{ page.author }}</span>
        </span>
      {% endif %}
    </p>
  </header>

  <div class="post-content" itemprop="articleBody">
    {{ content }}
  </div>

</article>
{% endraw %}
{% endhighlight %}
