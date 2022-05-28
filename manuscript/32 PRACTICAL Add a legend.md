# Practical: Add a Legend

In this practical we add a legend that indicates how the circle size relates to the percentage value. It’ll be located in the top right of the display and consists of a circle and label.

![Energy Explorer legend (top right)](d0537c27e242a3029044d5b646fba7c4.png)

The legend circle is sized such that it represents a value of 100%.

## Overview

Open `step14`. The file structure is:

```text
step14
├── css
│   └── style.css
├── data
│   └── data.csv
├── index.html
└── js
    ├── config.js
    ├── layout.js
    ├── lib
    │   ├── d3.min.js
    │   ├── lodash.min.js
    │   └── popup-v1.1.1.min.js
    ├── main.js
    ├── menu.js
    ├── popup.js
    ├── store.js
    └── update.js
```

In this practical we:

1. Add the legend to `index.html`.
2. Add a function to update the radius of legend circle.
3. Style the legend.

## Add the legend to index.html

In `index.html` we add the following HTML and SVG for the legend:

{caption: "index.html", line-numbers: false}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
  ...
  </head>

  <body>
    <div id="wrapper">
      <div id="controls">
        <div class="menu">
          <div class="items"></div>
        </div>
markua-start-insert
        <div class="legend">
          <svg width="70" height="70">
            <g transform="translate(35,35)">
              <circle />
              <text>100%</text>
            </g>
          </svg>
        </div>
markua-end-insert
      </div>
      <div id="chart-wrapper">
        <svg width="1200" height="1200">
          <g id="chart"></g>
        </svg>
      </div>
    </div>

    <script src="js/lib/d3.min.js"></script>
    <script src="js/lib/popup-v1.1.1.min.js"></script>
    <script src="js/lib/lodash.min.js"></script>

    <script src="js/config.js"></script>
    ...
  </body>
</html>
```

The legend is added to `div#controls` and consists of an SVG element. The SVG element contains a `<g>` element which is centred within the SVG element. The `<g>` element contains a `<circle>` element and a `<text>` element.

Save `index.html`.

## Add a function to update the radius of the legend circle

In `js/layout.js` we create a new function that returns the maximum radius of the country circles. This value is based on the width and number of columns of the chart:

{caption: "js/layout.js", line-numbers: false}
```js
function sortAccessor(d) { ... }

function getSortedData(data) { ... }

function isVisible(d) { ... }

function getTruncatedLabel(text) { ... }

markua-start-insert
function getMaxRadius() {
    let cellWidth = config.width / config.numColumns;
    let maxRadius = 0.35 * cellWidth;
    return maxRadius;
}
markua-end-insert

function layout(data) {
    let labelHeight = 20;
    let cellWidth = config.width / config.numColumns;
    let cellHeight = cellWidth + labelHeight;

markua-start-delete
    let maxRadius = 0.35 * cellWidth;
markua-end-delete
markua-start-insert
    let maxRadius = getMaxRadius();
markua-end-insert

    let radiusScale = d3.scaleSqrt()
        .domain([0, 100])
        .range([0, maxRadius]);

    let sortedData = getSortedData(data);

    let layoutData = sortedData.map(function(d, i) { ... });

    return layoutData;
}
```

`getMaxRadius` computes the maximum circle radius in Energy Explorer using the same calculation as before. We call `getMaxRadius` in function `layout` and we'll also use it when updating the legend circle.

In `update.js` we add a new function `updateLegend` which uses D3 to update the legend circle:

{caption: "js/update.js", line-numbers: false}
```js
...

function updateChart() {
    let layoutData = layout(data);

    d3.select('#chart')
        .selectAll('g')
        .data(layoutData, function(d) {
            return d.id;
        })
        .join('g')
        .each(updateGroup);
}

markua-start-insert
function updateLegend() {
    d3.select('.legend circle')
        .attr('r', getMaxRadius());
}
markua-end-insert

function update() {
    updateChart();
    updateMenu();
markua-start-insert
    updateLegend();
markua-end-insert
}
```

`updateLegend` selects the legend circle and sets its radius using `getMaxRadius`. This means that if we change the chart width or number of columns, the legend circle will still display the correct radius.

## Style the legend

We add some CSS rules for the legend in `css/style.css`:

{caption: "css/style.css"}
```css
body { ... }

#wrapper { ... }

markua-start-insert
#controls {
    display: flex;
    justify-content: space-between;
    align-items: center;
}
markua-end-insert

.menu .items {
    display: flex;
}

.menu .item {
    padding: 0 1rem;
    color: #333;
    opacity: 0.5;
}

.menu .item.selected, .menu .item:hover {
    opacity: 1;
}

markua-start-insert
.legend circle {
    stroke: #aaa;
    fill: none;
}

.legend text {
    fill: #777;
    text-anchor: middle;
    dominant-baseline: middle;
}
markua-end-insert

circle.renewable {
    fill: #7FB069;
}

...
```

The `div#controls` element contains the menu and legend. We'd like the menu to be on the left and the legend to be on the right and we use Flexbox to achieve this. (You can find out more about Flexbox in the [Fundamentals of HTML, SVG, CSS & JavaScript for Data Visualisation](fundamentalsbook) book.)

We set `display` to `flex` on the controls container. We also set `justify-content` to `space-between` to maximise the horizontal space between the menu and legend. Setting `align-items` to `center` aligns the menu and legend vertically.

On the legend itself we set the circle stroke to `#aaa` and `fill` to `none`. The legend's `<text>` element has a `fill` of `#777`. To center the label horizontally `text-anchor` is set to `middle` and to center the label vertically `dominant-baseline` is set to `middle`.

Now save `index.html`, `js/layout.js`, `js/update.js` and `css/style.css`. Load `step14` in the browser and the legend looks like:

![Energy Explorer legend (top right)](d0537c27e242a3029044d5b646fba7c4.png)

The completed code for this practical is in `step14-complete`.

## Summary

In this practical we added a legend to show the relationship between circle size and percentage value. We added code so that the legend radius is updated using the same logic as the circles in the visualisation. This ensures that the legend stays in step with any changes in visualisation width and number of columns.