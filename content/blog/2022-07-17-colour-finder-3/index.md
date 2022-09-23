+++
title = "Finding Colours - Part 3: Inefficiencies Begone!"
date = 2022-07-17
description = "Part 3 of our series on finding dominant colours in images, removing inefficiencies in our existing code"
draft = true

[taxonomies]
tags = ["web-dev", "fun", "optimization"]

[extra]
author = "Colin McCulloch"
+++

We left off [part 2](@/blog/2022-07-16-colour-finder-2/index.md) with code that worked and gave good results but was disastrously inefficient, taking minutes to finish the algorithms run on higher resolution images. In this post, we're going to try and fix some of those issues.

<!-- more -->

## Measuring the problems

So to start with this, we'll need some numbers. I have some idea of where the problems in the code lie but I want to back this up with some numbers before I start making changes.

To test the theory we can do basic timing checks on parts of the code by wrapping sections with start and finish `Date.now()`s like so:

```js
const cnc = Date.now();
newClusters = calcNewClusters(kClusters, colorData);
console.log(`Calc new clusters: ${Date.now() - cnc}ms`);
```

I picked out 4 areas of the code to start off with and was actually surprised by some of the results:

```
Build Colour Data: 289ms
Initial clusters: 0ms
Calc new clusters: 20900ms
Calc distance shift: 0ms
```

I wasn't surprised that the initial cluster and distance shift calculations were quick, as these weren't particularly difficult for the system to run. I was more surprised at how quick the `colorData`:

```js
const bcd = Date.now();
let colorData = [];

for (i = 0; i < imageData.length; i += 4) {
	const colStr = [
		imageData[i],
		imageData[i + 1],
		imageData[i + 2],
	].join(",");

  colorData.push(colStr);
}

colorData = [...new Set(colorData)];

colorData = colorData.map((v) => {
  const rgb = v.split(",");
  return {
    r: parseInt(rgb[0]),
    g: parseInt(rgb[1]),
    b: parseInt(rgb[2]),
  };
});
console.log(`Build Colour Data: ${Date.now() - bcd}ms`);
```

I was expecting this to be more take a lot longer than the <300ms time being shown, we may come back to this later as it could probably be optimised further, but for the moment there is one stand-out section of the code that needs optimising, the new clusters calculations.

```
clusteredData: 26095ms
newKs: 2ms
```
