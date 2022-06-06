+++
title = "Generate UUID v4"
date = 2022-06-04

[taxonomies]
tags = ["snippet", "algorithm"]
language = ["typescript", "javascript"]

[extra]
author = "Colin McCulloch"
+++

Generates a UUID using the RFC 4122 specification.

Version 4 UUIDs take the form `xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx` where `x` is any hex digit and `y` is in `[8, 9, a, b]`

Assuming these are generated with sufficient entropy, 1B uuids every second for 100 years would be ~50% chance of collision.

```typescript
('' + 1e7 + -1e3 + -4e3 + -8e3 + -1e11)
  .replace(/1|0/g, () => {
    return (0 | Math.random() * 16).toString(16);
  });
```