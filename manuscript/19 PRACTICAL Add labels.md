# Practical: Add labels

## Practical: Join data to multiple elements

In this practical you’ll join `data` to `g` elements. Each `g` element will contain a single `circle` element. This technique was covered in the [Joining an array to multiple elements section](https://learn.createwithdata.com/books/d3-start-to-finish/sections/joining-an-array-to-multiple-elements/).

Currently each country is represented by a single `circle` element. For example:

```
<circle cx="471" cy="264" r="24"></circle>
```

The aim of this practical is to represent each country with a `g` element that contains a `circle` element:

```
<g class="country" transform="translate(471,264)">
<circle r="24"></circle>
</g>
```

Instead of positioning the circle (using `cx` and `cy` attributes) we position the `g` element using the `transform` attribute.

In your code editor open the `step7` directory of the `d3-start-to-finish` code.

### Join layoutData to `g` elements

The existing join code is in the `update` function (`update.js)`:

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

Start by removing all the `.attr` calls and replacing `circle` with `g`:

{caption: "update.js", line-numbers: false}
```
function update() {
var layoutData = layout(data);
d3.select('#chart')
.selectAll('g')
.data(layoutData)
.join('g');
}
```

### Add `circle` element to `g` element

We want to add a `circle` element to each `g` element so add a call to `.each` and pass in a function named `updateGroup`:

{caption: "update.js", line-numbers: false}
```
function update() {
var layoutData = layout();
d3.select('#chart')
.selectAll('g')
.data(layoutData)
.join('g')
.each(updateGroup);
}
```

`updateGroup` will be similar to the function of the same name in the [Joining an array to multiple elements](https://learn.createwithdata.com/books/d3-start-to-finish/sections/joining-an-array-to-multiple-elements/) section. Add it above `update`:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
var g = d3.select(this);
if(g.selectAll('*').empty()) {
g.append('circle');
}
}
function update() {
var layoutData = layout();
d3.select('#chart')
.selectAll('g')
.data(layoutData)
.join('g')
.each(updateGroup);
}
```

Now add code to update:

* the class and transform of the `g` element
* the radius of the `circle`:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
var g = d3.select(this);
if(g.selectAll('*').empty()) {
g.append('circle');
}
g.classed('country', true)
.attr('transform', 'translate(' + d.x + ',' + d.y + ')');
g.select('circle')
.attr('r', d.radius);
}
...
```

The `g` element is translated using `d.x` and `d.y` which is the circle’s center position. This means that **everything in the group is positioned relative to the circle center**. This is why there’s no need to set the `cx` and `cy` attributes of the circle.

The entire `update.js` file should now look like:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
var g = d3.select(this);
if(g.selectAll('*').empty()) {
g.append('circle');
}
g.classed('country', true)
.attr('transform', 'translate(' + d.x + ',' + d.y + ')');
g.select('circle')
.attr('r', d.radius);
}
function update() {
var layoutData = layout(data);
d3.select('#chart')
.selectAll('g')
.data(layoutData)
.join('g')
.each(updateGroup);
}
```

Save `update.js` and refresh your browser (making sure it’s loading `step7`).

You should see the same output as before:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-3.png)

If you right click on a circle and select ‘Inspect element’ you should see that each country is now represented by a `g` element containing a `circle` element.

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/Peek-2021-04-13-16-07.gif)

## Practial: Add label

In this practical you’ll **add a label** to each country group:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-6.png)

Currently each country is represented by a `g` element containing a single `circle` element and looks something like:

```
<g class="country" transform="translate(471,264)">
<circle r="24"></circle>
</g>
```

The aim of this practical is to add a `text` element containing the country name:

```
<g class="country" transform="translate(471,264)">
<circle r="24"></circle>
<text class="label" y="50">Denmark</text>
</g>
```

The `text` element has a `y` attribute that positions the label below the circle.

The steps are:

* **add** a `text` element to each country group
* add **layout properties** for positioning and populating the label
* **update** the label position and content using the new layout properties
* **center** the labels
* **truncate** long labels

Open the `step7` directory of the `d3-start-to-finish` code. (This will already be open if you’ve just completed the previous practical.)

### Append text element

In `updateGroup` (update`.js`) append a `text` element to the `g` element and give it a class attribute with the name `label`:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
var g = d3.select(this);
if(g.selectAll('*').empty()) {
g.append('circle');
g.append('text')
.classed('label', true);
}
g.classed('country', true)
.attr('transform', 'translate(' + d.x + ',' + d.y + ')');
g.select('circle')
.attr('r', d.radius);
}
...
```

### Add label properties to layout

You’ll now add **two new properties** to the layout:

* the label text (e.g. `'Chile'`)
* the vertical position of the label

In the `layout` function in `layout.js` add a new property named `labelText` containing the country name (see the [loading data](https://learn.createwithdata.com/books/d3-start-to-finish/sections/load-the-data/) practical to remind yourself of the original data structure):

{caption: "layout.js", line-numbers: false}
```
function layout() {
...
var layoutData = data.map(function(d, i) {
var item = {};
...
item.radius = radiusScale(d.renewable);
item.labelText = d.name;

return item;
});
return layoutData;
}
```

You’ll now increase `cellHeight` to accomodate the label. At the start of the `layout` function:

* add a new variable named `labelHeight` that defines the amount of vertical space for the label
* increase `cellHeight` by `labelHeight`

{caption: "layout.js", line-numbers: false}
```
function layout() {
var labelHeight = 20;
var cellWidth = config.width / config.numColumns;
var cellHeight = cellWidth + labelHeight;
...
}
```

Now add a property named `labelOffset` to `item` for the label’s vertical position. This is relative to the circle center:

{caption: "layout.js", line-numbers: false}
```
function layout() {
...
var layoutData = data.map(function(d, i) {
var item = {};
...
item.labelText = d.name;
item.labelOffset = maxRadius + labelHeight;

return item;
});
return layoutData;
}
```

We use `maxRadius + labelHeight` so that the label is positioned below the circle. (Remember that a `text` element’s position refers to the bottom left of the text element.)

The entire `layout` function now looks like:

{caption: "layout.js", line-numbers: false}
```
function layout() {
var labelHeight = 20;
var cellWidth = config.width / config.numColumns;
var cellHeight = cellWidth + labelHeight;
var maxRadius = 0.35 * cellWidth;
var radiusScale = d3.scaleSqrt()
.domain([0, 100])
.range([0, maxRadius]);
var layoutData = data.map(function(d, i) {
var item = {};
var column = i % config.numColumns;
var row = Math.floor(i / config.numColumns);
item.x = column * cellWidth + 0.5 * cellWidth;
item.y = row * cellHeight + 0.5 * cellHeight;
item.radius = radiusScale(d.renewable);
item.labelText = d.name;
item.labelOffset = maxRadius + labelHeight;

return item;
});
return layoutData;
}
```

### Update label position and content

In `updateGroup` in `update.js` select the `text` element and set:

* its y coordinate to `d.labelOffset`
* its content to `d.labelText`

(these are the two properties you added in the previous section)

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
var g = d3.select(this);
if(g.selectAll('*').empty()) {
g.append('circle');
g.append('text')
.classed('label', true);
}
g.classed('country', true)
.attr('transform', 'translate(' + d.x + ',' + d.y + ')');
g.select('circle')
.attr('r', d.radius);
g.select('.label')
.attr('y', d.labelOffset)
.text(d.labelText);
}
```

Now if you save `main.js` and `layout.js` and refresh your browser you should see:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-7.png)

The labels are added and there’s just two more things to do:

* add a CSS rule so that the labels are center aligned
* truncate the labels so that they don’t overlap

### Center align the labels

Add a new directory to `step7` named `css` and add a new file called `style.css`. Your directory structure should now look like:

```
step7/
css/
style.css
data/
js/
index.html
```

Include the CSS file in `index.html`:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Energy mix by Country 2015</title>
<link rel="stylesheet" href="css/style.css">
</head>
<body>
...
</body>
</html>
```

Add a rule to `style.css` to center the text labels:

{caption: "style.css", line-numbers: false}
```
.country .label {
text-anchor: middle;
}
```

Save `style.css` and refresh the browser.

The labels should now be centered:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-8.png)

### Truncate long labels

Finally you’ll add a bit of code to truncate long labels so that the labels don’t overlap.

First add a function to the beginning of `layout.js` that takes a string and returns it if it’s less than 10 characters long otherwise truncates the string and adds some dots:

{caption: "layout.js", line-numbers: false}
```
function getTruncatedLabel(text) {
return text.length < 10 ? text : text.slice(0, 9) + '...';
}
```

> The above code uses JavaScript’s ternary operator. You can read more about it in the [JavaScript Operators](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/sections/javascript-operators/) section of my Fundamentals of HTML, SVG, CSS and JavaScript for Data Visualisation book.

Now use `getTruncatedLabel` when setting `labelText` in the `layout` function:

{caption: "layout.js", line-numbers: false}
```
function layout() {
...
item.radius = radiusScale(d.energyMix.renewable);
item.labelText = getTruncatedLabel(d.name);
item.labelOffset = maxRadius + labelHeight;
...
}
```

Save `layout.js` and refresh your browser.

You should now see that some of the longer names such as Bosnia and Herzegovina have been truncated:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-9.png)

The completed code for this practical can be found in `step7-complete`.

### Wrapping up

If you’ve got to this point you’ve done well!

You’ve completed a major part of the Energy Explorer build. Converting the data join to a nested join was quite a hard thing to do but hopefully it mostly made sense.

In the next section you’ll add the remaining circles so that each country has four circles.