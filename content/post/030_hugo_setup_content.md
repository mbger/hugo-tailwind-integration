---
title: "Hugo Setup: Content"
date: 2020-04-12T12:24:54+02:00
draft: false
weight: 30
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

With the basic structure in place, we can already start to add some content. 

## Adding Content

To add content, run the following command from within your site's root
directory. 

```
hugo new post/hello-world.md
```

This will create a new directory below `content/` called `post` with the
`hello-world.md` file in it:

```
├── archetypes
│   └── default.md
├── config.toml
├── content
│   └── post
│       └── hello-world.md
├── data
├── layouts
├── static
└── themes
```

Now that we added some content to our site, it's finally time to preview our
site with hugo's built-in server. To spin up a local web server hosting the site
run the following command: 

```
hugo serve -D
```

The web browser will be started and listen's to incoming connections on
`http://localhost:1313/`. The `-D` flag tells hugo that you want to include
content marked as `draft` in the generated output. Direct your browser to
[http://localhost:1313](http://localhost:1313/) and you'll see: _nothing_.
**Why?** Because hugo doesn't know how to render your content, yet. We need to
tell it how to do so [using templates][next].

[next]:{{< ref "040_hugo_setup_templates.md#adding-templates" >}}

