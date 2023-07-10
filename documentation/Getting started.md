# Getting started with Eleventy

## Installation and basic configuration

Initialize npm within your project's empty folder.

`npm init -y`

Install Eleventy as a dev dependency.

`npm install @11ty/eleventy --save-dev`

Set the build and serve scripts in the `package.json` file.
```
"scripts": {
    "start": "npx @11ty/eleventy --serve",
    "build": "npx @11ty/eleventy"
},
```

Create the 11ty configuration file `.eleventy.js` at the root of the project and for now just define the input and output folders names.
*You are free to name those differently if needed.*
```
module.exports = function (eleventyConfig) {
  return {
    dir: {
      input: "src",
      output: "public",
    },
  };
};
```

## Let's display some content
Create the `src` folder and a new `index.md` file inside.
Let's write some basic markdown in this file, for example :
```
> This is some text in a blockquote.

[What about a link to Eleventy's documentation ?](https://www.11ty.dev/docs/)

**Nice !**
```

In the command line run `npm build`. A `public` folder with an `index.html` file will be generated. Take a look inside the html file, you should see the html equivalent of your markdown.
```
<blockquote>
<p>This is some text in a blockquote.</p>
</blockquote>
<p><a href="https://www.11ty.dev/docs/">What about a link to Eleventy's documentation ?</a></p>
<p><strong>Nice !</strong></p>
```

At this point the code is just sitting there without any html boilerplate. Let's set this up by creating an `_includes` folder under `src`, and a `base.njk` template file there.

> .njk files are related to Nunjucks, a template language that works quite well with Eleventy. Many other are compatible with Eleventy, see [documentation on 11ty template languages](https://www.11ty.dev/docs/languages/).

Insert the html boilrerplate into the `base.njk` file. You can copy-paste the following code or use Emmet's `!` shortcut to set it up.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body></body>
</html>
```

Let's go back to `index.md` and add a page title. We will first add a *front matter*, which is some *YAML* between a set of `---`, and will contain any variables we would like to pass to the layout, such as a title. It sits on top of the file, above the content.

```
---
title: Welcome to my Eleventy project
layout: base
---
```

Now in `base.njk` replace the `<title>` string by `{{ title }}`. Those sets of curly braces will take in some variables and output their content at build time. In the `<body>`, create an `h1` tag within and put `{{ title }}` there too. Finally, get the content there using `{{ content | safe }}`. The `content` variable is referring to our markdown body, and is followed by the "safe" keyword, with a pipe (`|`) in between. This is how you can use filters in 11ty, and `safe` is a built-in directive that allows safe output of HTML elements. You can try removing it, letting just `{{ content }}`, and will see on your page the literal HTML code as a string.

If it's not already running, start a Browsersync web server displaying our output in a browser, using the `npm start` command. It will watch for changes within the project and automatically refresh the page. Take a look in the browser (usually at `http://localhost:8080/`) and see how beautiful is this! Well, hum, we can surely make it prettier with some CSS styles a bit later.