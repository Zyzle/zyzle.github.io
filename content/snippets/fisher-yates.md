+++
title = "Fisher-Yates Shuffle"
date = 2022-03-31
description = "Take a finite array and shuffle items in-place"

[taxonomies]
tags = ["snippet", "algorithm"]
language = ["typescript"]

[extra]
author = "Colin McCulloch"
+++


```typescript
shuffle(cards: any[]) {
  let m = cards.length;
  let t, i = 0;

  while(m) {
    i = Math.floor(Math.random() * m--);
    t = cards[m];
    cards[m] = cards[i];
    cards[i] = t;
  }
}
```

Formal definition:

```
for i from n−1 downto 1 do
  j ← random integer such that 0 ≤ j ≤ i
  exchange a[j] and a[i]
```