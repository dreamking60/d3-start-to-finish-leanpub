# Practical: Load the data

You learned about D3 requests in the previous section. In this practical you’ll be introduced to the data that’ll be used in the Energy Explorer. You’ll also use D3 to load this data into the Energy Explorer.

## The data

In your code editor open the `step2` directory of the `d3-start-to-finish` code.

Two new directories have been added for you: `data` and `js`.

`data` is where the data (`data.csv`) is located and `js` is where the JavaScript files live:

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

Look for `data.csv` within the `data` directory and open it.

You should see:

{caption: "data.csv", line-numbers: false}
```
id,name,oilgascoal,nuclear,hydroelectric,renewable
AGO,Angola,46.8,,53.2,0.0
ALB,Albania,0.0,,100.0,0.0
ARE,United Arab Emirates,99.8,,0.0,0.2
ARG,Argentina,66.9,,26.2,1.9
ARM,Armenia,35.9,,28.3,0.1
...
```

This is the data that’ll be visualized by the Energy Explorer. It contains data on how much of a country’s electricity production is from hydroelectric, nuclear, fossil fuels or renewables.

It’s a comma separated value (CSV) file containing several rows of data. Each row represents a country. Each country has an id, a name and four indicators (`hydroelectric`, `nuclear`, `oilgascoal` and `renewable`).

The indicators describe the energy mix (as a percentage) of a country. For example, 53.2% of Angola’s electricity is provided by hydroelectrics (from water, such as wave power or a dam).

The data originates from the [World Bank’s World Development Indicators](http://datatopics.worldbank.org/world-development-indicators/) database.

## index.html

Now open `index.html`.

Two JavaScript files (`d3.min.js` and `main.js`) have been included:

{caption: "index.html", line-numbers: false}
```
  <body>
<script src="js/lib/d3.min.js"></script>
<script src="js/main.js"></script>
</body>
```

`d3.min.js` is the minified version of D3 and is located in the `js/lib` directory. (Minified means that the size of the library has been reduced by doing things like shortening the names of variables.) This version of D3 (5.15.0) was downloaded from [cdnjs.com](https://cdnjs.com/libraries/d3).

`main.js` is where the energy explorer’s code will live. At the moment it’s a blank file.

### Data request

You’ll now add some code to request `data.csv`.

Open `main.js` (in the `js` directory) and add the following:

{caption: "main.js", line-numbers: false}
```
function dataIsReady(csv) {
console.log(csv);
}
d3.csv('data/data.csv')
.then(dataIsReady);
```

> To save typing you can copy the code from the code snippets on this page and paste it into your code editor.

This code is very similar to what you learned in the previous section.

`d3.csv` requests a CSV file at `data/data.csv`. This URL is relative to `index.html` and it points at the `data.csv` file.

As explained in the previous section, when the CSV file arrives D3 converts it into an array of objects. It then calls the `dataIsReady` callback which outputs the array to the console.

Save `main.js` and refresh your browser.

> Make sure that your browser is showing `step2`. If you’re running a Node, Python, PHP or similar server, you might need to browse back to the directory list and open `step2`. If you’re using Brackets, select Live Preview from the File menu.

You’ll just see a blank page in your browser. However if you open the Console within the Developer Tools of the browser you should see something like:

![](https://learn.createwithdata.com/wp-content/uploads/2020/06/image-7.png)

In Google Chrome you can open the Developer Tools by either:

* pressing F12
* pressing ctrl-shift-j (Windows) cmd-option-j (Mac)

(See [here](https://developers.google.com/web/tools/chrome-devtools/open) for further information on opening the console in Chrome’s developer tools. Similar instructions for Firefox are [here](https://developer.mozilla.org/en-US/docs/Tools/Web_Console/Opening_the_Web_Console).)

The Console allows you to output useful information (out of the view of your users) while you develop your application. In this case you’re outputting the `json` variable.

Within the console, expand the array and the first few elements. You should see something like:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image.png)

Can you see that this array corresponds to the array within the `data.csv` file described at the start of this section?

Here’s a animation of me inspecting the console in Firefox:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/Peek-2021-04-13-15-40.gif)

Using the debug console in Firefox to inspect the contents of the data array

If you got this far congratulations – you’ve loaded the data into your application!

If not, check the following:

* are you definitely running a local webserver or the live server within Brackets?
* are you getting any errors on the developer tools console?
* try opening the Network tab of the developer tools and refreshing. You should see a list of files that the browser has loaded (see below). Make sure that `data.csv` is one of them
* try opening `step2-complete` which is the completed version of this section

![](https://learn.createwithdata.com/wp-content/uploads/2021/02/image-1.png)

Network tab of the developer tools in Chrome, showing that data.csv has loaded

If none of the above help and you still can’t see the data in the console, visit this book’s forum and you’ll be able to get help there. This is one of the trickier steps to get working, so don’t worry if you’re having difficulty. Once this has been solved the following sections shouldn’t present similar issues.

### Take a break

In this section you used D3’s `d3.csv` method to load the energy data. You added a function `dataIsReady` that’s called when the data arrives. `dataIsReady` outputs the data to the console and you verified you could open and browse the array within the Developer Tools’ console.

In the next section you’ll start learning how D3 is able to add, remove and manipulate HTML and SVG elements.