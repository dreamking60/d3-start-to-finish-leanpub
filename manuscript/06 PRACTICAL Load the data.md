# Practical: Load the data

You learned about D3 requests in the previous section. In this practical we introduce the data that Energy Explorer uses and we show how to use D3 to load this data.

## Overview

Open `d3-start-to-finish/step2` in your text editor. The file structure is:

```
step2
├── data
│   └── data.csv
├── index.html
└── js
    ├── lib
    │   └── d3.min.js
    └── main.js
```

There are three new directories: `data`, `js` and `js/lib`. `data` is where the data is located and `js` is where JavaScript files live. `js/lib` contains libraries such as D3.

There are also three new files: `data.csv`, `d3.min.js` and `main.js`. The first is the CSV file containing data for Energy Explorer, `d3.min.js` is D3 and `main.js` is an empty file that'll contain the Energy Explorer JavaScript code.

In this practical we:

1. Take a look at the CSV file `data/data.csv`.
2. Link to `js/main.js` and `js/lib/d3.min.js` in `index.html`.
3. Load `data/data.csv` using D3.

## The data

Open `data/data.csv` which looks like:

{caption: "data.csv", line-numbers: false}
```text
id,name,oilgascoal,nuclear,hydroelectric,renewable
AGO,Angola,46.8,,53.2,0.0
ALB,Albania,0.0,,100.0,0.0
ARE,United Arab Emirates,99.8,,0.0,0.2
ARG,Argentina,66.9,,26.2,1.9
ARM,Armenia,35.9,,28.3,0.1
...
```

This is the data that’ll be visualised by Energy Explorer. Each row represents a country and has an id, a name and four indicators (`hydroelectric`, `nuclear`, `oilgascoal` and `renewable`). The indicators describe the energy mix (as a percentage) of a country. For example, 53.2% of Angola’s electricity is provided by hydroelectrics (from water, such as wave power or a dam).

The data originates from the [World Bank’s World Development Indicators](http://datatopics.worldbank.org/world-development-indicators/) database.


## Include JavaScript files in index.html

Now open `index.html`. We'll include the two JavaScript files `d3.min.js` and `main.js`:

{caption: "index.html", line-numbers: false}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Energy mix by Country 2015</title>
  </head>

  <body>
markua-start-insert
    <script src="js/lib/d3.min.js"></script>
    <script src="js/main.js"></script>
markua-end-insert
  </body>
</html>
```

We can include JavaScript files in a webpage using the `<script>` element. This uses an attribute `src` to specify the path of the JavaScript file.

`d3.min.js` is the minified version of D3 and is located in the `js/lib` directory. (Minified means that the size of the library has been reduced by, for example, shortening the names of variables.) We use version 7 of D3 in this book. `main.js` is where Energy Explorer’s code will live.

D>There's a number of different ways of including D3 in your project. We've opted for one of the simplest approaches which is to download the library from the [D3 homepage](https://d3js.org/) and link to it in `index.html`. For larger projects you can use 'bundlers' such as Webpack that can obtain library code for you and package it all together. Such tools add a layer of complexity and to minimise distraction I don't use them in this book.

## Data request

We now request `data/data.csv` using `d3.csv`. Open `js/main.js` and add the following:

{caption: "main.js", line-numbers: false}
```js
function dataIsReady(csv) {
  console.log(csv);
}

d3.csv('data/data.csv')
  .then(dataIsReady);
```

This code is very similar to the code in the D3 Requests chapter. `d3.csv` requests a CSV file at `data/data.csv`. (This URL is relative to `index.html` and it points at the `data.csv` file.) D3 converts the CSV file into an array of objects then calls `dataIsReady` which outputs the array to the console.

Now save `main.js` and refresh your browser.

> Make sure that your browser is showing `step2`. If you’re running a Node, Python, PHP or similar server, you might need to browse back to the directory list and open `step2`. If you’re using Brackets, select Live Preview from the File menu.

You’ll just see a blank page in your browser. However if you open the Console within the Developer Tools of the browser you should see something like:

{width: 50%}
![Developer Tools console showing the output of `dataIsReady`](39b4553046d04fbfe4b158c846ac04f5.png)

In Google Chrome you can open the Developer Tools by either:

* pressing F12
* pressing ctrl-shift-j (Windows) cmd-option-j (Mac)

(See [here](https://developers.google.com/web/tools/chrome-devtools/open) for further information on opening the console in Chrome’s developer tools. Similar instructions for Firefox are [here](https://developer.mozilla.org/en-US/docs/Tools/Web_Console/Opening_the_Web_Console).)

The Console allows you to inspect useful information while you develop your application. In this case you’re using `console.log` to output the `csv` variable. Within the console, expand the array and the first few elements. You should see something like:

{width: 50%}
![Inspecting the data array](b6a045dc22ee9aa25983ed73aaf868b6.png)

Can you see that this array corresponds to the `data.csv` file? (Each array object corresponds to a row of data.)

If you got this far congratulations – you’ve loaded the data into your application!

If not, check the following:

* are you definitely running a local webserver or the live server within Brackets?
* are you getting any errors on the developer tools console?
* try opening the Network tab of the developer tools and refreshing. You should see a list of files that the browser has loaded (see below). Make sure that `data.csv` is one of them
* try opening `step2-complete` which is the completed version of this section

![Inspecting the Network tab in Chrome's developer tools](af5718a5115a48b016f2f0e037350bc8.png)

This is one of the trickier steps to get working, so don’t worry if you’re having difficulty. Once this has been solved the remaining practicals shouldn’t present similar issues.

## Wrapping up

In this section we used D3’s `d3.csv` method to load the energy data. We added a function `dataIsReady` that’s called when the data arrives. `dataIsReady` outputs the data to the console.

In the next chapter you learn how D3 adds, removes and manipulates HTML and SVG elements.