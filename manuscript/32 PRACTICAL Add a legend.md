# Practical: Add a legend

In this practical you’ll add a legend that indicates how the **circle size relates to the percentage value**. It’ll be located in the top right of the display and consists of a circle and label.

![](http://learn.createwithdata.com/wp-content/uploads/2020/08/image-13-1024x175.png)

The legend circle is sized such that it represents 100%.

You’ll code the legend such that the circle radius is computed in the same manner as the circles in the visualisation. This means that if `config.width` or `config.numColumns` changes the country circles **and** the legend circle resize accordingly.

The steps are:

* **add the legend** to `index.html`
* add a function to **calculate the maximum circle radius**. (This’ll be used by the `layout` function and the function that updates the legend circle radius)
* add a function to **update the radius of the legend circle**
* **style** the legend

In your code editor open the `step14` directory of the `d3-start-to-finish` code.

### Add HTML and SVG for the legend

Open `index.html` and add the following HTML and SVG for the legend:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
...
<body>
<div id="wrapper">
<div id="controls">
<div class="menu">
<div class="items"></div>
</div>
<div class="legend">
<svg width="70" height="70">
<g transform="translate(35,35)">
<circle />
<text>100%</text>
</g>
</svg>
</div>
</div>
<div id="chart-wrapper">
...
</div>
</div>
...
</body>
</html>
```

The legend is added to `div#controls` and consists of an SVG element. The SVG element contains a `g` element which is centred within the SVG element. The `g` element contains a `circle` element and a `text` element.

Save `index.html`.

### Add a function to compute the maximum circle radius

Currently in the `layout` function (`layout.js`) we compute `maxRadius`:

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
let labelHeight = 20;
let cellWidth = config.width / config.numColumns;
let cellHeight = cellWidth + labelHeight;
let maxRadius = 0.35 * cellWidth;
let radiusScale = d3.scaleSqrt()
.domain([0, 100])
.range([0, maxRadius]);
...
return layoutData;
}
```

`maxRadius` depends on `cellWidth` which in turn depends on `config.width` and `config.numColumns`.

We’d like to use `maxRadius` to set the radius of the legend circle. There’s a few ways to achieve this and the approach we’ll take is to add a new function named `getMaxRadius` which computes and returns the maximum radius. This function will be used by `layout` **and** the function that’ll update the legend radius.

Just above the `layout` function add a new function `getMaxRadius`:

{caption: "layout.js", line-numbers: false}
```
function getTruncatedLabel(text) { ... }
function getMaxRadius() {
let cellWidth = config.width / config.numColumns;
let maxRadius = 0.35 * cellWidth;
return maxRadius;
}
function layout(data) { ... }
```

and in `layout` use the new function to set `maxRadius`:

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
let labelHeight = 20;
let cellWidth = config.width / config.numColumns;
let cellHeight = cellWidth + labelHeight;
let maxRadius = getMaxRadius();
let radiusScale = d3.scaleSqrt()
.domain([0, 100])
.range([0, maxRadius]);
...
}
```

Now save `layout.js`.

### Add a function to update the radius of the legend

In `update.js` add a new function `updateLegend` which uses D3 to select the legend circle.

Update the circle’s radius using the `getMaxRadius` function you added previously.

{caption: "update.js", line-numbers: false}
```
function updateChart() { ... }
function updateLegend() {
d3.select('.legend circle')
.attr('r', getMaxRadius());
}
function update() { ... }
```

Now add a call to `updateLegend` in `update`:

{caption: "update.js", line-numbers: false}
```
function updateLegend() { ... }
function update() {
updateChart();
updateMenu();
updateLegend();
}
```

Save `update.js`.

### Add CSS for the legend

In `style.css` add style for the legend’s `circle` and `text` element.

The `circle` will have a stroke of `#aaa` and a fill of `none`.

The `text` element will have a fill of `#777`. To center the label horizontally set `text-anchor` to `middle` and to center the label vertically set `dominant-baseline` to `middle`:

{caption: "style.css", line-numbers: false}
```
.menu .item.selected, .menu .item:hover {
opacity: 1;
}
.legend circle {
stroke: #aaa;
fill: none;
}
.legend text {
fill: #777;
text-anchor: middle;
dominant-baseline: middle;
}
circle.renewable {
fill: #7FB069;
}
```

Now save `style.css`.

Refresh your browser (making sure its loading `step14`). You should see:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-11-1024x185.png)

The legend itself looks fine, but it needs to be aligned to the right. You’ll add some CSS to achieve this in the next section.

### Add CSS to position the legend

The controls section consists of the menu and the legend:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
<head>
...
</head>
<body>
<div id="wrapper">
<div id="controls">
<div class="menu">
...
</div>
<div class="legend">
...
</div>
</div>
<div id="chart-wrapper">
...
</div>
</div>
...
</body>
</html>
```

We’d like the menu to appear on the **left of the wrapper** (`div#wrapper`) and the legend to appear on the **right of the wrapper**.

There’s various ways this can be done and we’ll use CSS’s flexbox layout because it’s fairly straightforward to achieve. If you’re not familiar with the flexbox layout I recommend CSS-Tricks’ [tutorial](https://css-tricks.com/snippets/css/a-guide-to-flexbox/). I’ve also built [Flexplorer](https://app.peterrcook.com/flexplorer/) which allows you to experiment with different layouts.

Let’s start by making the menu appear to the left and the legend to the right.

This can be done by setting the following rules on the controls container:

{caption: "style.css", line-numbers: false}
```
body { ... }
#wrapper { ... }
#controls {
display: flex;
justify-content: space-between;
}
.menu .items { ... }
```

Setting `display` to `flex` on an element indicates that we’d like that element to act as a flexbox container.

Setting `justify-content` to `space-between` indicates that we’d like the element’s content to occupy the entire width with any spare space being placed in between the content.

Save `style.css` and refresh the browser (making sure it’s loading `step14`) and you should see:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-12-1024x168.png)

To vertically align the menu and legend you can set flexbox’s `align-items` property to center:

{caption: "style.css", line-numbers: false}
```
body { ... }
#wrapper { ... }
#controls {
display: flex;
justify-content: space-between;
align-items: center;
}
.menu .items { ... }
```

Now when you save and refresh you should see:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-13-1024x175.png)

The menu and legend are now vertically aligned.

The completed code for this practical can be found in `step14-complete`.

## Summing up

In this practical you added a legend to show the relationship between circle size and percentage value.

You added code so that the legend radius is updated using the same logic as the actual circles in the visualisation. This ensures that the legend stays in step with any changes in visualisation width and number of columns.

You’re very nearly there. The functionality of the Energy Explorer is largely complete. There’s just some finishing touches to be done now.