+++
title = "Let's write a blog - Part 3: Jekyll"
date = 2022-05-15

[taxonomies]
tags = ["blog", "jekyll"]

[extra]
author = "Colin McCulloch"
ghissue = "https://github.com/Zyzle/zyzle.github.io/issues/6"
+++

[Jekyll](https://jekyllrb.com/) is the oldest of the static site generators we'll be looking at it's 1.0 version appearing in 2013. It was originally designed to be centred around blogging but has grown into a more fully featured static site generator.

<!-- more -->

The application is written in ruby and the sites and themes are build around gems. One additional benefit of Jekyll is it comes fully supported by GitHub which will be my preferred way of hosting the blog for now.

## Installation

This is where the first hurdles with Jekyll come in to play. For one thing apparently you shouldn't use the macOS system of Ruby for [various reasons](https://www.moncefbelyamani.com/why-you-shouldn-t-use-the-system-ruby-to-install-gems-on-a-mac/) so we're going to first have to install the [chruby](https://github.com/postmodern/chruby) tool. I'm going to skip the exact steps I went through for this but you can follow the instructions on the [Jekyll site](https://jekyllrb.com/docs/installation/macos/)

```bash
$ gem install bundler jekyll
```

As well as requiring a Ruby install Jekyll has quite a few gem dependencies making it one of the larger install footprints we'll be looking at.

## Site setup

First we make a new directory for our blog and run the command

```bash
$ bundle init
```

This gives us an empty `Gemfile` which we'll add our dependencies to:

```rb
source "https://rubygems.org"

gem "jekyll"
# this is required running ruby versions >3
gem "webrick"

```

Once these dependencies are in the gemfile we can use the bundle tool to install them with

```bash
$ bundle
```

Now we have our dependencies installed lets go ahead and create some basic directory structure for the site:

```
jekyllblog/
  |-- _includes/
  |-- _layouts/
  |-- _posts/
  |-- assets/
  |-- _config.yml
  +-- Gemfile
```

These directory names are fairly self explanatory, `_includes` will hold our template partials that can be included in other files, `_layouts` will contain our main template files, `_posts` will be where we put our blog page markdown files, and `assets` contains site assets.

The `_config.yml` is our Jekyll config file, we wont be adding much to this for the moment, just the sites title:

```yaml
title: My Blog
```

Now we have the basics we can run Jekyll in watch mode to serve our site for us while we develop

```bash
$ jekyll serve --watch
Configuration file: /Users/jekyllblog/_config.yml
            Source: /Users/jekyllblog
       Destination: /Users/jekyllblog/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
                    done in 0.02 seconds.
 Auto-regeneration: enabled for '/Users/jekyllblog'
    Server address: http://127.0.0.1:4000
  Server running... press ctrl-c to stop.
```

If we navigate to `http://127.0.0.1:4000` right now all we'll see is the output directory listing from webrick so let's go ahead and add some templates and content.

## Templating 101

Let's go through the templates for our site. First the base template which we'll call `default.html` which we'll place in our `_layouts` its content will be as follows:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>{{page.title}} | {{ site.title }}</title>
  </head>

  <body>
    <header>
      <h1>{{ site.title }}</h1>
      {% include navigation.html %}
    </header>
    <main>{{ content }}</main>
  </body>
</html>
```

If you read the [previous entry](@/blog/2022-04-16-zola/index.md#templating-101) in our series this base template should fairly familiar, Jekyll's templating engine [Liquid](https://shopify.github.io/liquid/) (originally developed by [Shopify](https://www.shopify.com/)) shares a lot of syntax with [Tera](https://tera.netlify.app/).

Unlike our Zola blog the Jekyll one does not define named content blocks the `{{content}}` template entry will automatically pick up the content either from a markdown page or from a sub-template that will extend this one. `{{site.title}}` simply comes from our `_config.yml` file.

We've separated our header navigation into an include in `_includes/navigation.html`

```html
<nav>
  <ul>
    <li><a href="{% link index.html %}">Home</a></li>
    <li><a href="{% link blog.html %}">Blog</a></li>
    <li><a href="{% link about.md %}">About</a></li>
  </ul>
</nav>
```

Here we use the Jekyll `link` command to construct a link to the specific pages in the root of the project, as we can see this link can either be to an explicit HTML file or a Markdown file that will be processed by Jekyll during its build.

So lets take a look at the `index.html` file that we've created:

```html
---
layout: default
title: Home
---

<p>This is the blog homepage</p>
```

Not much in the way of html in here, this is really just a front-matter telling Jekyll which layout template to plug this content in to and a title for the page.

## Our List/Entries pages

Our `blog.html` file will contain a title and give us a basic unordered list of the current blog entries on the site. Jekyll gives us a helpful `site.posts` variable that we can iterate over for this purpose:

```html
---
layout: default
title: Blog
---

<h1>List of blog posts</h1>

<ul>
  {% for post in site.posts %}
  <li>
    <a href="{{ post.url }}">{{ post.title }}</a>
  </li>
  {% endfor %}
</ul>
```

Again we're using the default template here and injecting this content directly. So far so simple.

The blog entry page template `blog-page.html` contains the following:

```html
---
layout: default
---

<h1 class="title">
	{{page.title}}
</h1>
<p class="subtitle"><strong>{{page.date}}</strong></p>

{{ content }}
```

All we need to do here is pull out the page title and date from the `page` object Jekyll gives us and wrap these in some tags to be displayed at the top of the content block. Notice this templates front-matter where we have a `layout` property defined, this tells Jekyll to forward the results of merging this template with its markdown file into the `content` block of the `default.html` template defined above.

The final part of these blog posts is the markdown itself, these files are placed in the `_posts` folder and their names are prefixed by the post release date e.g `2022-05-20-post-1.md` in these files we define a front-matter with template to use and title, then the content that makes up our blog post:

```markdown
---
layout: blog-page
title: My first post
---

This is the first blog post
```

## About Page

The about page we will be using exists as a markdown file at the root of the project `about.md`

```markdown
---
layout: page
title: About
---

This is the blog about page
```

It's template `page.html`  looks like this:

```html
---
layout: default
---

<article>
  <header>
    <h1>{{page.title}}</h1>
  </header>
  {{ content }}
</article>
```

Again we have a partial HTML file with some basic tags that wrap the markdown content and extract the page title into a header. As with the previous template this is then passed up to the `default.html` template and inserted into it's `content` section.

## Build and deploy

```bash
$ jekyll build
Configuration file: /Users/jekyllblog/_config.yml
            Source: /Users/jekyllblog
       Destination: /Users/jekyllblog/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
                    done in 0.02 seconds.
 Auto-regeneration: disabled. Use --watch to enable.
```

The build process is quick and generates the static HTML into a directory in our blog root `_site`. The directory structure 

```
_site/
  |-- 2002/
  |    +-- 05/
  |         |-- 06/
  |         |    +-- post-1.html
  |         +-- 07/
  |              +-- post-2.html
  |-- about.html
  |-- blog.html
  +-- index.html
```

As you can see Jekyll uses post dates to create a URL structure for the generated website, the compiled about page looks almost identical to our [previous example](@/blog/2022-04-16-zola/index.md#building-for-deployment)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>About | My Blog</title>
  </head>
  <body>
    <header>
      <h1>My Blog</h1>
      <nav>
        <ul>
          <li><a href="/">Home</a></li>
          <li><a href="/blog.html">Blog</a></li>
          <li><a href="/about.html">About</a></li>
        </ul>
      </nav>
    </header>
    <main>
      <article>
        <header>
          <h1>About</h1>
        </header>
        <p>This is the blog about page</p>
      </article>
    </main>
  </body>
</html>
```

Unlike our previous example Jekyll doesn't provide us with any extras out of the box although it does provide a wide catalogue of [plugins](https://jekyllrb.com/docs/plugins/) that can be used with the site.

## Conclusion

Jekyll is easy to get up and running with all be it if you have previous knowledge of managing a Ruby installation on your system, it's templating system is straightforward and the Liquid template engine is well documented. Jekyll's built URL structure is a little archaic and actually goes against what many SEO guides [recommend](https://www.sistrix.com/blog/want-slowly-kill-content-google-simply-use-directory-structure-dates/). The constructed site is smaller than the one generated by Zola, mostly because of the omitted extra files like the 404 page and sitemap, and comes in at around 20Kb.