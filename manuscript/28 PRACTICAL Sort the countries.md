# Practical: Sort the countries

In this practical you’ll add code that **sorts the countries** according to the chosen indicator.

To recap, the indicator that determines the sort order is chosen using the menu at the top of the visualization:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-16.png)

When an indicator is clicked, the `action` function in `store.js` is called. This will set the `state.selectedIndicator` property to the indicator’s id (for example `'oilgascoal'`) and the whole visualization is redrawn.

In this practical you’ll add code that sorts the countries according to `state.selectedIndicator`. The sorting will be carried out **in the layout function** using Lodash’s `_.orderBy` method.

You’ll also add a feature whereby countries which have a **zero value (or no data) for the selected indicator will be hidden**. This isn’t an essential feature but I think it’s a nice one to have. It’s also an opportunity to add a simple filter to the energy explorer.

For example, when Nuclear is selected you’ll see:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-6-1024x215.png)

The steps for adding sorting to the Energy Explorer are:

* **include Lodash** in the application
* add a function that **sorts the data**
* **use the sort function** in the layout function
* **hide countries** which have a zero value (or no data) for the selected indicator

In your code editor open the `step12` directory of the `d3-start-to-finish` code.

### Include Lodash

The `js/lib` directory in `step12` already contains Lodash. (It was downloaded from [https://lodash.com/](https://lodash.com/).)

In `index.html` add `script` tags to load the Lodash:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
<head>
...
</head>
<body>
...
<script src="js/lib/d3.min.js"></script>
<script src="js/lib/popup-v1.1.1.min.js"></script>
<script src="js/lib/lodash.min.js"></script>
...
</body>
</html>
```

Now save `index.html`.

### How the sorting works

Recall that `data` looks something like:

```
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
etc.
]
```

The selected menu item will determine the property by which the array will be sorted. For example, if _Oil, Gas & Coal_ is selected we’ll sort using the property `oilgascoal`.

Recall that the selected menu item is stored in `state.selectedIndicator` and the possible values are:

* `'country'`
* `'renewable'`
* `'oilgascoal'`
* `'hydroelectric'`
* `'nuclear'`

If `state.selectedIndicator` is `'country'` we’ll sort using the `name` property:

```
sorted = _.orderBy(data, 'name');
```

Otherwise we’ll sort (in descending order) using the property described by `state.selectedIndicator`:

```
function sortAccessor(d) {
return d[state.selectedIndicator];
}
sorted = _.orderBy(data, sortAccessor, 'desc');
```

For example, suppose `state.selectedIndicator` is `'hydroelectric'`. This would result in the array being sorted by the property `hydroelectric`.

Note we’ve put the sort accessor in a separate function for clarity.

By default, when sorting in descending order `orderBy` will place items with a `null` value at the beginning of the sorted array.

To avoid this, we can set the return value of `sortAccessor` to 0 if the indicator value is `null`:

```
function sortAccessor(d) {
var value = d[state.selectedIndicator];
if(value === null) value = 0;
return value;
}
sorted = _.orderBy(data, sortAccessor, 'desc');
```

Now let’s add the sorting function to the energy explorer.

### Add a function to sort the data

Open `layout.js` and add the sort accessor function at the top of the file:

{caption: "layout.js", line-numbers: false}
```
function sortAccessor(d) {
var value = d[state.selectedIndicator];
if(value === null) value = 0;
return value;
}
function getTruncatedLabel(text) {
return text.length < 10 ? text : text.slice(0, 9) + '...';
}
...
```

Now add a new function named `getSortedData` which returns a sorted copy of `data`:

{caption: "layout.js", line-numbers: false}
```
function sortAccessor(d) {
var value = d[state.selectedIndicator];
if(value === null) value = 0;
return value;
}
function getSortedData(data) {
var sorted;
if(state.selectedIndicator === 'country') {
sorted = _.orderBy(data, 'name');
} else {
sorted = _.orderBy(data, sortAccessor, 'desc');
}
return sorted;
}
function getTruncatedLabel(text) {
return text.length < 10 ? text : text.slice(0, 9) + '...';
}
...
```

### Use sorted array in layout function

Now in `layout` (in `layout.js`) call `getSortedData` and assign its output to a new variable `sortedData`.

Then use `sortedData` instead of `data` when computing `layoutData`:

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
var labelHeight = 20;
var cellWidth = config.width / config.numColumns;
var cellHeight = cellWidth + labelHeight;
var maxRadius = 0.35 * cellWidth;
var radiusScale = d3.scaleSqrt()
.domain([0, 100])
.range([0, maxRadius]);
var sortedData = getSortedData(data);
var layoutData = sortedData.map(function(d, i) {
var item = {};
...
return item;
});
return layoutData;
}
```

Now save `layout.js` and refresh the browser (making sure it’s loading `step12`).

If you click on the energy indicators in the menu you should see the circles change position. For example if _Oil, Gas and Coal_ is selected you should see:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-7-1024x401.png)

(_Oil, Gas and Coal_ is represented by the solid gray outlined circles and you can see that the largest of these circles now appear first.)

### Hide countries with zero value or no data

If you select _Nuclear_ the countries with the highest mix of nuclear energy appear first, but **countries with a zero value or no data** **for nuclear energy also appear**. We’d like them to be hidden so that only countries with a greater than 0% nuclear mix are visible.

You’ll achieve this in two steps:

* **add a property** named `visible` to the layout which indicates whether the country is visible
* **hide the circle** in the chart update code if its `visible` property is `false`

In `layout.js` after the sort functions add a new function named `isVisible` which determines whether a country is visible or not:

{caption: "layout.js", line-numbers: false}
```
function getSortedData(data) { ... }
function isVisible(d) {
return state.selectedIndicator === 'country' || d[state.selectedIndicator] > 0;
}
function getTruncatedLabel(text) { ... }
function layout(data) { ... }
```

If _Country_ is selected in the menu **all** the countries will be visible. Otherwise a country is only visible if it has an indicator value above zero.

Now add a new property named `visible` to `item` in the `layout` function:

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
var labelHeight = 20;
var cellWidth = config.width / config.numColumns;
var cellHeight = cellWidth + labelHeight;
var maxRadius = 0.35 * cellWidth;
var radiusScale = d3.scaleSqrt()
.domain([0, 100])
.range([0, maxRadius]);
var sortedData = getSortedData(data);
var layoutData = sortedData.map(function(d, i) {
var item = {};
...
item.y = row * cellHeight + 0.5 * cellHeight;
item.visible = isVisible(d);
item.renewableRadius = radiusScale(d.energyMix.renewable);
...
return item;
});
return layoutData;
}
```

Now in the `updateGroup` function (`update.js`) add a couple of `.style` calls to set a country group’s `opacity` and `pointer-events` according to `d.visible`:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
var g = d3.select(this);
if(g.selectAll('*').empty()) initializeGroup(g);
g.attr('transform', 'translate(' + d.x + ',' + d.y + ')')
.style('opacity', d.visible ? 1 : 0)
.style('pointer-events', d.visible ? 'all' : 'none');
g.select('.popup-center')
.attr('cy', d.popupOffset);
...
}
```

You could’ve set the `display` CSS property to `inline` or `none` in order to show or hide the country group but we’re using `opacity` so that it can be animated in an upcoming practical.

The `pointer-events` property is also set so that the popup doesn’t appear on hidden groups. (Pointer events are still active if the opacity is zero.)

Now save `layout.js` and `update.js`. Refresh your browser and you should see that countries with a zero or missing indicator value are hidden.

For example click on _Nuclear_ and you should see:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-8-1024x215.png)

### Summing up

In this practical you added sort functionality to the Energy Explorer.

All the important sort logic was added to the layout module and you didn’t need to change any of the rendering code in `update.js`. This is a nice separation of concerns.

You also added some simple filtering so that if an energy indicator such as _Oil, Gas & Coal_ is selected only countries with a value above zero are visible.

You used the `opacity` property to show or hide country groups. This is so that when animations are added (which will be done in an upcoming practical) the countries will fade in or out.