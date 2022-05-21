# The build so far

This chapter gives an overview of the Energy Explorer build so far. The directory structure of `step6-complete` is:

```text
step6-complete
├── data
│   └── data.csv
├── index.html
└── js
    ├── config.js
    ├── layout.js
    ├── lib
    │   └── d3.min.js
    ├── main.js
    └── update.js
```

The `data` directory contains a single file `data.csv` which contains the data for Energy Explorer. It’s a CSV file where each row represents a country:

{caption: "data/data.csv", line-numbers: false}
```text
id,name,oilgascoal,nuclear,hydroelectric,renewable
AGO,Angola,46.8,,53.2,0.0
ALB,Albania,0.0,,100.0,0.0
ARE,United Arab Emirates,99.8,,0.0,0.2
ARG,Argentina,66.9,,26.2,1.9
ARM,Armenia,35.9,,28.3,0.1
...
```

Not including D3, there are four JavaScript files in the `js` directory: `main.js`, `update.js`, `layout.js` and `config.js`.

The first statement that executes is the call to `data.csv` in `main.js` which loads `data/data.csv`:

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

When the data loads D3 converts the CSV into an array of objects and calls `dataIsReady`. This assigns the array to the global variable `data`: `dataIsReady` also calls `update` which is responsible for creating the circles.

`update` (`js/update.js`) starts by calling the `layout` function and assigning the output to `layoutData`:

{caption: "js/update.js", line-numbers: false}
```js
function update() {
  let layoutData = layout(data);
  ...
}
```

The `layout` function (`js/layout.js`) iterates through `data` and returns an array of circle positions and radii:

{caption: "js/layout.js"}
```js
function layout(data) {
    let cellWidth = config.width / config.numColumns;
    let cellHeight = cellWidth;

    let maxRadius = 0.35 * cellWidth;

    let radiusScale = d3.scaleSqrt()
        .domain([0, 100])
        .range([0, maxRadius]);

    let layoutData = data.map(function(d, i) {
        let item = {};

        let column = i % config.numColumns;
        let row = Math.floor(i / config.numColumns);

        item.x = column * cellWidth + 0.5 * cellWidth;
        item.y = row * cellHeight + 0.5 * cellHeight;
        item.radius = radiusScale(d.renewable);

        return item;
    });

    return layoutData;
}
```

The output of `layout` looks like:

```js
[
  {
    radius: 0,
    x: 42.857142857142854,
    y: 42.857142857142854
  },
  {
    radius: 0.9486832980505137,
    x: 128.57142857142856,
    y: 42.857142857142854
  },
...
]
```

The first object in the array corresponds to Albania, the next to Algeria and so on.

In `update` the output of `layout` is assigned to `layoutData`. `update` then joins `layoutData` to a selection of circles and updates the circle attributes using `layoutData`‘s `x`, `y` and `radius` properties:

{caption: "js/update.js", line-numbers: false}
```js
function update() {
    let layoutData = layout(data);

    d3.select('#chart')
        .selectAll('circle')
        .data(layoutData)
        .join('circle')
        .attr('cx', function(d) {
            return d.x;
        })
        .attr('cy', function(d) {
            return d.y;
        })
        .attr('r', function(d) {
            return d.radius;
        });
}
```

This results in a grid of circles representing each country (and sized according to the renewable indicator) being drawn on the screen.

Make sure you’re fairly comfortable with this overview before moving on. Perhaps look through `step6-complete` in your code editor and see if you can understand most of the code.