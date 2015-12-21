---
layout: post
title:  "Getting Started Part 2, Liquid Templates"
date:   2015-12-12 23:26:00
author: "Zyzle"
img: "20151212-getting-started-2/dropplet.jpg"
img_link: "https://commons.wikimedia.org/wiki/File:Wassertropfen.jpg"
img_license: ["cc", "by", "sa"]
img_author: "Sven Hoppe"
tags: ["blog", "tools", "liquid", "tutorial"]
comments: true
---

Lets face it the while Jekyll is a great tool for static site generation the default template is pretty boring. Before we can change it however, we need to understand how templating in Jekyll works. In this post I'll go over the basics of the [Liquid](http://liquidmarkup.org/) template engine used by Jekyll which should hopefully give you some ideas on how you can edit a site to your liking.

## Liquid

> A Ruby library for rendering safe templates which cannot affect the security of the server they are rendered on.

The description from the Liquid home page sums it up nicely. There isn't much I can add to that. Let's take a look at how we use it in our pages then.

If you've set up a basic blog the way we did in [part 1]({% post_url 2015-12-05-getting-started-part-1 %}) you'll notice in the front matter we define a `layout` entry in the YAML. This value corresponds to a filename in the `_layout` directory.

Open up `_layout/post.html` in your editor and have a look. It should be similar to the following:

{% highlight html %}
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

I'm going to assume you're familiar with basic HTML so shouldn't have any problem with most of what's here, although you may not have come across the `itemscope` and `itemprop` attributes before. These are part of the [schema.org](https://schema.org) extensions that the basic templates come with. I plan on writing about these at some point in the future so lets just skip them for now and look at the Liquid markup.

First off we have the Jekyll front matter. In this is defined a `layout` property. As you may have guessed already this template is in fact inheriting from another. Have a look at the `default.html` file in the templates directory:

{% highlight html %}
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

At the simplest level, output markup allows us to place some dynamic content in the template. The first example from our `post.html` file is {% raw %}`{{ page.title }}`{% endraw %} inside a heading tag. The `page.title` variable being referenced here relates to the `title` defined in the front matter of any blog posts using this template as their `layout`. Try opening up one of the markdown files from the previous post and editing this `title` variable in the front matter. Now start the Jekyll server, or if you have it running already with `--watch` enabled refresh and you should see the title for that post has changed in both the posts list and the post page.

#### Variables

Jekyll provides a couple of useful variables for us to use in our Liquid templates, the `page` one used above is just one example.  The others include:

{: .post-list }

* `site`: Provides site wide information and configuration variables from your `_config.yml` file. It has the following properties that may be accessed using `site.`:
  * `pages`: A list of all the pages currently in the site
  * `posts`: A list of all posts in chronological order
  * `tags.TAG`: A list of all posts with tag `TAG`
* `page`: Page specific information and the variables you set in the pages front matter. As well as the following properties any variable you define in the pages front matter are available though `page.`:
  * `content`: The rendered content of the page
  * `title`: The page title
  * `url`: The page URL without the domain
  * `date`: The date assigned to the page, should usually be the date published
  * `tags`: The list of tags this page has
  * `next`: The link to the next page in chronological order or `nil`
  * `previous`: The link to the previous page in chronological order or `nil`
* `content`: For layout files, this contains the rendered content of the post or page the layout file is wrapping

The list above is by no means exhaustive more information on these Jekyll provided site variables can be found on the [Jekyll site](http://jekyllrb.com/docs/variables/)

#### Filters

Have a look at the `<time>` tag in the post template. Notice that as well as a reference to the `page.date` there is also a pipe `|` and another argument. These additional arguments are called filters. Filters allow the content included with the output markup to be dynamically altered in some way before being added to the page.

Taking a look at the post page source generated by Jekyll will show us how these tags apply data from our post to the page;

The original template tag:

{% highlight html %}
{% raw %}
<time datetime="{{ page.date | date_to_xmlschema }}" itemprop="datePublished">
  {{ page.date | date: "%b %-d, %Y" }}
</time>
{% endraw %}
{% endhighlight %}

The source of the html created by Jekyll for our post:

{% highlight html %}
<time datetime="2015-12-05T20:31:26+00:00" itemprop="datePublished">Dec 5, 2015</time>
{% endhighlight %}

You'll see in the above that the template has taken in the date variable defined in the posts front matter and applied two different transformations to it using the `|` filter syntax. The first takes the date object and converts it to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) format. This filter provides a standardized date format that can be used as metadata for the `datePublished` schema attribute.

The second transformation is to a human readable format that will be shown to the user on the page. The string given as an argument after the filter provides the date format string to be used for this date. There are several filters available with Liquid a list of which can be found [here](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers#standard-filters) and more included by Jekyll as standard, found [here](http://jekyllrb.com/docs/templates/#filters)

As well as using `page` to access front matter metadata the special variable `content` is available.  This `content` block will be made up of the complete parsed markdown of your posts and pages and will be inserted into the template at the location you choose. You may have also noticed that `content` is used in the templates being inherited from (in the case of posts: `default.html`).

So the end product of a Liquid template compilation for our posts becomes the following:

{% highlight html %}
{% raw %}
<!-- everything in default.html before it's {{ content }} markup -->
  <!-- everything in post.html before it's {{ content }} markup -->
    <!-- the compiled markdown of your post as HTML -->
  <!-- everything after post.html's {{ content }} -->
<!-- everything after default.html's {{ content }} -->
{% endraw %}
{% endhighlight %}

### Tag markup

Tag markup provides a way to use logic in our templates. In the basic templates provided by Jekyll we have a few examples of this logic.

#### Conditionals

Conditionals allow page content to be shown or hidden depending in some condition. In the `post.html` template we have the following conditional:

{% highlight html %}
{% raw %}
{% if page.author %}
  • <span itemprop="author" itemscope itemtype="http://schema.org/Person">
    <span itemprop="name">{{ page.author }}</span>
  </span>
{% endif %}
{% endraw %}
{% endhighlight %}

The HTML inside the `if` block above will only be rendered into our page if the post front matter contains a variable `author` and the value of this is not null.

`else` statements can also be added to these conditionals to provide alternate content in the event that a condition is not met.  Lets try this now by editing the `<header>` section of the `post.html` and adding the following immediately after the author conditional from above:

{% highlight html %}
{% raw %}
{% if page.tag %}
  • <span itemprop="keywords">{{ page.tag }}</span>
{% else %}
  • <em>untagged</em>
{% endif %}
{% endraw %}
{% endhighlight %}

If you start the Jekyll server and have a look at a post right now you should see the _untagged_ message after the post date as the pages created up until now do not have either by default. Try adding one now by opening up the `.markdown` file we created in `_drafts` and add your name as `author` and `tutorial` as the tag. It should look like the below (remember you'll need to add the `--drafts` option to the Jekyll command line):

{% highlight yaml %}
---
layout: post
title:  "My Second Post!"
date:   2015-12-10 12:29:56 +0000
author: "my_name"
tag: "tutorial"
---
{% endhighlight %}

If you look at this second post in your browser now you should see, "my_name" and "tutorial" as the post author and tag in the heading.

#### Loops

Loops, as the name suggests, allow us to loop over a set of values and repeat content in out template. In the section above we added a `tag` to our second post. In most cases you'll probably want to tag a post with more than one keyword. Lets add a couple more to the post by editing the front matter like so:

{% highlight yaml %}
---
layout: post
title:  "My Second Post!"
date:   2015-12-10 12:29:56 +0000
author: "my_name"
tags: ["tutorial", "Jekyll", "Liquid"]
---
{% endhighlight %}

The syntax for creating an array (a list if items) is `[item1, item2, ...]`, that is items, in the case above some strings, separated by commas, within square brackets. Now we have an array of tags that we can loop over. Open up the `post.html` template and we'll use a loop to show these tags in the page header.

Edit the if statement we added earlier with a `for` loop inside it to match the one below:

{% highlight html %}
{% raw %}
{% if page.tags %}
  •
  {% for tag in page.tags %}
    <span itemprop="keywords">{{ tag }}</span>,
  {% endfor %}
{% else %}
  • <em>untagged</em>
{% endif %}
{% endraw %}
{% endhighlight %}

The effect on the HTML created by Liquid is exactly what you'd expect, for every item in the posts `tags` array, add a span to the output with the tag text. You'll see in the example above we've assigned each item in `page.tags` to a variable called `tag` this variable exists only between the `for` and `endfor` tags, attempting to use it outside of this will result in an error. Additionally naming conventions for things like lists and items of lists should keep this plural/singular format, so `page in site.pages`, `tag in page.tags` etc. Nothing in either Jekyll or Liquid enforce this convention but it does make your templates much easier for to read should they ever need editing.

If you open up your post in the browser now, you'll see a list of tags next to the date and author name.
![Tags](/images/20151212-getting-started-2/tags.png)

Liquid provides many other tags for using logic in your templates a complete list can be found in the Liquid [wiki](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers#tags)

Have a go at editing the templates for the blog pages yourself using the liquid template syntax. Perhaps you could try altering how your front page lists your posts or providing more information on them in the post list.

In the third part of this series we'll look at styling the blog we've created so far using Google's Material Design Lite CSS components.
