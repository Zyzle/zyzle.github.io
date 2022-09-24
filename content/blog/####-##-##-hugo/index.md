+++
title = "Let's write a blog - Part 4: Hugo"
date = 2022-05-26
description = "Evaluating Hugo static site generator for use in my personal blog"
draft = true

[taxonomies]
tags = ["blog", "hugo"]

[extra]
author = "Colin McCulloch"
ghissue = 8
+++

[Hugo](https://gohugo.io/) bills itself as "The world's fastest framework for building websites" it's gained a lot of popularity in recent years, written in Go and using the `html/template` and `text/template` modules as the basis for its templating although no previous knowledge of Go is required to get up and running with it

<!-- more -->

## Installation

```bash
$ brew install hugo
```

- version 0.99.1, size 54Mb
- also available Homebrew for linux, MacPorts, Chocolatey and scoop for windows
- requires Go runtime

## Site setup

```bash
$ hugo new site hugoblog
```

No options to give here but does give a hint about the next steps:

```
1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/ or
   create your own with the "hugo new theme <THEMENAME>" command.
2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
3. Start the built-in live server via "hugo server".
```

created site dir looks like this [fig hugo 1] again mostly empty directories with a basic `config.toml` and a `default.md` "archetype"?

## Templating 101

create a new barebones theme with the cli

```bash
$ hugo new theme mytheme
```

but there's a problem

```bash
WARN 2021/11/19 01:49:10 found no layout file for "HTML" for kind "home": You should create a template file which matches Hugo Layouts Lookup Rules for this combination.
```

My bad forgot to add the theme to my `config.toml` file

```toml
baseURL = 'http://example.org/'
languageCode = 'en-us'
title = 'My New Hugo Site'
theme = "mytheme"
```

Now the compilation step is error-free, but nothing but a white screen is showing on the development URL, let's fix that.

## Our List/Entries pages

Hugo comes with page builder commands in the cli.

```bash
$ hugo new blog/first.md
```

## Conclusion
