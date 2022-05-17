# Practical: Add modules

## Practical: Add update module

In this practical you’ll create a new module that’ll contain the code for updating the visualisation. (Currently this is just the `update` function.)

The steps are:

* create a **new module** named `update.js`
* **move** the `update` function from `main.js` into `update.js`

In your code editor open the `step5` directory of the `d3-start-to-finish` code.

### Create a new module

Start by creating a new file within the `js` directory and name it `update.js`.

Your directory structure should now look like:

```
step5
├── data
│   └── data.csv
├── index.html
└── js
├── lib
│   └── d3.min.js
├── main.js
└── update.js
```

Include the new file `update.js` in the application by adding a new `<script>` tag to `index.html`:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
...
<body>
...
<script src="js/lib/d3.min.js"></script>
<script src="js/update.js"></script>
<script src="js/main.js"></script>
</body>
</html>
```

Now save `update.js` and `index.html`.

### Move update function

Now copy and delete the `update` function from `main.js`:

{caption: "main.js", line-numbers: false}
```
var data;
// Remove update function
function dataIsReady(csv) {
data = csv;
update();
}
```

and paste it into `update.js`:

{caption: "update.js", line-numbers: false}
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

Save both `main.js` and `update.js` and check that the Energy Explorer is working as before:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-2.png)

## Practical: Add layout module

In this practical you’ll add a [layout function](https://learn.createwithdata.com/books/d3-start-to-finish/sections/layout-functions/) that’ll calculate the position and size of the circles in the Energy Explorer.

This is an ‘under the hood’ change, so you won’t see any difference in how the circles are rendered:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-2.png)

However, this practical sets the groundwork for laying out the circles in a grid:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-3.png)

The layout function will be added as a new [module](https://learn.createwithdata.com/books/d3-start-to-finish/sections/modules/).

The steps are:

* create a **new module**
* add a new **layout function** to the new module
* compute `x`, `y` and `radius` **properties** in the new layout function
* **call** the new layout function in the update function

In your code editor, if you haven’t already, open the `step5` directory of the `d3-start-to-finish` code.

### Create a new module

Create a new file within the `js` directory and name it `layout.js`.

Your directory structure should now look like:

```
step5
├── data
│   └── data.csv
├── index.html
└── js
├── layout.js
├── lib
│   └── d3.min.js
├── main.js
└── update.js
```

Include the new file `layout.js` in the application by adding a new `<script>` tag to `index.html`:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
...
<body>
...
<script src="js/lib/d3.min.js"></script>
<script src="js/layout.js"></script>
<script src="js/update.js"></script>
<script src="js/main.js"></script>
</body>
</html>
```

Now save `layout.js` and `index.html`.

## Add layout function

You’ll now add a layout function to `layout.js` that computes an array containing the positions and radii of each country. The output of the function will look something like:

```
[
{
radius: 0,
​​​x: 0,
​​​y: 100
​​  },
{​
radius: 0.6324555320336758,
​​​x: 10,
​​​y: 100
​​  },
{​
radius: 0,
x: 20,
y: 100
}
]
```

The first object corresponds to Albania, the next to Algeria and so on.

Currently the positions and radii are computed within the `update` function in `update.js`:

{caption: "update.js", line-numbers: false}
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

Our aim is to move the code that computes `cx`, `cy` and `r` into the layout function.

Open `layout.js` and add the following layout function skeleton code:

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
var layoutData = data.map(function(d, i) {
var item = {};
// add properties such as x, y, length, width, radius to item
return item;
});
return layoutData;
}
```

Now cut `radiusScale` from `main.js` and paste it into `layout`:

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
var radiusScale = d3.scaleSqrt()
.domain([0, 100])
.range([0, 20]);
var layoutData = data.map(function(d, i) {
var item = {};
// add properties such as x, y, length, width, radius to item
return item;
});
return layoutData;
}
```

Now add properties for `x`, `y` and `radius` to the `item` object in the `layout` function. Each of the calculations is taken from the `update` function in `update.js`:

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
var radiusScale = d3.scaleSqrt()
.domain([0, 100])
.range([0, 20]);
var layoutData = data.map(function(d, i) {
var item = {};
item.x = i * 10;
item.y = 100;
item.radius = radiusScale(d.renewable);
return item;
});
return layoutData;
}
```

That’s the layout function complete so save `layout.js`. Now we need to modify `update` in `update.js` to use the new layout function.

### Use the layout function

In `update.js` call `layout` at the beginning of `update` and assign its output to a new variable named `layoutData`:

{caption: "update.js", line-numbers: false}
```
function update() {
var layoutData = layout(data);
d3.select('#chart')
...
}
```

Then use `layoutData` instead of `data` when creating the data join:

{caption: "update.js", line-numbers: false}
```
function update() {
...
.selectAll('circle')
.data(layoutData)
.join('circle')
...
} 
```

Finally modify the `.attr` methods so that the **layout properties** `x`, `y` and `radius` are used:

{caption: "update.js", line-numbers: false}
```
function update() {
...
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

The complete `update` function should now look like:

{caption: "update.js", line-numbers: false}
```
function update() {
var layoutData = layout();
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

You’ve separated the geometric calculations from the update code. Can you see that the update code is simpler now? This will be even more apparent as your visualisation increases in complexity!

To recap:

* you created a new `layout` function (within a new module `layout.js`) which computes the position and size of each circle
* you joined `layoutData` instead of `data` to the circles
* you updated the `.attr` methods so that the layout properties are used

Now when you save `update.js` and refresh your browser (making sure it’s loading `step5`) you should see the same circles as before:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-2.png)

It’s worth checking that the layout function is now driving the visualisation.

In `layout.js` change:

{caption: "layout.js", line-numbers: false}
```
item.x = i * 10;
```

to

{caption: "layout.js", line-numbers: false}
```
item.x = i * 30;
```

and verify that the circles are now spaced further apart.

The completed code for this section is in `step5-complete`.