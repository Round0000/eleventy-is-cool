# Getting started with Eleventy

This first section will focus on initial setup of an 11ty project and an introduction to some of its main features such as partials, custom data and collections.


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
You are free to name those differently if needed.

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

What about a [link to Eleventy's documentation](https://www.11ty.dev/docs/)?

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

Insert the html boilerplate into the `base.njk` file. You can copy-paste the following code or use Emmet's `!` shortcut to set it up.

📂 *`src/_includes/base.njk`*
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

If it's not already running, start a Browsersync web server displaying our output in a browser, using the `npm start` command. It will watch for changes within the project and automatically refresh the page. Take a look in the browser (usually at `http://localhost:8080/`) and see how beautiful is this! Well, hum, we can surely make it prettier with some custom styles.


## CSS

Let's create our first CSS stylesheet! I usually store them in a `css` subfolder within `src` but it's up to you. Just make sure to update the 11ty configuration accordingly.

📂 *`src/css/styles.css`*
```
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    font-family: sans-serif;
}
```

To start, I usually include some basic "reset" rules and a sans-serif font for the body. 

Now before going further we need to open `.eleventy.js` and, before the `return` statement, write these two lines :
```
eleventyConfig.addPassthroughCopy("./src/css");
eleventyConfig.addWatchTarget("./src/css/");
```
The first line asks 11ty to pass files from our css folder to the project build. The second will watch for their changes when a live server is running.

> 11ty is also capable of processing .scss with some [additional steps](https://11ty.rocks/posts/process-css-with-lightningcss/).

Just before moving on, I'll wrap my content in a `.container` div and add some padding and max-width to it. Also, since I'm using a blockquote in this example, I've added some styles to it aswell. Finally I set the body minimum height to the viewport height, and add Flexbox. This will be useful for a nice display of the footer in the next section.


📂 *`src/css/styles.css`*
```
body {
    font-family: sans-serif;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
}

.container {
    max-width: 70em;
    padding: 1em;
}

blockquote {
    padding: .3em .5em;
    background-color: #f1f5f9;
    max-width: max-content;
    border-left: 4px solid #0002;
}
```


## Partials

The layout feels quite empty, and I would like to start creating some partials, which you can look at as reusable pieces of template. Let's start with a header and a footer.

Under `_includes/partials` create `header.njk` and `footer.njk`, and set some styles for these within our main CSS stylesheet.

> I'm setting a `margin-top: auto;` on the footer so it can work with the flex property and min-height of the body element, and therefore push it to the bottom of our screen.

📂 *`src/partials/header.njk`*
```
<header>
    <div class="container">
        <p>I'm the header.</p>
    </div>
</header>
```

📂 *`src/partials/footer.njk`*
```
<footer>
    <div class="container">
        <p>I'm the footer.</p>
    </div>
</footer>
```

📂 *`src/css/styles.css`*
```
header {
    background-color: #e4e4e7;
    border-bottom: 1px solid #0002;
}

footer {
    background-color: #222;
    color: #fff;
    margin-top: auto;
}
```

Now we need to put these partials in our base template, using the `include` tag. At the moment our file should look like this :
```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="stylesheet" href="/css/styles.css" />
    <link rel="stylesheet" href="/css/header.css" />
    <link rel="stylesheet" href="/css/footer.css" />
    <title>{{ title }}</title>
  </head>
  <body>
    {% include "partials/header.njk" %}

    <div class="container">
      <h1>{{ title }}</h1>
      <main>{{ content | safe }}</main>
    </div>

    {% include "partials/footer.njk" %}
  </body>
</html>
```


## Custom data

Under `src` create a `_data` folder. It will contain globally available custom data, either in .json or .js files. We can try adding a list of links to our footer using a custom data file. Create `links.js` in `_data` and make it export an array of URLs and labels.

📂 *`src/_data/useful_links.js`*
```
module.exports = [
    {
        'label': "Eleventy",
        'url': 'https://www.11ty.dev/'
    },
    {
        'label': "Sanity",
        'url': 'https://www.sanity.io/'
    },
    {
        'label': "GitHub",
        'url': 'https://github.com/'
    },
    {
        'label': "Netlify",
        'url': 'https://www.netlify.com/'
    },
]
```

In the footer partial replace the placeholder text by an unordered list of links, using a `{% for %}` loop.

📂 *`src/_includes/partials/footer.njk`*
```
<footer>
  <div class="container">
    <ul>
      {% for link in useful_links %}
      <li><a href="{{ link.url }}">{{ link.label }}</a></li>
      {% endfor %}
    </ul>
  </div>
</footer>

```

Set some custom styles for the list and links.
> In this example I'm using [nested CSS rules](https://developer.chrome.com/articles/css-nesting/), since it's finally natively supported in all major browsers! 

📂 *`src/css/styles.css`*
```
footer {
    background-color: #222;
    color: #fff;
    margin-top: auto;

    & ul {
        list-style: none;
        display: flex;
        gap: 1em;

        & a {
            display: block;
            padding: .3em .5em;
            color: #fff;
            border: 1px solid #fff5;
            text-decoration: none;
            background-color: #1e293b;
            transition: all .2s;

            &:hover {
                border-color: #fff;
                background-color: #334155;
            }
        }
    }
}
```

Our `useful_links.js` file for now holds some static data, but it can also be used to fetch it from an external source, which 11ty would query each time it builds the output site. We'll see how this can work later, while hooking up 11ty to our Sanity CMS.


## Collections

Ok, let's say we want to write some articles, which would have their own paths on our site, and list them on our homepage. At this point I need some example content, and I'm going to ask ChatGPT to generate some bird-themed articles. I could also use Lorem Ipsum but it looks less interesting. Feel free to create the articles with your own content if you already have some.

> My example files are available here : **TODO: add link to ressources**

