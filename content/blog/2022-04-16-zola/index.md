+++
title = "Let's write a blog - Part 2: Zola"
date = 2022-04-16
description = "Evaluating the Zola static site generator for use in my blog"

[taxonomies]
tags = ["blog", "zola"]

[extra]
author = "Colin McCulloch"
ghissue = 5
type = "article"
image = ""
+++

Zola (formally Gutenberg) is an SSG written in Rust. Zola has its own templating engine, [Tera](https://tera.netlify.app/) created by the same author and follows a similar design in its templating to Jinja 2. Distributed as a single binary Zola has a much smaller footprint than the other SSGs we'll be looking at, it also promises to be fast (comparable to Hugo) and runs from a simple augmented markdown including shortcodes and custom internal linking.

<!-- more -->

## Installation

Zola comes as a single binary distributable, on macOS the simplest way to install it is with homebrew.

```bash
$ brew install zola
```

Zola is also available on MacPorts, Chocolatey, Scoop, and various Linux package structures. With no external dependencies, Zola is probably the simplest of the 3 tools we're looking at.

The version we'll be using is the current stable 0.15.3 (23rd Jan 2022) at the time of writing, with a total size of ~19Mb it also has one of the smallest install footprints. 

## Site setup

Zola has a simple setup wizard that runs through some basic configuration options

```bash
$ zola init zolablog
```

Wizard process asks (defaults shown): 

    * site URL
    * Sass compilation (Y/n)
    * syntax highlighting (y/N)
    * search index (y/N)

The created site has the following structure:

```
zolablog/
  |-- content/
  |-- sass/
  |-- static/
  |-- templates/
  |-- themes/
  +-- config.toml
```

A few empty directories and a `config.toml` with the minimal configuration I selected in the site setup wizard. The full list of Zola configuration options can be found [in the Zola documentation](https://www.getzola.org/documentation/getting-started/configuration/). The config will look something like the following:

```toml
# The URL the site will be built for
base_url = "https://example.com"

title = "My Blog"

# Whether to automatically compile all Sass files in the sass directory
compile_sass = true

# Whether to build a search index to be used later on by a JavaScript library
build_search_index = false

[markdown]
# Whether to do syntax highlighting
# Theme can be customised by setting the `highlight_theme` variable to a theme supported by Zola
highlight_code = true

[extra]
# Put all your custom variables here
```

Let's start the development server with the following

```bash
$ zola serve
Building site...
Checking all internal links with anchors.
> Successfully checked 0 internal link(s) with anchors.
-> Creating 0 pages (0 orphan), 0 sections, and processing 0 images
Done in 56ms.

Web server is available at http://127.0.0.1:1111

Listening for changes in /Users/zolablog{/Users/zolablog/config.toml, content, sass, static, templates}
Press Ctrl+C to stop
```

Zola automatically runs in watch mode so any changes you make will be built and served.

## Templating 101

I'm not going to work off of a base template for Zola as everything can be built simply from scratch.

We'll start with a `base.html` file that all our other templates will inherit from:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>{% block title %}{{ config.title }}{% endblock title %}</title>
  </head>
  <body>
    <header>
      <h1>{{ config.title }}</h1>
        {% include "navigation.html" %}
    </header>
    <main>
      {% block content %}{% endblock %}
    </main>
  </body>
</html>
```

This is a fairly standard html5 template using some semantic HTML tags. Tera has 3 types of expression delimiters:

* `{{` and `}}` for expressions
* `{%` and `%}` for statements
* `{#` and `#}` for comments

In the above, we pick out the site title from the configuration file with `config.title` and wrap this with a `title` named block which we can override later. We also define a block called `content` inside the site's `<main>` tags that we'll be substituting with our page content in later templates. For now, let's have a look at the `navigation.html` template that will be included in the `header` tag.


```html
<nav>
  <ul>
    <li><a href="{{/* get_url(path="@/_index.md") */}}">Home</a></li>
    <li><a href="{{/* get_url(path="@/blog/_index.md") */}}">Blog</a></li>
    <li><a href="{{/* get_url(path="@/about.md") */}}">About</a></li>
  </ul>
</nav>
```

The `get_url` function gives the permalink for a given path the `@/` will be treated as an internal link to the root of the Zola `content` directory. Let's have a look at the first entry here, the `_index.md`

```toml
+++
page_template = "page.html"
+++
```

This simple markdown frontmatter does is tell Zola that any pages contained in this directory should be rendered with the `page.html` template, we'll come back to the contents of this later. 

Zola will also render the `index.html` file found in the templates directory at the site root

```j2
{% extends "base.html" %}

{% block title %}Home | {{/* super() */}}{% endblock title %}

{% block content %}
<p>This is the blog homepage</p>
{% endblock content %}
```

Here we are again extending the site's `base.html` and overriding the named blocks, first our `title` block, this has the interesting `super()` method which places the blocks existing content defined in the base template at the calling location allowing us to keep the sites name without having to redefine it in every template.

We just add a simple static paragraph of text into the `content` block of the template.

## Pages

Zola considers directories inside the `content` directory as sections, we'll create a new dir and file `/blog/_index.md` and add some TOML frontmatter that will tell Zola how to render this sections root page and any sub-pages:

```toml
+++
title = "List of blog posts"
sort_by = "date"
template = "blog.html"
page_template = "blog-page.html"
+++
```

This tells Zola to render the `blog/` index URL using the `blog.html` template and apply the `blog-page.html` template to any markdown files in this section folder, the `sort_by` directive also tells Zola to sort the articles in this section by date, default newest first. 

### Blog list

Let's look at the index page template `blog.html` first:

```j2
{% extends "base.html" %}

{% block title %}{{ section.title }} | {{/* super() */}}{% endblock title %}

{% block content %}
<h1 class="title">
  {{ section.title }}
</h1>
<ul>
  {% for page in section.pages %}
  <li><a href="{{ page.permalink | safe }}">{{ page.title }}</a></li>
  {% endfor %}
</ul>
{% endblock content %}
```

As we've seen before we start by extending the `base.html` template and override the title section using `super` again to keep our site title, then we insert it into our `content` block. We use `section.title` to pull out the title we defined in the front matter in `_index.md`.

Now we get on to showing the blog list. Zola offers us the `section.pages` variable that gives us a sorted list of page objects representing pages in our section that we can iterate over and provide links to using their `permalink` and `title` variables.

### Blog Page

```j2
{% extends "base.html" %}

{% block content %}
<h1 class="title">
	{{ page.title }}
</h1>
<p class="subtitle"><strong>{{ page.date }}</strong></p>
{{ page.content | safe }}
{% endblock content %}
```

There's not much in this template, again we're extending the `base.html` file and inserting our content into the named block. We'll also apply the Zola `safe` filter to the content because no html escaping is required for the content generated from the markdown by Zola.

### About

Our final template is the top-level `page.html` we use to render the `about.md` about page content.

```j2
{% extends "base.html" %}

{% block title%}{{ page.title }} | {{/* super() */}}{% endblock title %}

{% block content %}
<article>
  <header>
    <h1>{{ page.title }}</h1>
  </header>
  {{ page.content | safe }}
</article>
{% endblock content %}
```

There's nothing here we haven't looked at already, it's just a cut-down version of the blog page template.

## Building for deployment

```bash
$ zola build
Building site...
Checking all internal links with anchors.
> Successfully checked 0 internal link(s) with anchors.
-> Creating 3 pages (0 orphan) and 1 sections
Done in 13ms.
```

The build process is simple and fast, by default outputs the site to the `public` directory. The output looks like the following:

```
public/
  |-- about/
  |    +-- index.html
  |-- blog/
  |    |-- first/
  |    |    +-- index.html
  |    |-- second/
  |    |    +-- index.html
  |    +-- index.html
  |-- 404.html
  |-- index.html
  |-- robots.txt
  +-- sitemap.xml
```

So our pages have been placed into individual HTML files using the templates we defined in the previous steps as an example, here's what the about page looks like compiled:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>About | My Blog</title>
  </head>
  <body>
    <header>
      <h1>My Blog</h1>
      <nav>
        <ul>
          <li><a href="https://example.com/">Home</a></li>
          <li><a href="https://example.com/blog/">Blog</a></li>
          <li><a href="https://example.com/about/">About</a></li>
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

We haven't defined a template for a 404 page so Zola generates us a default one:

```html
<!doctype html>
<title>404 Not Found</title>
<h1>404 Not Found</h1>
```

There's also a basic `robots.txt` which points to our auto-generated `sitemap.xml` and if we had chosen it when we did the setup wizard we would have a JS search index file as well as a copy of [Elasticlunr](http://elasticlunr.com/) that can be hooked up to provide search functionality to our site. These are all extra features generated that I may take a deeper dive into later on but for now, we're done with the basic setup.

## Conclusion

Zola was easy to get up and running with, the templating engine is simple and seems well documented. Zola provides a lot of good integrations for more advanced features such as search and site feeds we'll look at in later sections. The final built site for our Zola example comes in at 32Kb.