# Practical: Sort the Countries

In this practical you’ll add code that sorts the countries according to `state.selectedIndicator`. The sorting will be carried out **in the layout function** using Lodash’s `_.orderBy` method. Remember that `state.selectedIndicator` is updated whenever a menu item is clicked.

{width: 75%}
![Energy Explorer menu](14b99904fd0c49ddbe35d100a23eede4.png)

We also add a feature whereby countries which have a zero value (or no data) for the selected indicator will be hidden. This isn’t an essential feature but I think it’s a nice one to have. It’s also an opportunity to add a simple filter to Energy Explorer. For example, when Nuclear is selected you’ll see:

![When Nuclear is selected, countries with no nuclear energy are filtered out](c28217c2aa67ca3db0355636f1e97163.png)

## Overview

Open `step12`. The file structure is:

```text
step12
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
    │   ├── lodash.min.js
markua-end-insert
    │   └── popup-v1.1.1.min.js
    ├── main.js
    ├── menu.js
    ├── popup.js
    ├── store.js
    └── update.js
```

Note that lodash has been added for you. (It was downloaded from [https://lodash.com/](https://lodash.com/).) In this practical we:

1. Link to lodash in `index.html`.
2. Add a function to sort the data.
3. Hide countries which have a zero value (or no data) for the selected indicator.

## Link to Lodash

In `index.html` add a `script` tag to load `js/lib/lodash.min.js`:

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
    ...

    <script src="js/lib/d3.min.js"></script>
    <script src="js/lib/popup-v1.1.1.min.js"></script>
markua-start-insert
    <script src="js/lib/lodash.min.js"></script>
markua-end-insert

    <script src="js/config.js"></script>
    <script src="js/store.js"></script>
    <script src="js/layout.js"></script>
    <script src="js/update.js"></script>
    <script src="js/popup.js"></script>
    <script src="js/menu.js"></script>
    <script src="js/main.js"></script>
  </body>
</html>
```

Now save `index.html`.

## Sort the data

Recall that `data` looks something like:

```js
[
  {
    "id": "ALB",
    "name": "Albania",
    "hydroelectric": 100,
    "nuclear": null,
    "oilgascoal": 0,
    "renewable": 0
  },
  {
    "id": "DZA",
    "name": "Algeria",
    "hydroelectric": 0.2,
    "nuclear": null,
    "oilgascoal": 99.7,
    "renewable": 0.1
  },
  ...
]
```

The selected menu item will determine the property by which the array will be sorted. For example, if 'Oil, Gas & Coal' is selected we sort using the property `oilgascoal`. A special case is if the first menu item 'Country' is selected we sort using the `name` property.

We make the following changes in `js/layout.js`:

{caption: "js/layout.js"}
```js
markua-start-insert
function sortAccessor(d) {
    let value = d[state.selectedIndicator];
    if(isNaN(value)) value = 0;
    return value;
}

function getSortedData(data) {
    let sorted;

    if(state.selectedIndicator === 'country') {
        sorted = _.orderBy(data, 'name');
    } else {
        sorted = _.orderBy(data, sortAccessor, 'desc');
    }

    return sorted;
}

function isVisible(d) {
    return state.selectedIndicator === 'country' || d[state.selectedIndicator] > 0;
}
markua-end-insert

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

markua-start-insert
    let sortedData = getSortedData(data);
markua-end-insert
markua-start-delete
    let layoutData = data.map(function(d, i) {
markua-end-delete
markua-start-insert
	  let layoutData = sortedData.map(function(d, i) {
markua-end-insert
        let item = {};

        let column = i % config.numColumns;
        let row = Math.floor(i / config.numColumns);

        item.x = column * cellWidth + 0.5 * cellWidth;
        item.y = row * cellHeight + 0.5 * cellHeight;

markua-start-insert
        item.visible = isVisible(d);
markua-end-insert

        item.renewableRadius = radiusScale(d.renewable);
        item.oilGasCoalRadius = radiusScale(d.oilgascoal);
        item.hydroelectricRadius = radiusScale(d.hydroelectric);
        item.nuclearRadius = radiusScale(d.nuclear);

        item.labelText = getTruncatedLabel(d.name);
        item.labelOffset = maxRadius + labelHeight;

        item.popupOffset = -0.8 * maxRadius;
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

Near the top of function `layout`, before iterating through the data, we call `getSortedData` and assign the output to `sortedData`.

`getSortedData` uses lodash's `orderBy` function to create a sorted copy of `data`. If `state.selectedIndicator` is `'country'` it sorts by the `name` property (so the countries are sorted alphabetically). Otherwise it sorts using the `sortAccessor` function which extracts the selected indicator value from a data item. (This was covered in the Data Manipulation chapter.)

For example `sortAccessor` might get called with `d` equal to:

```js
{
  "name": "Angola",
  "id": "AGO",
  "hydroelectric": 53.2,
  "nuclear": NaN,
  "oilgascoal": 46.8,
  "renewable": 0.0
}
```

If `state.selectedIndicator` is `'oilgascoal'`, we evaluate `d['oilgascoal']` which returns `46.8`. This value gets assigned to `value`. If `value` is `NaN` we return zero, which results in the items with no data appearing at the end of the sorted array.

To summarise, `getSortedData` returns a sorted copy of `data`. If 'Country' is selected in the menu, the data is sorted alphabetically. Otherwise it's sorted by the selected indicator, in descending order. Countries with missing data are placed at the end of the sorted array.

We also add a new property `visible` to the layout items. This indicates whether the country should be visible. If the country's selected indicator value is zero (or missing), we set `visible` to `false`, otherwise it's set to `true`.

## Hide countries with a zero or missing value

In `js/update.js` we update the country's opacity according to the `visible` property:

{caption: "js/update.js"}
```js
function initialiseGroup(g) {
    ...
}

function updateGroup(d, i) {
    let g = d3.select(this);

    if(g.selectAll('*').empty()) initialiseGroup(g);

markua-start-insert
    g.attr('transform', 'translate(' + d.x + ',' + d.y + ')')
        .style('opacity', d.visible ? 1 : 0)
        .style('pointer-events', d.visible ? 'all' : 'none');
markua-end-insert
	
    g.select('.popup-center')
        .attr('cy', d.popupOffset);

    g.select('.renewable')
        .attr('r', d.renewableRadius);

    ...
}

function updateChart() {
    let layoutData = layout(data);

    d3.select('#chart')
        .selectAll('g')
        .data(layoutData)
        .join('g')
        .each(updateGroup);
}

function update() {
    updateChart();
    updateMenu();
}
```

We set the opacity of the country group according to the new `visible` property. We could’ve set the `display` CSS property to `inline` or `none` in order to show or hide the country group but we use `opacity` so that it can be animated later on.

We also need to set the `pointer-events` property so that the popup doesn’t appear on hidden groups. (Pointer events are still active if the opacity is zero.)

Now save `layout.js` and `update.js`. Load `step12` in your browser and click on the menu items. The circles should sort according to the selected indicaator. You should also see that countries with a zero or missing indicator value are hidden.

For example click on 'Nuclear' and you'll see:

{width: 75%}
![Countries sorted by Nuclear energy](be052962082d88af9f313532f28f0f53.png)

## Summary

In this practical we added sort functionality to Energy Explorer. All the important sort logic was added to the layout module and we didn’t need to change any of the rendering code in `update.js`. This is a nice separation of concerns.

We also added filtering so that if an energy indicator such as 'Oil, Gas & Coal' is selected only countries with a value above zero are visible. We used the `opacity` property to show or hide country groups. This is so that when animations are added (which will be done in an upcoming practical) the countries will fade in or out.