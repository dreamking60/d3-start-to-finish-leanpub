# Practical: Add the other circles

In this practical you’ll **add three more circles** so that each country has four circles. Each circle represents an energy type.

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-10-1024x363.png)

Currently each country group looks like:

```
<g class="country" transform="translate(471,264)">
<circle r="24"></circle>
<text class="label" y="50">Denmark</text>
</g>
```

At the end of this practical each country group will look like:

```
<g class="country" transform="translate(471,264)">
<circle class="renewable" r="24"></circle>
<circle class="oilgascoal" r="17"></circle>
<circle class="hydroelectric" r="1"></circle>
<circle class="nuclear" r="0"></circle>
<text class="label" y="50">Denmark</text>
</g>
```

There’s three additional circles. Each circle also has a class attribute indicating which energy type it represents.

The circles will be added in a similar manner to the text label in the previous practical. You will:

* create a **new function** `initializeGroup` for creating the `circle` and `text` elements
* **add** another 3 circle elements to each circle group
* add a **radius property** for each of the 4 energy types in the layout function
* **update** the radius of all 4 circles
* **style** the circles

In your code editor open the `step8` directory of the `d3-start-to-finish` code.

### Add initializeGroup function

You’ll start by creating a new function `initializeGroup` which will get called from `updateGroup`. This function will be responsible for initializing each `g` element. This means that it will add the `circle` and `text` elements to the `g` element.

Start by adding a new function `initializeGroup` to the top of `update.js`:

{caption: "update.js", line-numbers: false}
```
function initializeGroup(g) {
}
function updateGroup(d, i) {
var g = d3.select(this);
if(g.selectAll('*').empty()) {
g.append('circle');
g.append('text')
.classed('label', true);
}
...
}
```

Now move the `circle` and `text` append statements from `updateGroup` into `initializeGroup` and call `initializeGroup` if the `g` element has no members:

{caption: "update.js", line-numbers: false}
```
function initializeGroup(g) {
g.append('circle');
g.append('text')
.classed('label', true);
}
function updateGroup(d, i) {
var g = d3.select(this);
if(g.selectAll('*').empty()) initializeGroup(g);
g.classed('country', true)
.attr('transform', 'translate(' + d.x + ',' + d.y + ')');
...
}
```

In addition, move the `g.classed('country', true)` call into `initializeGroup` because this only needs to be called during initialisation:

{caption: "update.js", line-numbers: false}
```
function initializeGroup(g) {
g.classed('country', true);
g.append('circle');
g.append('text')
.classed('label', true);
}
function updateGroup(d, i) {
var g = d3.select(this);
if(g.selectAll('*').empty()) initializeGroup(g);
g.attr('transform', 'translate(' + d.x + ',' + d.y + ')');
...
}
```

Now we have two functions `initializeGroup` and `updateGroup`. `initializeGroup` is responsible for populating the `g` elements when they’re first created. `updateGroup` is responsible for updating a `g` element and its members.

### Append new circle elements

In `initializeGroup` (`update.js`) remove the append statement for the circle:

{caption: "update.js", line-numbers: false}
```
function initializeGroup(g) {
g.classed('country', true);
// Remove the circle append
g.append('text')
.classed('label', true);
}
```

Now append 4 circles and give them class attributes of `renewable`, `oilgascoal`, `hydroelectric` and `nuclear`:

{caption: "update.js", line-numbers: false}
```
function initializeGroup(g) {
g.classed('country', true);
g.append('circle')
.classed('renewable', true);
g.append('circle')
.classed('oilgascoal', true);
g.append('circle')
.classed('hydroelectric', true);
g.append('circle')
.classed('nuclear', true);
g.append('text')
.classed('label', true);
}
```

### Add a radius property for each circle to the layout

Currently the radius of the existing circle is computed in the `layout` function (`layout.js`) using:

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
item.labelText = getTruncatedLabel(d.name);
item.labelOffset = maxRadius + labelHeight;

return item;
});
return layoutData;
}
```

Start by changing the property name from `radius` to `renewableRadius`:

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
...
item.renewableRadius = radiusScale(d.renewable);
...
}
```

Now add radius properties for the `oilgascoal`, `hydroelectric` and `nuclear` indicators:

{caption: "layout.js", line-numbers: false}
```
function layout() {
...
item.radius = radiusScale(d.renewable);
item.oilGasCoalRadius = radiusScale(d.oilgascoal);
item.hydroelectricRadius = radiusScale(d.hydroelectric);
item.nuclearRadius = radiusScale(d.nuclear);
...
}
```

(You can recap the data structure in the [data loading section](http://learn.createwithdata.com/books/d3-start-to-finish/sections/load-the-data/).)

### Update the circles

Now in `updateGroup` remove the code that updates the circle:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
var g = d3.select(this);
if(g.selectAll('*').empty()) initializeGroup(g);
g.attr('transform', 'translate(' + d.x + ',' + d.y + ')');
// Remove the circle radius update
g.select('.label')
.attr('y', d.labelOffset)
.text(d.labelText);
}
```

Add code to update the radius of all four circles using the new radius properties in the layout:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
var g = d3.select(this);
if(g.selectAll('*').empty()) initializeGroup(g);
g.attr('transform', 'translate(' + d.x + ',' + d.y + ')');
g.select('.renewable')
.attr('r', d.renewableRadius);
g.select('.oilgascoal')
.attr('r', d.oilGasCoalRadius);
g.select('.hydroelectric')
.attr('r', d.hydroelectricRadius);
g.select('.nuclear')
.attr('r', d.nuclearRadius);
g.select('.label')
.attr('y', d.labelOffset)
.text(d.labelText);
}
```

Now save `update.js` and `layout.js`.

### Style the circles

Now add a CSS rule so that the circle has a stroke (the outline of the circle) but no fill:

{caption: "style.css", line-numbers: false}
```
.country .label {
text-anchor: middle;
}
circle {
fill: none;
stroke: #aaa;
}
```

Save `style.css` and refresh the browser (making sure it’s loading `step8`).

You should see:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-11-1024x363.png)

The completed code is in `step8-complete` of the code download.

### Summing up

Adding the nested join in the previous section was fairly complicated, but once it’s done adding further elements to the `g` element is fairly straightforward.

**Each country now has 4 circles** (each of which represents one of the four energy types) and a **text label**.

It’s not possible to see which circle relates to which energy type so in the next section you’ll **style each circle** in accordance with its energy type.