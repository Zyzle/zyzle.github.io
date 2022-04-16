+++
title = "Lets Write a Blog - Part 3: Jekyll"
date = 2021-12-03
draft = true

[taxonomies]
tags = ["blog", "jekyll"]

[extra]
author = "Colin"
+++

Jekyll is the oldest of the static site generators we'll be looking at it's 1.0 version appearing in 2013.


<!-- more -->

* one of the longer running projects (1.0.0 since 2013)
* designed around blogging
* one of the first to have gh-pages support(only one?)
* based on ruby gems rather than redistributable

## Installation

```bash
$ gem install bundler jekyll
```

```
Fetching bundler-2.2.31.gem
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /Library/Ruby/Gems/2.6.0 directory.
```

* I guess this was expected but I have no knowledge of ruby so some googling required on what to do
* opted for [RVM](https://rvm.io/rvm/install#explained) setup got side-tracked for 30min installing 2.7
* seems much more dependency heavy than the other two, many gems needed to be downloaded alongside 
* installation on windows/linux would probably need similar setup for ruby and dependencies

## Site setup

```bash
$ jekyll new jekyllblog
```

* no wizard setup
* new command gives not only bare-bones site but some default content, about page, 404 as well as config
[fig jekyll 1]

## Templating 101

Jekyll pre-loads a theme for you when a new site is generated [Minima](https://github.com/jekyll/minima)

> The goal of gem-based themes is to allow you to get all the benefits of a robust, continually updated theme without having all the theme’s files getting in your way and over-complicating what might be your primary focus: creating content.

yeah cool but also blegh. I want control over the theme I don't want to have to create a completely new gem and push it to the repository in order to use it.

One of the suggested ways to avoid this is to selectively override theme files in our local content, how do we know which files to override? By digging into the theme's gem installation path and looking at the files there... no really this is actually what they recommend in the Jekyll documentation :vomit:

There is however another option: "classic jekyll" this is much more like the other two static generators we've been looking at where theme files exist along side the project.

So first thing to do is remove entries to themes from the `_config.yml` and `Gemfile`:

```yaml
theme: minima
```

```ruby
gem "minima"
```

Next  I'll go ahead and create a few directories in the jekyllblog directory:

```
_includes
_layouts
_sass
assets
```

templates are similar to what we've already seen (link excerpts) 
slightly different to zola using yaml rather than toml 

## Our List/Entries pages

## Conclusion

