# Practical: Arrange the data

In this practical you’ll arrange the countries in a grid format:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-3.png)

The [previous section](https://learn.createwithdata.com/books/d3-start-to-finish/sections/arranging-items-in-a-grid/) explained the mathematics and code to arrange an array of data in a grid and you’ll use a similar approach in the Energy Explorer.

There’ll be two main steps:

* add a **configuration module** containing an object that defines the chart width and the number of columns
* modify the `layout` function to **arrange the circles in a grid**

In your code editor open the `step6` directory of the `d3-start-to-finish` code.

### Add configuration object

Create a new file within the `js` directory and name it `config.js`.

Your directory structure should now look like:

```
step5
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

Include the new file `config.js` in the application by adding a new `<script>` tag to `index.html`:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
...
<body>
...
<script src="js/lib/d3.min.js"></script>
<script src="js/config.js"></script>
<script src="js/layout.js"></script>
<script src="js/update.js"></script>
<script src="js/main.js"></script>
</body>
</html>
```

Now add an object named `config` to the new file which stores properties for the chart width and the number of columns:

{caption: "config.js", line-numbers: false}
```
var config = {
width: 1200,
numColumns: 14
};
```

### Modify layout function

Now open `layout.js` and at the top of the function `layout` add variables for the cell width and height:

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
var cellWidth = config.width / config.numColumns;
var cellHeight = cellWidth;
var radiusScale = d3.scaleSqrt()
.domain([0, 100])
.range([0, 20]);
...
}
```

In addition:

* create a new variable named `maxRadius` which defines the maximum circle size. Set it to 0.35 times `cellWidth` (this should give a nice amount of spacing between each circle)
* use `maxRadius` when defining the range of `radiusScale`

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
var cellWidth = config.width / config.numColumns;
var cellHeight = cellWidth;
var maxRadius = 0.35 * cellWidth;
var radiusScale = d3.scaleSqrt()
.domain([0, 100])
.range([0, maxRadius]);
...
}
```

Now it’s time to modify the layout so that the circles are arranged in a grid.

First add code to calculate the row and column number as explained in the previous section:

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
...
var layoutData = data.map(function(d, i) {
var item = {};
var column = i % config.numColumns;
var row = Math.floor(i / config.numColumns);
item.x = i * 10;
item.y = 100;
item.radius = radiusScale(d.renewable);
return item;
});
return layoutData;
}
```

Now modify the circle centers (`item.x` and `item.y`) so that the circles are positioned in a grid format (as covered in the previous section):

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
...

var layoutData = data.map(function(d, i) {
var item = {};
var column = i % config.numColumns;
var row = Math.floor(i / config.numColumns);
item.x = column * cellWidth + 0.5 * cellWidth;
item.y = row * cellHeight + 0.5 * cellHeight;
item.radius = radiusScale(d.renewable);
return item;
});
return layoutData;
}
```

Now save `main.js` and `layout.js` and refresh your browser (making sure it’s pointing at `step6`).

You should see:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-3.png)

(The complete code for this practical is in `step6-complete`.)

By modifying the layout function you’ve changed the way in which the circles are arranged. Notice that you didn’t have to change any of the data join code in `update.js`!

Try changing the value of `numColumns` in `config.js`. For example, set it to 30 and you should see:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-4.png)

Set it to 8 and you’ll see:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-5.png)

Notice that the circles adapt their size to the cell size. (This is enabled by the `maxRadius` variable in the `layout` function.)

Hopefully you can also see the benefit of the architecture we’ve chosen. You’re able to change a single number in a configuration object and the visualization layout adapts in a sensible manner. You were also able to change the arrangement of the circles without having to touch the data join code.

## The build so far

This section gives an overview of the Energy Explorer build so far.

You can follow along by opening `step6-complete` of the `d3-start-to-finish` code.

The directory structure of `step6-complete` is:

```
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

The `data` directory contains a single file `data.csv` which contains the data for the Energy Explorer. It’s a CSV file where each row represents a country:

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

You’ve created four JavaScript files: `main.js`, `update.js`, `layout.js` and `config.js`.

`main.js` loads `data.csv` using `d3.csv`. When the data loads D3 converts the CSV into an array of objects and calls `dataIsReady`. This assigns the array to the global variable `data`:

{caption: "main.js", line-numbers: false}
```
var data;
function dataIsReady(csv) {
data = csv;
update();
}
function parseNumber(d) {
return d === "" ? null : +d;
}
function transformRow(d) {
return {
name: d.name,
id: d.id,
hydroelectric: parseNumber(d.hydroelectric),
nuclear: parseNumber(d.nuclear),
oilgascoal: parseNumber(d.oilgascoal),
renewable: parseNumber(d.renewable)
};
}
d3.csv('data/data.csv', transformRow)
.then(dataIsReady);
```

`dataIsReady` also calls `update` (`update.js`) which is responsible for creating the circles.

`update` starts by calling the `layout` function and assigning the output to `layoutData`:

{caption: "update.js", line-numbers: false}
```
function update() {
var layoutData = layout(data);
...
}
```

The `layout` function (`layout.js`) iterates through `data` and returns an array of circle positions and radii.

The output of the layout function looks something like:

```
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

{caption: "update.js", line-numbers: false}
```
function update() {
var layoutData = layout(data);
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

The result is that a grid of circles representing each country (and sized according to the renewable indicator) is drawn on the screen.

Make sure you’re fairly comfortable with this overview before moving on. Perhaps look through `step6-complete` in your code editor and see if you can understand most of the code.