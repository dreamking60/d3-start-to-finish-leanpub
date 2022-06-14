# Practical: Draw the Data

In this chapter we modify Energy Explorer to draw a **single circle for each country**.

The end result will look like:

![Circles joined to the energy data](9ea1b91da5d8a91c1909eaa715f61662.png)

It’s not the most exciting of data visualisations, but each circle represents a country. Later on we show how to set the radius of each circle according to a data variable.

## Overview

Open `d3-start-to-finish-code/step3` in the code download. The file structure is:

```text
step3
├── data
│   └── data.csv
├── index.html
└── js
    ├── lib
    │   └── d3.min.js
    └── main.js
```

Recall that in the previous practical we used D3's `.csv` function to load `data/data.csv`.

In this practical we:

1. Add a container for the circles in  `index.html`.
2. Join the energy mix data to `<circle>` elements.

## Add a container for the circles

In `index.html` add a container for the SVG `<circle>` elements snd set its width and height to 1200.

Now add a `<g>` element inside the `<svg>` element and give it an `id` attribute of value `chart`. This element will contain the circles:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Energy mix by Country 2015</title>
  </head>
  <body>
markua-start-insert
    <svg width="1200" height="1200">
      <g id="chart"></g>
    </svg>
markua-end-insert
    <script src="js/lib/d3.min.js"></script>
    <script src="js/main.js"></script>
  </body>
</html>
```

D>At this point we’re not sure what the size of the chart will be, but setting the width and height to 1200 pixels is a good starting point.

## Join the data array to circle elements

Make the following changes in `js/main.js`:

{caption: "js/main.js", line-numbers: false}
```js
markua-start-insert
let data;

function update() {
    d3.select('#chart')
        .selectAll('circle')
        .data(data)
        .join('circle')
        .attr('cx', function(d, i) {
            return i * 10;
        })
        .attr('cy', 100)
        .attr('r', 2);
}
markua-end-insert

function dataIsReady(csv) {
markua-start-delete
  console.log(csv);
markua-end-delete
markua-start-insert
    data = csv;
    update();
markua-end-insert
}

d3.csv('data/data.csv')
    .then(dataIsReady);
```

We've added a global variable `data` at the top of the file. In the `dataIsReady` function we assign the array of data (`csv`) to this new variable.

D>The `data` variable is a global variable because it sits outside of any function and is accessible everywhere in the code.

We also create a new function `update` that uses D3 to join `data` to `<circle>` elements. This new function is called from `dataIsReady`. The code inside `update` is similar to the code we covered in the previous sections on data joins. Let's go through it step by step.

First we select the container element using `d3.select('#chart')` and then call `.selectAll` to select the SVG elements we're joining.

We then specify the array that we’re joining using `.data(data)` method. (We're calling a method called `.data` and passing the global variable `data` into it.)

Next we call `.join('circle')` to perform the join. (This will cause a `<circle>` element to be created for each array element.)

Finally we call  `.attr` to update the `cx`, `cy` and `r` attributes of the circles. The `cx` attribute is set according to the array index `i`. This results in evenly spaced circles. The `cy` and `r` attributes are set to constants:

To summarise, when Energy Explorer loads, it'll load the CSV file using `d3.csv` and assign the resulting data array to the global variable `data`. It'll then call `update` which joins `data` to circles resulting in a row of circles appearing in the browser.

## Save and refresh

Save `index.html` and `main.js` and make sure your browser is loading `step3`.

D> Make sure that your browser is showing `step3`. If you’re running a Node, Python, PHP or similar server, you might need to browse back to the directory list and open `step3`. If you’re using Brackets, select Live Preview from the File menu.

You should now see the following in your browser:

![Circles joined to the energy data](9ea1b91da5d8a91c1909eaa715f61662.png)

Each circle represents a country. There should be 141 circles on the page but some of them will be invisible because they go beyond the dimensions of the SVG element.

The complete code for this section can be found in the `step3-complete` directory.

The next step in building Energy Explorer will be to size each circle according to one of the underlying data variables. But before you do this you’ll learn about **scale functions** in the next chapter.