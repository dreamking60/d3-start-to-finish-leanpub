# Practical: Add More Circles

In this practical we **add three more circles** so that each country is represented by four circles (one for each energy type).

{width: 100%}
![Each country represented by four circles](148995046a809d8710b2ae2ccbfb65a0.png)

Currently each country group looks like:

```html
<g class="country" transform="translate(471,264)">
  <circle r="24"></circle>
  <text class="label" y="50">Denmark</text>
</g>
```

At the end of this practical each country group will look like:

```html
<g class="country" transform="translate(471,264)">
  <circle class="renewable" r="24"></circle>
  <circle class="oilgascoal" r="17"></circle>
  <circle class="hydroelectric" r="1"></circle>
  <circle class="nuclear" r="0"></circle>
  <text class="label" y="50">Denmark</text>
</g>
```

There are three additional circles. Each circle also has a class attribute indicating which energy type it represents.

## Overview

Open `d3-start-to-finish-code/step8`. The file structure is:

```text
step8
├── css
│   └── style.css
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

In this practical we:

1. Add properties for each energy type in the layout function (`js/layout.js`).
2. Modify the update function (`js/update.js`) to append and update four circles.
3. Style the circles in `css/style.css`.

## Add properties for each energy type in the layout function

In `js/layout.js` add new properties for each of the energy type values:

{caption: "js/layout.js"}
```js
function getTruncatedLabel(text) {
    return text.length <= 10 ? text : text.slice(0, 10) + '...';
}

function layout(data) {
    let labelHeight = 20;
    let cellWidth = config.width / config.numColumns;
    let cellHeight = cellWidth + labelHeight;

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
markua-start-delete
        item.radius = radiusScale(d.renewable);
markua-end-delete
markua-start-insert
        item.renewableRadius = radiusScale(d.renewable);
        item.oilGasCoalRadius = radiusScale(d.oilgascoal);
        item.hydroelectricRadius = radiusScale(d.hydroelectric);
        item.nuclearRadius = radiusScale(d.nuclear);
markua-end-insert

        item.labelText = getTruncatedLabel(d.name);
        item.labelOffset = maxRadius + labelHeight;

        return item;
    });

    return layoutData;
}
```

As a reminder, each object in `data` looks similar to:

```js
{
  "name": "Angola",
  "id": "AGO",
  "hydroelectric": 53.2,
  "nuclear": null,
  "oilgascoal": 46.8,
  "renewable": 0
}
```

In the above changes the layout function takes each of the four energy indicators (hydroelectric, nuclear, oilgascoal and renewable), applies the radius scale and assigns the result to new properties `renewableRadius`, `oilGasCoalRadius`, `hydroelectricRadius` and `nuclearRadius`.

## Add four circles in the update function

Currently the update function (`js/update.js`) adds a single circle. Make the following changes which add circles for each of the four energy types:

{caption: "js/update.js"}
```js
markua-start-insert
function initialiseGroup(g) {
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
markua-end-insert

function updateGroup(d, i) {
    let g = d3.select(this);

markua-start-delete
    if(g.selectAll('*').empty()) {
        g.append('circle');

        g.append('text')
            .classed('label', true);
    }
markua-end-delete
markua-start-insert
    if(g.selectAll('*').empty()) initialiseGroup(g);
markua-end-insert

markua-start-delete
    g.classed('country', true)
        .attr('transform', 'translate(' + d.x + ',' + d.y + ')');
markua-end-delete
markua-start-insert
    g.attr('transform', 'translate(' + d.x + ',' + d.y + ')');
markua-end-insert

markua-start-delete
    g.select('circle')
        .attr('r', d.radius);
markua-end-delete
markua-start-insert
    g.select('.renewable')
        .attr('r', d.renewableRadius);

    g.select('.oilgascoal')
        .attr('r', d.oilGasCoalRadius);

    g.select('.hydroelectric')
        .attr('r', d.hydroelectricRadius);

    g.select('.nuclear')
        .attr('r', d.nuclearRadius);
markua-end-insert

    g.select('.label')
        .attr('y', d.labelOffset)
        .text(d.labelText);
}

function update() {
    let layoutData = layout(data);

    d3.select('#chart')
        .selectAll('g')
        .data(layoutData)
        .join('g')
        .each(updateGroup);
}
```

We add a new function `initialiseGroup` to take care of initialising each `<g>` element. This function sets the class attribute of each `<g>` element to `country`, adds a circle for each energy type and adds the `<text>` element for the label. Each of the circles also gets a class attribute that describes the energy type it refers to.

`updateGroup` now selects each of the four circles and updates its radius using the new properties we added in the previous section.

## Style the circles

By default SVG circles have a fill colour of black meaning we can't distinguish the circles, so let's set the fill to `none` and the stroke to `#aaa`:

{caption: "css/style.css"}
```css
.country .label {
    text-anchor: middle;
}

markua-start-insert
circle {
    fill: none;
    stroke: #aaa;
}
markua-end-insert
```


## Save and refresh

Save `css/style.css`, `js/layout.js` and `js/update.js` and load `step8` in your browser.

You should see:

{width: 100%}
![Energy Explorer with a circle for each energy type](18eaca30a5e3a178e078dba084d4cc66.png)

The completed code is in `step8-complete` of the code download.

## Summary

Adding the nested join in the previous section was fairly complicated it made adding further elements to the `g` element fairly straightforward. Each country now has 4 circles (each of which represents one of the four energy types) and a text label. It's beginning to take shape!

It’s not possible to see which circle relates to which energy type so in the next section you’ll style each circle in accordance with its energy type.