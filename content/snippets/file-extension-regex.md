+++
title = "File extension regex (js example)"
date = 2022-04-01

[taxonomies]
tags = ["snippet", "regex"]
language = ["javascript"]

[extra]
author = "Colin McCulloch"
+++

Simple regex for determining file extension

```js
var re = /(?:\.([^.]+))?$/;

var ext = re.exec("file.name.with.dots.txt")[1];   // "txt"
var ext = re.exec("file.txt")[1];                  // "txt"
var ext = re.exec("file")[1];                      // undefined
var ext = re.exec("")[1];                          // undefined
var ext = re.exec(null)[1];                        // undefined
var ext = re.exec(undefined)[1];                   // undefined

/**
(?:         # begin non-capturing group
  \.        #   a dot
  (         #   begin capturing group (captures the actual extension)
    [^.]+   #     anything except a dot, multiple times
  )         #   end capturing group
)?          # end non-capturing group, make it optional
$           # anchor to the end of the string
*/
```