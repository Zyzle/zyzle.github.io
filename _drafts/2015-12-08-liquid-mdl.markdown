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

I'm going to assume you're familiar with basic HTML so shouldn't have any problem with most of what's here, you may not have come across the `itemscope` and `itemprop` attributes before. These are part of the [schema.org](https://schema.org) extensions that the basic templates come with. I plan on writing about these at some point in the future so lets just skip them for now and look at the Liquid markup.

First off we have the Jekyll front matter. In this is defined a `layout` property. As you may have guessed already this template is in fact inheriting from another. Have a look at the `default.html` file in the templates directory:

{% highlight html linenos %}
{% raw %}
<!DOCTYPE html>
<html>
  {% include head.html %}
  <body>
    {% include header.html %}
    <div class="page-content">
      <div class="wrapper">
        {{ content }}
      </div>
    </div>
    {% include footer.html %}
  </body>
</html>
{% endraw %}
{% endhighlight %}

This is the base template that all others in this basic setup inherit from, you'll notice there's no front matter here to specify a parent template.

Lets take a look at the Liquid markup syntax. There are two basic types of enclosing tags in Liquid, {% raw %}`{% ... %}` and `{{ ... }}`{% endraw %}, these are known as "tag markup" and "output markup" respectively.

### Output markup

At the simplest level, output markup allows us to place some dynamic content in the template. The first example from our `post.html` file is {% raw %}`{{ page.title }}`{% endraw %} inside a heading tag. The `page.title` variable being referenced here relates to the `title` defined in the front matter of any blog posts using this template as their `layout`. Try opening up one of the post markdown files from the previous post and editing this `title` variable in the front matter. Now start the Jekyll server, or if you have it running already with `--watch` enabled you should see the title for that post has changed in both the posts list and the post page.



### Tag markup

Tag markup provides a way to use logic in out templates. In the example above from
