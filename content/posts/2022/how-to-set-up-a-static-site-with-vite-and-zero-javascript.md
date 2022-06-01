---
title: How to set up a static site with Vite and zero Javascript
date: 2021-10-21
image: /assets/images/2022/vite-zero-js-header.jpg
---

## Why not use javascript on a site?
In a web developer’s eternal quest for efficiency and fast page speeds, nothing beats a purely static site.

Removing Javascript from the equation decreases the requests the browser has to make, as well as the ‘time to interactive’ Google page score.

No Javascript does mean no:

* Interactivity
* Data fetching
* Front-end analytics

In my case, I have a small agency website [MB Media](mbmedia.co.nz) which doesn’t need updating very often. As a front for my web work, I want it to be quick to showcase what I can do for clients!

## What is Vite?

[Vite](https://vitejs.dev/guide/) is a very quick opinionated build tool that provides a dev server and build command to bundle and optimise your exported code.

It was originally designed for Vue but is now framework-agnostic. It’s faster than Webpack and offers a bunch of features out of the box such as typescript support and Hot Module Replacement (HMR).

## My tools

I’ve chosen to use Vite as the build tool, and [Tailwind](https://tailwindcss.com/) for the styling. Storing the source code on Github means I don’t need a CMS - I can make any small edits locally and push my updates.

Hosting it with [Netlify](https://www.netlify.com/) or [Render](https://render.com/) means it gets deployed on a global CDN, can rebuild the site when the Git repo changes, and better yet - hosting for static files is free!

## How to setup Vite

This the project structure we'll end up with:
![Vite project structure](/assets/images/2022/vite-project-structure.png)

According to their docs, to create a new Vite project open VS Code and the integrated terminal and run:

```
npm create vite@latest
```

Choose a name and select ‘vanilla’ as the template:

To start everything up, follow the prompts:

```
cd my-vite-project
npm install
npm run dev
```

If you haven’t used Vite before, try editing the `index.html` or `js/main.js` file to see just how fast the reloading is!

To build your site ready for production run: `npm run build` to create your index.html and bundled assets into the `dist/` directory.

#### Relative Links

By default Vite uses absolute paths, so if you use an extension like [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) to preview your `dist/index.html`, the console will show 404 errors.

To fix this, we can tell Vite to use relative URLs by adding the flag `—base=./`  in our build command. Open the package.json file, and edit the scripts section to look like the following:

```javascript
"scripts": {
    "dev": "vite",
    "build": "vite build --base=./",
    "preview": "vite preview"
  },
```

Now using



## Deleting javascript and tidying up the project

Let's go ahead and delete `main.js`, and edit the `index.html` file to take out references to it:
```html
<div id="app"></div>
<script type="module" src="/main.js"></script>
```

Link your css file into the `index.html` since it was referenced in the now deleted `main.js`.
```html
<link rel="stylesheet" href="./style.css" type="text/css" />
```

To make managing the project a bit easier, we can move our working files into a new directory.

Create a `src/` folder and move `index.html` and `style.css` into it.

To let Vite know we've changed the main file path, create a `vite.config.js` file and add the following:

```javascript
module.exports = {
    root: 'src',
    build: {
      outDir: '../dist',
      emptyOutDir: true
    }
}
```

### (optional) Add compression plugin

To optimise your build bundle even further you can install [vite-plugin-compress](https://github.com/alloc/vite-plugin-compress) with `npm i -D vite-plugin-compress`

Import the plugin into your `vite.config.js` with the default options so it now looks like this:

```javascript
import compress from 'vite-plugin-compress'

module.exports = {
    root: 'src',
    build: {
      outDir: '../dist',
      emptyOutDir: true
    },
    plugins: [
      compress(),
    ]
}
```

## Install Tailwind

To install Tailwind and an auto-prefixer, run `npm i -D tailwindcss autoprefixer`.

Create a `tailwind.config.js` file and add in your rules
```javascript
module.exports = {
    content: ["./**/src/*.{html,js}"],
    theme: {
      extend: {},
    },
    plugins: [],
  }
```

In your `src/style.css` file, add the base Tailwind styles:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Lastly, link autoprefixer and tailwind together with a new file `postcss.config.js` and add in the following:

```javascript
module.exports={
    plugins: [
     require('tailwindcss'),
     require('autoprefixer'),
     ]
    }
```

To test it out, edit your `src/index.html` folder and add a class name like so:
```html
<h1 class="text-cyan-500">Test</h1>
```


### * Bonus *

A Tailwind generator like [Tails](https://devdojo.com/tails/app) can help you develop your HTML and styling very quickly!

---

### Further reading

* [Moving from Weback to Vite](https://css-tricks.com/adding-vite-to-your-existing-web-app/)
* [Static vs Server-rendered vs Incremental Static Generation](https://blog.logrocket.com/incremental-static-regeneration-next-js/))
