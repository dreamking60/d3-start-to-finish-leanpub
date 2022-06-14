# Practical: Add a Popup

In this practical we **add a popup** to Energy Explorer using the Flourish popup component:

{width: 33%}
![Energy Explorer with Flourish popup](7253c5359b08f6d4fc7f52559fa5c94b.png)

When a country is hovered a popup will appear containing the country name together with the country’s energy mix.

## Overview

Open `d3-start-to-finish-code/step10`. The directory structure is:

```text
step10
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
markua-start-insert
    │   └── popup-v1.1.1.min.js
markua-end-insert
    ├── main.js
    └── update.js
```

The Flourish popup library has been added so you don't need to download it yourself. (It was downloaded from `https://cdn.flourish.rocks/popup-v1.full.min.js`.)

In this practical we:

1. Link to the popup library in `index.html`.
2. Add a new module `js/popup.js` to manage the popup.
3. Add event handlers to the country groups that show/hide the popup.
4. Populate the popup with the energy data.
5. Offset the popup (so that it's above the circle center).

## Link to the popup library

In `index.html` add `script` tags to load the popup library:

{caption: "index.html", line-numbers: false}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Energy mix by Country 2015</title>
    <link rel="stylesheet" href="css/style.css">
  </head>

  <body>
    <div id="wrapper">
      <svg width="1200" height="1200">
        <g id="chart"></g>
      </svg>
    </div>

    <script src="js/lib/d3.min.js"></script>
markua-start-insert
    <script src="js/lib/popup-v1.1.1.min.js"></script>
markua-end-insert

    <script src="js/config.js"></script>
	  <script src="js/layout.js"></script>
    <script src="js/update.js"></script>
    <script src="js/main.js"></script>
  </body>
</html>
```

Save `index.html`.

## Add new module for popup

Create a new file `popup.js` in the `js` directory that'll contain the popup related code:

```
step10
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
    │   └── popup-v1.1.1.min.js
    ├── main.js
markua-start-insert
    ├── popup.js
markua-end-insert
    └── update.js
```

Link to this new file in `index.html`:

{caption: "index.html"}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Energy mix by Country 2015</title>
    <link rel="stylesheet" href="css/style.css">
  </head>

  <body>
    <div id="wrapper">
      <svg width="1200" height="1200">
        <g id="chart"></g>
      </svg>
    </div>

    <script src="js/lib/d3.min.js"></script>
    <script src="js/lib/popup-v1.1.1.min.js"></script>

    <script src="js/config.js"></script>
    <script src="js/layout.js"></script>
    <script src="js/update.js"></script>
markua-start-insert
    <script src="js/popup.js"></script>
markua-end-insert
    <script src="js/main.js"></script>
  </body>
</html>
```

Save `index.html`.


## Add event handlers

In `js/popup.js` create a popup object and add mouseover and mouseout event handlers:

{caption: "js/popup.js"}
```js
let popup = Popup();

function handleMouseover(e, d) {
    popup
        .point(this)
        .html(d.labelText)
        .draw();
}

function handleMouseout() {
    popup.hide();
}
```

This code is similar to what we covered in the 'Flourish Popup Library' chapter.

In `handleMouseover`, `this` represents the `<g>` element that triggered the mouse event. We pass the `labelText` property into the popup, just so we can get something up and running quickly.

Now register the event handlers in the update function using D3's `.on` method:

{caption: "js/update.js"}
```js
function initialiseGroup(g) {
markua-start-insert
    g.classed('country', true)
        .on('mouseover', handleMouseover)
        .on('mouseout', handleMouseout);
markua-end-insert

    g.append('circle')
        .classed('popup-center', true)
        .attr('r', 1);

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

...
```

There's also a couple of CSS rules we need to add. By default, circles with a fill colour of `none` don't trigger events. We can change this by setting `pointer-events` to `all` on each country group. We also set `pointer-events` to `none` on the popup itself so that `mouseover` events aren't triggered if the mouse pointer moves over the popup itself. (These aren't very obvious things to add, and knowing to do these comes with experience.)

{caption: "css/style.css"}
```css
body {
    background-color: #FFFFF7;
}

...

circle.nuclear {
    fill: none;
    stroke: #F6AE2D;
    stroke-dasharray: 4,2;
}

markua-start-insert
.country {
    pointer-events: all;
}
markua-end-insert

.country .label {
    text-anchor: middle;
}

markua-start-insert
.flourish-popup {
    pointer-events: none;
}
markua-end-insert
```

Save `index.html`, `js/popup.js`, `js/update.js` and `css/style.css` and load `step10` in your browser. Now when you hover over a country you should see a popup displaying the country name.

{width: 25%}
![Popup showing country name](217f3f1916a6f8a788ad2018dfa8b96e.png)


## Populate popup with energy data

We now add the four energy indicators to the popup. Start by adding a new property `popupData` to each item in `js/layout.js`: 

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

        item.renewableRadius = radiusScale(d.renewable);
        item.oilGasCoalRadius = radiusScale(d.oilgascoal);
        item.hydroelectricRadius = radiusScale(d.hydroelectric);
        item.nuclearRadius = radiusScale(d.nuclear);

        item.labelText = getTruncatedLabel(d.name);
        item.labelOffset = maxRadius + labelHeight;

markua-start-insert
        item.popupData = {
            name: d.name,
            renewable: d.renewable,
            oilgascoal: d.oilgascoal,
            hydroelectric: d.hydroelectric,
            nuclear: d.nuclear
        };
markua-end-insert

        return item;
    });

    return layoutData;
}
```

The new property `.popupData` is an object that contains the information that'll be displayed in the popup. Now modify `js/popup.js` so that each energy indicator is displayed:

{caption: "js/popup.js"}
```js
let popup = Popup();

markua-start-insert
function getPopupEntry(d, type, label) {
    if (!isNaN(d.popupData[type])) {
        return '<div>' + label + ': ' + d.popupData[type] + '%</div>';
    }

    return '';
}

function popupTemplate(d) {
    let html = '';
    html += '<h3>' + d.popupData.name + '</h3>';

    html += getPopupEntry(d, 'renewable', 'Renewable');
    html += getPopupEntry(d, 'oilgascoal', 'Oil, Gas & Coal');
    html += getPopupEntry(d, 'hydroelectric', 'Hydroelectric');
    html += getPopupEntry(d, 'nuclear', 'Nuclear');

    return html;
}
markua-end-insert

function handleMouseover(e, d) {
    popup
        .point(this)
markua-start-insert
        .html(popupTemplate(d))
markua-end-insert
        .draw();
}

function handleMouseout() {
    popup.hide();
}
```

In `handleMouseover` we now pass `popupTemplate(d)` into the `.html` method. `popupTemplate` is a function that constructs a string containing HTML code that represents the popup content. We build up the string line by line starting with a heading containing the country name. (`.popupData` was added to the layout object items in the previous step.) A helper function `getPopupEntry` constructs a single line that displays an indicator name and associated value. If the value is NaN (which means it was missing from the CSV file) the indicator is omitted.

Save `js/layout.js` and `js/popup.js` and refresh your browser. You should now see the energy indicators when you hover over a country:

{width: 33%}
![Popup with all four energy indicators](0ee5f3e76b3d4430bb08d27e2adae4a9.png)


## Offset the popup

Currently the popup is positioned in the center of the `<g>` element (which contains the circles and the label). Most of the country's circles are obscured so we’d like the popup to appear **towards the top** of the circles. We’ll do this by adding a hidden element, placing it where we’d like the popup to appear. This element will get passed into the popup’s `.position` method. 

Start by adding a new property `popupOffset` to the layout items. This specifies where the popup pointer (the triangle underneath the popup) should appear in relation to the circle centers:

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

    ...

        item.labelText = getTruncatedLabel(d.name);
        item.labelOffset = maxRadius + labelHeight;

markua-start-insert
        item.popupOffset = -0.8 * maxRadius;
markua-end-insert
        item.popupData = {
            name: d.name,
            renewable: d.renewable,
            oilgascoal: d.oilgascoal,
            hydroelectric: d.hydroelectric,
            nuclear: d.nuclear
        };

        return item;
    });

    return layoutData;
}
```

We position the popup pointer `0.8 * maxRadius` above the circle centers. This value was arrived at by experimentation and strikes a balance between the popup being high enough to reveal the underlying circles but not so high it disconnects from smaller circles. Ideally we'd position the popup according to the largest circle, but we're keeping things simple and focused for the benefit of learning.

Now in `js/update.js` add a new circle to each group: 

{caption: "js/update.js"}
```js
function initialiseGroup(g) {
    g.classed('country', true)
        .on('mouseover', handleMouseover)
        .on('mouseout', handleMouseout);

markua-start-insert
    g.append('circle')
        .classed('popup-center', true)
        .attr('r', 1);
markua-end-insert

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

function updateGroup(d, i) {
    let g = d3.select(this);

    if(g.selectAll('*').empty()) initialiseGroup(g);

    g.attr('transform', 'translate(' + d.x + ',' + d.y + ')');

markua-start-insert
    g.select('.popup-center')
        .attr('cy', d.popupOffset);
markua-end-insert

    g.select('.renewable')
        .attr('r', d.renewableRadius);

    ...
}
```

The new circle is added in `initialiseGroup` and given a class attribute of `popup-center`. It doesn't really matter what its radius is, so long as it isn't zero. In `updateGroup` the circle's `cy` attribute is updated using the new layout item property `.popupOffset`.

Now use the new circle to position the popup:

{caption: "js/popup.js"}
```js
let popup = Popup();

function getPopupEntry(d, type, label) {
    if (!isNaN(d.popupData[type])) {
        return '<div>' + label + ': ' + d.popupData[type] + '%</div>';
    }

    return '';
}

function popupTemplate(d) {
    let html = '';
    html += '<h3>' + d.popupData.name + '</h3>';

    html += getPopupEntry(d, 'renewable', 'Renewable');
    html += getPopupEntry(d, 'oilgascoal', 'Oil, Gas & Coal');
    html += getPopupEntry(d, 'hydroelectric', 'Hydroelectric');
    html += getPopupEntry(d, 'nuclear', 'Nuclear');

    return html;
}

function handleMouseover(e, d) {
markua-start-insert
    let popupCenter = d3.select(this)
        .select('.popup-center')
        .node();
markua-end-insert

    popup
markua-start-insert
        .point(popupCenter)
markua-end-insert
        .html(popupTemplate(d))
        .draw();
}

function handleMouseout() {
    popup.hide();
}
```

In `handleMouseover` we select the new circle and call `.node()` to get the actual `<circle>` element (see the D3 Selections chapter for a reminder). We then pass the element into the popup's `.point` method.

Save `js/layout.js`, `js/update.js` and `js/popup.js` and refresh your browser. The popup should now appear a bit higher up:

{width: 33%}
![Popup positioned with the 'popupCenter' circle (which is visible)](52676cc44e50fc530a2071f3a46df13c.png)

We also need to hide the new circles, so add the following to `css/style.css`:

{caption: "css/style.css"}
```css
body {
    background-color: #FFFFF7;
}

...

.country {
    pointer-events: all;
}

.country .label {
    text-anchor: middle;
}

markua-start-insert
.popup-center {
    opacity: 0;
}
markua-end-insert

.flourish-popup {
    pointer-events: none;
}
```

Now the Energy Explorer with popup looks like:

{width: 33%}
![Energy Explorer with Flourish popup](7253c5359b08f6d4fc7f52559fa5c94b.png)


## Summary

In this practical we added an information popup to Energy Explorer. This allows users to get precise information for each country.

Although there was quite a lot of work to get the popup up and running, using an existing library has saved a lot of work. For instance, the popup library automatically positions the popup so that it doesn’t disappear off the edges of the screen.