# Practical: Add Labels

Currently in Energy Explorer we join the `layoutData` array to `<circle>` elements (see `js/update.js`). We would now like to add a label to the circle indicating the country name. In this practical we'll do this by joining `layoutData` to `<g>` elements instead of `<circle>` elements. Each `<g>` element will be populated with a `<circle>` and `<text>` element. This technique was covered in the More on D3 Joins chapter.

By the end of this practical, each country will be represented by a `<g>` element containing a `<circle>` and `<text>` element:

```html
<g class="country" transform="translate(471,264)">
  <circle r="24.26"></circle>
  <text class="label" y="50">Denmark</text>
</g>
```

## Overview

Open step7. The file structure is:

```text
step7
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

1. Add label information to the layout function (`js/layout.js`).
2. Modify the update function (`js/update.js`) so that it joins `layoutData` to `<g>` elements instead of `<circle>` elements.
3. Add a CSS file containing a rule to center the labels.

## Add label information to layout.js

Make the following changes to `js/layout.js`:

{caption: "js/layout.js"}
```js
markua-start-insert
function getTruncatedLabel(text) {
    return text.length <= 10 ? text : text.slice(0, 10) + '...';
}
markua-end-insert

function layout(data) {
markua-start-insert
    let labelHeight = 20;
markua-end-insert
    let cellWidth = config.width / config.numColumns;
markua-start-insert
    let cellHeight = cellWidth + labelHeight;
markua-end-insert

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

markua-start-insert
        item.labelText = getTruncatedLabel(d.name);
        item.labelOffset = maxRadius + labelHeight;
markua-end-insert

        return item;
    });

    return layoutData;
}
```

Two new properties `labelText` and `labelOffset` are added to each item. `labelText` represents the label text. Some of labels will overlap due to their length so we add a function `getTruncatedLabel` which truncates a label to a maximum of 10 characters. `labelOffset` is the vertical position of each label (with respect to the circle center) and is computed from `maxRadius` and `labelHeight`. We also increase `cellHeight` to accomodate the label.

Now `layoutData` has label information and looks like:

```js
[
  {
    "x": 42.857142857142854,
    "y": 52.857142857142854,
    "radius": 0,
markua-start-insert
    "labelText": "Angola",
    "labelOffset": 50
markua-end-insert
  },
  {
    "x": 128.57142857142856,
    "y": 52.857142857142854,
    "radius": 0,
markua-start-insert
    "labelText": "Albania",
    "labelOffset": 50
markua-end-insert
  },
  {
    "x": 214.28571428571428,
    "y": 52.857142857142854,
    "radius": 1.3416407864998736,
markua-start-insert
    "labelText": "United Ara...",
    "labelOffset": 50
markua-end-insert
  },
	...
]
```


## Modify update function to join data to `<g>` elements

The following changes are made to `js/update.js`:

{caption: "js/update.js"}
```js
markua-start-insert
function updateGroup(d, i) {
    let g = d3.select(this);

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
markua-end-insert

function update() {
    let layoutData = layout(data);

    d3.select('#chart')
markua-start-insert
        .selectAll('g')
markua-end-insert
        .data(layoutData)
markua-start-insert
        .join('g')
        .each(updateGroup);
markua-end-insert
}
```

The changes follow the same pattern as in the 'Joining an array to groups of elements' section of the More on D3 Joins chapter.

The join code in function `update` now joins `<g>` elements instead of `<circle>` elements. A new function `updateGroup` is called on each joined element.

`updateGroup` appends a `<circle>` and `<text>` element to the current `<g>` element if it's empty. The `<g>` element is given a class attribute of value `country` (which helps us later on when we style each country).

Instead of setting the center of each circle we now apply a translate transform to each `<g>` element. Next we set the circle radius. Finally we set the `y` attribute of the label and its text content using the two properties we added in the previous section.

Now save `js/layout.js` and `js/update.js` and load Step 7 in your browser. You should see:

![Each country is now represented by a `<circle>` and `<text>` element](700624f4266152a6bd0373a62df060fb.png)

## Center the labels using CSS

Finally we horizontally center the labels so that they're aligned with the circles.

Add a new directory to `step7` named `css` and add a new file `style.css`. Your directory structure should now look like:

```text
step7
markua-start-insert
├── css
│   └── style.css
markua-end-insert
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

Include the CSS file in `index.html` using a `<link>` element:

{caption: "index.html", line-numbers: false}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Energy mix by Country 2015</title>
markua-start-insert
    <link rel="stylesheet" href="css/style.css">
markua-end-insert
  </head>

  <body>
  ...
  </body>
</html>
```

Now add a rule to `style.css` to center the text labels:

{caption: "css/style.css", line-numbers: false}
```css
.country .label {
  text-anchor: middle;
}
```

The CSS classes `.country` and `.label` were added to each `<g>` and `<text>` element in `js/update.js`. The `text-anchor` property applies to SVG `<text>` elements and sets the horizontal alignment of the text.

Save `index.html` and `css/style.css` and refresh your browser. The labels should now be centered:

![Centered labels](05d0481a0c21af96882eb5bb246f26ad.png)

The completed code for this practical can be found in `step7-complete`.

## Summary

If you’ve got to this point you’ve done well! You’ve completed a major part of the Energy Explorer build. Converting the data join to a nested join was quite tricky but it creates a good foundation for the remaining practicals. In the next section you’ll add the remaining circles so that each country has four circles.