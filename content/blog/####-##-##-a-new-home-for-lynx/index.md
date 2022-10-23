+++
title = "A new home for Lynx"
description = "In this post, I'll be looking at alternatives to host Lynx over Heroku now that free Dyno's are being removed"
date = 2022-09-23
draft = true

[taxonomies]
tags = ["lynx", "rust", "rocket", "heroku", "cloud hosting", "fly.io"]

[extra]
author = "Colin McCulloch"
type = "article"
+++

Now Heroku has [decided to shut down their free dyno offering](https://blog.heroku.com/next-chapter), I'll need to find a new home for Lynx. This looks like a good time to evaluate the various hosting options available for cloud functions and see what else is out there on the market

<!-- more -->

Alternatives:

[render](https://render.com/docs/deploy-rocket-rust)
[koyeb](https://www.koyeb.com/tutorials/deploy-a-rust-web-app-with-rocket)
[qovery (AWS)](https://hub.qovery.com/guides/tutorial/how-to-deploy-a-rust-rest-api-application-on-aws-with-ease/)
[google cloud run](https://cprimozic.net/blog/rust-rocket-cloud-run/)
[fly.io](https://fly.io/) <-- using this one