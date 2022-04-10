+++
title = "Lets Write a Blog - Part 2: Zola"
date = 2022-04-16
draft = true

[taxonomies]
tags = ["blog", "zola"]

[extra]
author = "Colin"
+++

Zola (formally Gutenberg) is a SSG written in Rust. Zola has it's own templating engine [Tera](https://tera.netlify.app/) written by the same author and follows a similar design in it's templating to Jinja 2. Distributed as a single binary Zola has a much smaller footprint than the other SSGs we'll be looking at, it also promises to be fast (comparable to Hugo) and runs from a simple augmented markdown including shortcodes and custom internal linking.

<!-- more -->

## Installation

Zola comes as a single binary distributable, on MacOS the simplest way to install it is with homebrew.

```bash
$ brew install zola
```

Zola is also available on MacPorts, Chocolatey, Scoop, and various linux package structures. With no external dependencies Zola is probably the simplest of the 3 tools we're looking at.

The version we'll be using is the current stable 0.15.3 (23rd Jan 2022) at the time of writing, with a total size of ~19mb it also has one of the smallest install footprints. 

## Site setup

Zola has a simple setup wizard that runs through some basic configuration options

```bash
$ zola init zolablog
```

Wizard process asks: 

    * site url
    * Y/n Sass compilation
    * y/N syntax highlighting
    * y/N search index

created site has following structure:

```
zolablog/
|-- content/
|-- sass/
|-- static/
|-- templates/
|-- themes/
+-- config.toml
```

A few empty directories and a `config.toml` with the minimal configuration I selected in the site setup wizard. The full list of Zola configuration options can be found [in the zola documentation](https://www.getzola.org/documentation/getting-started/configuration/).

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
    <title>{{ config.title }}</title>
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

This is a fairly standard html5 template using some semantic html tags. Tera has 3 types of expression delimiters:

* `{{` and `}}` for expressions
* `{%` and `%}` for statements
* `{#` and `#}` for comments

In the above we pick out the site title from the configuration file with `config.title`. We define a block called `content` inside the sites `<main>` tags that we'll be substituting with out page content in later templates. For now lets have a look at the `navigation.html` template that will be included in the the `header` tag.

```j2
<nav>
    <ul>
        
    </ul>
</nav>
```

The `get_url` function gives the permalink for a given path the `@/` will be treated as an internal link to the root of the Zola `content` directory. Lets have a look at the first entry here, the `_index.md`

```markdown
+++
page_template = "page.html"
template = "home.html"
+++
```

## List/Entries pages

Existing at the root of the content directory this file will be rendered whenever our blog root or any of it's section pages are accessed. When our site index is accessed the `home.html` template will be rendered, it consists of the following:

```j2
{% extends "base.html" %}

{% block content %}
<ul>
    {% set post_section = get_section(path=config.extra.homepage_section ~ "/_index.md") %}
    {% for post in post_section.pages %}
    <li>
        <a href="{{ post.permalink | safe }}">{{ post.title }}</a>
        {{ post.summary | safe }}
        <a href="{{ post.permalink }}#continue-reading">Continue reading</a>
        <br />
        <br />
    </li>
    {% endfor %}
</ul>
{% endblock %}
```

First we extend the `base.html` template, this lets Zola know that the base template will be used and we'll be substituting into sections defined within it. The `block` command tells Zola which section we'll be placing our content in to. Zola offers us the `get_selection` function allowing us to pick out an arbitrary sections pages and iterate over them to produce the desired homepage list. We use the `homepage_section` we defined in the configuration earlier and look at the associated `_index.md` in that directory. The markdown file contains just a simple frontmatter: 

```md
+++
sorted_by = "date"
redirect_to = "/"
page_template = "blog-page.html"
+++
```

Here we're telling Zola to order the entries by date, newest first, that directly accessing the `blog/` url will redirect to the sites homepage, and that any pages in this section should be rendered with the `blog-page.html` template:

```j2
{% extends "base.html" %}

{% block content %}
<h1 class="title">{{ page.title }}</h1>
<p class="subtitle"><strong>{{ page.date }}</strong></p>
{{ page.content | safe }}
{% endblock content %}
```

There's not much in this template, again we're extending the `base.html` file and inserting our content into the named block. 


## Building for deployment

```bash
$ zola build 
Building site...
Checking all internal links with anchors.
> Successfully checked 0 internal link(s) with anchors.
-> Creating 3 pages (0 orphan), 1 sections, and processing 0 images
Done in 73ms.
```

The build process is simple and fast, by default outputs the site to the `public` directory. The output looks like the following:

```
public/
|-- about/
|   +-- index.html
|-- blog/
|   |-- first/
|   |   +-- index.html
|   |-- second/
|   |   +-- index.html
|   +-- index.html
|-- 404.html
|-- elasticlunr.min.js
|-- index.html
|-- robots.txt
|-- search_index.en.js
+-- sitemap.xml
```

So our pages have been placed into individual HTML files using the templates we defined in the previous steps as an example here's what the about page looks like compiled:

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <title>Zolablog</title>
    </head>
    <body>
        <header>
            <h1>Zolablog</h1>
            <nav>
    <ul>
        <li><a href=https://www.zyzle.dev/>Home</a></li>
        <li><a href=https://www.zyzle.dev/about/>About</a></li>
    </ul>
</nav>
        </header>
        <main>
            
<h1 class="title">About</h1>
<p>The about page...</p>


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

There's also a basic `robots.txt` which points to our auto-generated `sitemap.xml` and a JS search index file as well as a copy of [Elasticlunr](http://elasticlunr.com/) that can be hooked up to provide search functionality to our site. These are all extra features generated that I may take a deeper dive into later on but for now we're done with the basic setup.

## Conclusion

Zola was easy to get up and running with, the templating engine is simple and seems well documented. Zola provides a lot of good integrations for more advanced features such as search and site feeds we'll look at in later sections. The final built site for our Zola example comes in at 56k.