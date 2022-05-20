# Practical: Add modules

This practical modularises Energy Explorer by splitting it into separate JavaScript files. It also adds a layout function that'll be used by the update function. This is very much a refactoring exercise and there won't be any difference in the output. However it sets the groundwork for subsequent additions.

## Overview

Open step5. The file structure is:

```text
step5
├── data
│   └── data.csv
├── index.html
└── js
    ├── lib
    │   └── d3.min.js
    └── main.js
```

In this practical we:

1. Add two new empty modules: `layout.js` and `update.js`.
2. Add a layout function to `layout.js`.
3. Move the update code from `main.js` to `update.js`
4. Modify the update function to use the layout function

## Add new modules

Create two new files `layout.js` and `update.js` in the `js` directory. The file structure should now look like:

```text
step5-complete
├── data
│   └── data.csv
├── index.html
└── js
markua-start-insert
    ├── layout.js
markua-end-insert
    ├── lib
    │   └── d3.min.js
    ├── main.js
markua-start-insert
    └── update.js
markua-end-insert
```

Now link to these modules in `index.html`:

{caption: "index.html", line-numbers: false}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Energy mix by Country 2015</title>
  </head>

  <body>
    <svg width="1200" height="1200">
      <g id="chart"></g>
    </svg>

    <script src="js/lib/d3.min.js"></script>

markua-start-insert
    <script src="js/layout.js"></script>
    <script src="js/update.js"></script>
markua-end-insert
    <script src="js/main.js"></script>
  </body>
</html>
```

## Add a layout function

Add a layout function to `js/layout.js`:

{caption: "js/layout.js", line-numbers: false}
```js
function layout(data) {
    let radiusScale = d3.scaleSqrt()
        .domain([0, 100])
        .range([0, 20]);

    let layoutData = data.map(function(d, i) {
        let item = {};

        item.x = i * 10;
        item.y = 100;
        item.radius = radiusScale(d.renewable);

        return item;
    });

    return layoutData;
}
```

This takes a similar form to the layout functions in the previous chapter. The scale function `radiusScale` and the geometric calculations for `x`, `y` and `radius` are lifted from the update function (`js/main.js`).

The layout function accepts the energy data and outputs a new array where each object represents the position and radius of the country's circle and looks something like:

```js
{
  radius: 0.6324555320336758,
  x: 10,
  y: 100
}
```

The output of `layout` will be used in the new update function (see later).

## Move update code

Delete `radiusScale` from `js/main.js` (because it's now defined in `js/layout.js`).

Then cut the update function (highlighted in red) from `js/main.js` :

{caption: "js/main.js", line-numbers: false}
```js
let data;

markua-start-delete
let radiusScale = d3.scaleSqrt()
    .domain([0, 100])
    .range([0, 20]);
markua-end-delete

markua-start-insert
function update() {
    d3.select('#chart')
        .selectAll('circle')
        .data(data)
        .join('circle')
        .attr('cx', function(d, i) {
            return i * 10;
        })
        .attr('cy', 100)
        .attr('r', function(d) {
            return radiusScale(d.renewable);
        });
}
markua-end-insert

function dataIsReady(csv) {
    data = csv;
    update();
}

function transformRow(d) {
    return {
        name: d.name,
        id: d.id,
        hydroelectric: parseFloat(d.hydroelectric),
        nuclear: parseFloat(d.nuclear),
        oilgascoal: parseFloat(d.oilgascoal),
        renewable: parseFloat(d.renewable)
    };
}

d3.csv('data/data.csv', transformRow)
    .then(dataIsReady);
```

Paste the update function into `js/update.js`:

{caption: "js/update.js"}
```
function update() {
    d3.select('#chart')
        .selectAll('circle')
        .data(data)
        .join('circle')
        .attr('cx', function(d, i) {
            return i * 10;
        })
        .attr('cy', 100)
        .attr('r', function(d) {
            return radiusScale(d.renewable);
        });
}
```

Check that `js/main.js` looks like:

{caption: "js/main.js", line-numbers: false}
```js
let data;

function dataIsReady(csv) {
    data = csv;
    update();
}

function transformRow(d) {
    return {
        name: d.name,
        id: d.id,
        hydroelectric: parseFloat(d.hydroelectric),
        nuclear: parseFloat(d.nuclear),
        oilgascoal: parseFloat(d.oilgascoal),
        renewable: parseFloat(d.renewable)
    };
}

d3.csv('data/data.csv', transformRow)
    .then(dataIsReady);
```

## Use layout function

Modify the update function `update` (in `js/update.js`) so that it uses the layout function:

{caption: "js/update.js"}
```js
function update() {
markua-start-insert
    let layoutData = layout(data);
markua-end-insert

    d3.select('#chart')
        .selectAll('circle')
        .data(layoutData)
        .join('circle')
markua-start-insert
        .attr('cx', function(d) {
            return d.x;
        })
        .attr('cy', function(d) {
            return d.y;
        })
        .attr('r', function(d) {
            return d.radius;
        });
markua-end-insert
}
```


## Save and refresh

Save all the modified files: `index.html`, `js/main.js`, `js/update.js` and `js/layout.js`.

Refresh your browser (making sure it’s loading `step5`) and you should see the same circles as before:

![Output after modularising Energy Explorer (step 5)](cc586c1a36fc02369e4d9cba86a9c290.png)

If this hasn't worked for you, see if you can spot the problem. Perhaps you can add `console.log(layoutData)` after calling `layout(data)` in the update function. Check that it outputs an array of objects containing position information. Failing that, compare your code with `step5-complete`.
