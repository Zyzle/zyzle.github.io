+++
title = "Let's write a blog - Part 1: Intro"
date = 2022-04-09
description = "An Introduction to our series comparing various static site generators"

[taxonomies]
tags = ["blog", "zola", "jekyll", "hugo"]

[extra]
author = "Colin"
+++

I've been wanting to get back into blogging for some time but have no idea what to write about to start me off, so why not write about choosing a blog tool and it's setup. We're going to go through some of the tools I've considered and the reasons behind my final choice as well as the process of creating a template and deploying the blog.

<!-- more -->

## Why static site?

For a couple of reasons I've chosen to look at static site generators for this task. Firstly while I wan't to create, style, and release my content as simply as possible I want to be in control of it. With the blogging platforms available today I don't really find I get this. 

Another big plus for me with the static site generators is I can keep all of the content in git in simple text files (usually markdown). The benefit if this to me is it allows my content to be stored in a version control system, I can look back through the history of drafts to see how things have progressed or even revert to older versions if I go off in a direction I later want to revise. 

I've always been a fan of reducing page bloat as much as possible, these days even simple articles are bogged down with so much unnecessary scripts, includes, and poorly optimised assets that page sizes are out of control. I chose at random a reasonably sized Medium blog post of around 1300 words, this clocked in at 4.4Mb! The actual readable text content of the page consisting of only 8Kb of this total, so yeah <0.2%  of the page is actual content.

> Maciej Ceglowski has a great talk about this, the text of which can be found [here](https://idlewords.com/talks/website_obesity.htm)

From what I can see all of the most common blogging platforms these days share this issue with Medium and I don't really want to have to hand-crank every page of my blog from scratch so the obvious solution seems to be a static site generator.

These have become popular with content creators in recent years because they allow you to keep your content blog entries, pages, sections etc. in a set of simple markdown files which can then be processed by the engine into a set of pre-defined html templates creating a more complete looking web experience for the end user. 

There are many of these tools available currently but we'll be focusing on 3 for the purposes of this blog, basically because I don't want to have to learn more than 3 tools and re-implementing the site more than that.

The three tools I've chosen to look at are:

### [Hugo](https://gohugo.io/)

One of the most popular static site generators, more generic site template engine than the other two we'll be looking at. Claims to be one of the fastest static site generators around. Written in Go and uses Go's `html/template` and `text/template` libraries for its templating.

### [Jekyll](https://jekyllrb.com/)

One of the older static site generators and the first (only?) one to be integrated into github pages. Built in Ruby and does appear to require some Ruby knowledge to use.

### [Zola](https://www.getzola.org/)

The newest of the three we'll be looking at written in Rust and using a custom template engine similar to Jinja. Has a strong opinion on how content should be structured but has good freedom in it's templates.


The plan is to implement the new blog, or at least a very small subset of it in each of these 3 tools  looking at things like: 

* simplicity of setup
* ease of use
* flexibility
* build/deploy pipeline

## What I'm looking to achieve 

Looking to build a new home for the blog I thought up a few basic feature requirements:

* requirements:
    * ease of adding blog entries
    * list by date published
    * permalinking
    * ability to add custom template
    * syntax highlighting for code blocks
    * styled figures/quotes/callout sections
    * page/deep links
* nice to have's:
    * no bespoke markdown
    * simple site structure
    * taxonomies support
    * ability to draft entries
    * template that can be reused
    * integration with GH-pages deploy

For the first part of this series I'll create the same simple site template and basic page set in each of the three static site generators. I'm going to start with a simple homepage showing site title, a navigations section linking to homepage and an about page, a list of two blog entries showing title a summary of the page content and a link to continue reading. I'll also need another two templates, one for the free-form About page and one for our blog posts. 

The basic sites first iteration should look something like the following:

{{ figure(path="homepage.png", isColocated=true, caption="The homepage", alt="the basic homepage layout") }}
{{ figure(path="about.png", isColocated=true, caption="An about page", alt="a simple about page") }}
{{ figure(path="blog-list.png", isColocated=true, caption="The blog list page sorted by date", alt="the blog list page")}}
{{ figure(path="blog-page.png", isColocated=true, caption="A basic post page", alt="a basic post page") }}

I'll also try and keep parity between the HTML generated by each 

By the time you read this I'll have a decision made on which tool to use (spoiler it's Zola), in the coming posts I'll go through what I've found evaluation the 3 of these and hopefully my decision will become clear.