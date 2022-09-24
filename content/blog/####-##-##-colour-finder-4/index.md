+++
title = "Finding Colours - Part 4: WebAssembly to the rescue"
date = 2022-09-22
description = "Part 4 of our series on finding dominant colours in images, looking here at using WebAssembly to manage the computationally heavy parts of the process"
draft = true

[taxonomies]
tags = ["web-dev", "fun", "optimization", "webassembly", "rust"]

[extras]
author = "Colin McCulloch"
+++

```bash
$ cargo generate --git https://github.com/rustwasm/wasm-pack-template
```

give it a project name: colors-wasm
