---
title: "Setup TailwindCSS Part 4"
date: 2020-04-12T12:24:54+02:00
draft: false
weight: 63
layout: tw_plain
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
        <link rel="stylesheet" href="{{ $css.RelPermalink }}" type="text/css"/>
    </head>
    <body>
        <main>
            {{ block "main" . }}
            {{ end }}
        </main>
    </body>
</html>
```

The content should now be easier to grasp.

With a few additional changes we can even make it kind of beautiful: 

```
/* simple.css */

body {
  margin: 0 auto;
  max-width: 42rem;
  line-height: 1.5;
  font-family: "Helvetica", "Arial", sans-serif;
  padding: 4em 1em;
  color: #566b78;
}

h1,
h2,
h3,
strong {
  color: #333;
}

h2, h3 {
  margin-top: 1em;
  padding-top: 1em;
}

pre {
  padding: 1em;
  border-left: 2px solid #63B3ED;
  border-bottom: 1px solid #d8dee9;
}

p > code, dt > code {
  padding: 2px 4px;
  vertical-align: text-bottom;
}

dt > code {
  margin-left: -4px;
}

code,
pre {
  background: #F7FAFC;
  color: #718096;
}

a {
  color: #ED64A6;
}
```

Now that we have some feel how for how site generation using hugo works, let's
step up our game a little bit by including TailwindCSS to generate our
stylesheet(s).

## Getting started with TailwindCSS

[TailwindCSS][tailwind] is a _utility-first_ CSS framework which makes styling
your HTML right in the DOM easy by providing a myriad of small utility classes.
Composing those small classes together makes for a rapid prototyping workflow.
Or as they describe it:

> Tailwind CSS is a highly customizable, low-level CSS framework that gives you
> all of the building blocks you need to build bespoke designs without any
> annoying opinionated styles you have to fight to override.

We will use [NPM][npm] to install TailwindCSS and its dependencies. Tailwind's
[official documentation][tailwind-install] has a very good step-by-step-guide so
we're just following along.

[tailwind]: https://tailwindcss.com
[tailwind-install]: https://tailwindcss.com/docs/installation/
[npm]: https://www.npmjs.com/

### Setting up TailwindCSS

1. In our site's root directory we'll run `npm init`. This will give us our
   initial `package.json` file. We continue by running `npm install tailwindcss`
2. We create another stylesheet in `assets`, called `main.css`. This will
   contain the three tailwind directives:
   ```
   /* main.css */

   @tailwind base;
   @tailwind components;
   @tailwind utilities;
   ```
3. In our site's root directory, we run `npx tailwindcss init`. This will give
   us a blank configuration file for TailwindCSS `tailwind.config.js` with the
   following content: 
   ```
   // tailwind.config.js
   module.exports = {
       theme: {},
       variants: {},
       plugins: [],
   }
   ```

That's it for TailwindCSS itself. However, we haven't yet declared who's
responsible for taking our `main.css` stylesheet and processing the `@tailwind`
directives so that we end up with a browser-readable stylesheet. As you can tell
from Tailwind's documentation, there are quite a few options. We will use
PostCSS because it's supported by hugo's build pipes.

### Integrating TailwindCSS with hugo

The first thing we need to do is adding a PostCSS configuration file to our
site's root directory: 

```
// postcss.config.js
module.exports = {
  plugins: [
    require('tailwindcss'),
    require('autoprefixer'),
  ]
}
```

Then it's time to modify our base template `layouts/_default/baseof.html`:

```
{{ $css := resources.Get "css/main.css" }}
{{ $css = $css | resources.PostCSS }}

<!doctype html>
<html class="no-js" lang="{{ .Site.LanguageCode }}">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="x-ua-compatible" content="ie=edge">
        <title>{{ .Site.Title }}</title>
        <meta name="description" content="">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link rel="stylesheet" href="{{ $css.RelPermalink }}" type="text/css"/>
    </head>
    <body>
        <main>
            {{ block "main" . }}
            {{ end }}
        </main>
    </body>
</html>
```

And, TADAAAAAAAAAAAARGGGGGH 😱😱😱

Good Lord, that's ugly. What happend!? What we're looking at here are Tailwind's
base styles, called **Preflight**:

> Built on top of normalize.css, Preflight is a set of base styles for Tailwind
> projects that are designed to smooth over cross-browser inconsistencies and make
> it easier for you to work within the constraints of your design system.

So as far as styling goes, we're basically starting from zero. We need to
restore our design by adding a bunch of rules to `main.css`.

[**PLEASE CLICK HERE TO CONTINUE**][next]

[next]: {{< ref "064_tailwind_setup_pt5.md#restoring-our-styles-with-tailwindcss" >}}
