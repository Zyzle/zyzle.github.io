+++
title = "Markdown Tester Page"
date = 2022-03-26

[taxonomies]
tags = ["blog"]

[extra]
author = "Colin"
+++

This page is going to function as a test bed for the sites new styling, it's going to contain as many different markdown elements as possible in order to see whether or not we support them

<!-- more -->

# Headings

# Heading 1

## Heading 2

### Heading 3

#### Heading 4

##### Heading 5

###### Heading 6

# Unordered List

- one
- two
- three

# Ordered List

1. one
2. two
3. three

# Blocks

> some blockquote information text
>
> over multiple lines in markdown

```js
(() => {
    const bar = document.querySelector('#progress-bar');
    const post = document.querySelector('#docmain');
    const html = document.documentElement;
    const height = post.scrollHeight;

    window.addEventListener('scroll', () => {
        bar.style.width = 
            (html.scrollTop / (height - html.clientHeight))
            * 100 + '%';
    });
})();
```

# Text

Some text with *italic*, **bold**, ***bold italic***, `monospaced`, [a link](http://www.zyzle.dev), ~~strike~~

# Image

a basic image

![a caption for image](bg2.jpg)

one using our `figure` custom shortcode

{{ figure(path="bg2.jpg", caption="a caption for the image", alt="test image") }}