+++
title = "Recursive find and remove"
date = 2022-03-31

[taxonomies]
tags = ["snippet", "os"]
# language = ["bash"]

[extra]
author = "Colin"
+++

Find all files/directories based on a name pattern and remove them recursively from a base directory.

For files:

```bash
find . -name "*.bak" -type f -exec rm {} \;
```

For directories:

```bash
find . -name "node_modules" -type d -exec rm -rf {} \;
```