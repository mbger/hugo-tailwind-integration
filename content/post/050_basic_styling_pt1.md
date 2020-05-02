---
title: "Basic Styling Part 1"
date: 2020-04-12T12:24:54+02:00
draft: false
weight: 50
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
tell it how to do so using templates.

## Adding Templates

We will start by adding 3 default templates located in `layouts/_default`:

```
├── archetypes
│   └── default.md
├── config.toml
├── content
│   └── post
│       └── hello-world.md
├── data
├── layouts
│   ├── _default
│   │   ├── baseof.html
│   │   ├── list.html
│   │   └── single.html
├── static
└── themes
```

`layouts/_default/baseof.html`
:    This template describes the overarching structure of our site. It includes
     a `block` placeholder which we use to tell hugo where to put the content in
     our basic HTML structure of the site
     ```
     <!doctype html>
     <html lang="{{ .Site.LanguageCode }}">
         <head>
             <meta charset="utf-8">
             <meta http-equiv="x-ua-compatible" content="ie=edge">
             <title>{{ .Site.Title }}</title>
             <meta name="description" content="">
             <meta name="viewport" content="width=device-width, initial-scale=1">
         </head>
         <body>
             <main>
                 {{ block "main" . }}
                 {{ end }}
             </main>
         </body>
     </html>
     ```
    
`layouts/_default/list.html`

:   is our default list template. If we don't have a special template for our
    home page defined (and we do not), then our homepage will be rendered using
    this template. Our homepage will contain a link for each of our content
    pages inside a list.
    ```
    {{ define "main" }}
    <ul>
    {{ range where site.RegularPages "Type" "in" site.Params.mainSections }}
        <li><a href="{{ .RelPermalink }}">{{ .Title }}</a></li>
    {{ end }}
    </ul>
    {{ end }}
    ```

`layouts/_default/single.html`

:    This template describes how each content page is rendered. We will just put
     the page's Title inside an `<h1>` tag and put the content underneath.
     ```
     {{ define "main" }}

     <h1>{{ .Title }}</h1>
     {{ .Content }}

     {{ end }}
     ```

Running `hugo serve -D` now should render links to our content right at the
home page. Clicking the link should take us to the corresponding content page.
You can tell hugo to always navigate to the page you're currently working by
invoking `hugo serve -D --navigateToChanged`.

Our website is now up and running, though heinously looking. Let's fix that by
adding some style.

### Adding Style

To add some styling to our page, we need to create a stylesheet. We'll create
another top-level directory called `assets` and add a new CSS file `simple.css`.

Right now the content is hard to read because it spans the entire page. Let's
limit the width of the body and center it by adding the following rules to `simple.css`:

```
body {
  margin: 0 auto;
  max-width: 42rem;
}
```

Adding those rules to the file doesn't change the look of our site just yet. We
need to reference the stylesheet from within our base template
`layouts/_default/baseof.html`:

```
{{ $css := resources.Get "css/simple.css" }}

<!doctype html>
<html class="no-js" lang="{{ .Site.LanguageCode }}">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="x-ua-compatible" content="ie=edge">
        <title>{{ .Site.Title }}</title>
        <meta name="description" content="">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link rel="stylesheet" href="{{ $css.RelPermalink }}" type="text/css" media="screen" />
    </head>
    <body>
        <main>
            {{ block "main" . }}
            {{ end }}
        </main>
    </body>
</html>
```

The content should now be [easier to grasp][next].

[next]: {{< ref "051_basic_styling_pt2.md#adding-style">}}
