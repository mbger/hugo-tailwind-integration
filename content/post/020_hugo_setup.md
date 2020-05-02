---
title: "Hugo Setup"
date: 2020-04-12T12:24:54+02:00
draft: false
weight: 20
layout: plain
---

Integrating Hugo with TailwindCSS
==========================================

You want to start your own blog and don't know much about web development. Then
you're at the point where I was a couple of months ago. Building a blog using a
static site generator seems to be the most popular choice right now. There are
many static site generators available, so go with one of them. My choice is
[hugo](https://gohugo.io). 

## Basic Hugo setup

First, you need to create your site. Run `hugo new site <site-path>` to create
the basic directory structure. It should look like this: 

```
├── archetypes
│   └── default.md
├── config.toml
├── content
├── data
├── layouts
├── static
└── themes
```

With the basic structure in place, we can already start to [add some content][next]. 

[next]:{{< ref "030_hugo_setup_content.md#adding-content" >}}
