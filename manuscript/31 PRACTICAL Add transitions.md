# Practical: Add Transitions

In this chapter we add transitions to Energy Explorer so that:

* The countries fade in when the explorer first loads.
* The countries animate into new locations when the sort order changes.

## Overview

Open step13. The file structure is:

```text
step13
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

1. Add a key function to the data join.
2. Add a transition to the countries (so their position and opacity is animated).
3. Add a configurable transition duration and delay.
4. Initialise the country group positions.
5. Add a transition to the circle radii (so they expand when the page loads).

## Add a key function to the data join

In the previous chapter we discussed the importance of adding a key function to the data join if:

* the **array is sorted**
* the elements are **positioned according to array index** and
* **transitions** are used

Energy Explorer meets all 3 of these criteria so we need to add a key function to the data join.

In `js/layout.js` we add an `id` property to each `item`:

{caption: "js/layout.js", line-numbers: false}
```js
function sortAccessor(d) { ... }

function getSortedData(data) { ... }

function isVisible(d) { ... }

function getTruncatedLabel(text) { ... }

function layout(data) {
    ...

    let sortedData = getSortedData(data);

    let layoutData = sortedData.map(function(d, i) {
        let item = {};

markua-start-insert
        item.id = d.id;
markua-end-insert

        let column = i % config.numColumns;
        let row = Math.floor(i / config.numColumns);

        item.x = column * cellWidth + 0.5 * cellWidth;
        item.y = row * cellHeight + 0.5 * cellHeight;

        ...

        return item;
    });

    return layoutData;
}
```

(Each item in `data` and `sortedData` has a property named `id` so you can use that to set `item.id`. See the Load the Data practical for a reminder of the structure of `data`.)

Now in `updateChart` in `js/update.js` we add a key function to the data join:

{caption: "js/update.js", line-numbers: false}
```js
...

function updateChart() {
    let layoutData = layout(data);

    d3.select('#chart')
        .selectAll('g')
markua-start-insert
        .data(layoutData, function(d) {
            return d.id;
        })
markua-end-insert
        .join('g')
        .each(updateGroup);
}

function update() {
    updateChart();
    updateMenu();
}
```

The key function returns the `id` property which we added in the previous step. Adding this key function ensures that each array item remains associated with the same HTML/SVG element whenever the join occurs, even if the array is in a different order.

## Add transition to the country groups

We now add a transition to the country groups (the `<g>` elements that contain the 4 circles). This will result in the countries animating into new positions when the sort order changes. The opacity will also animate if its value changes, such as when the country's `visible` property changes.

In `updateGroup` (`update.js`) add a call to `.transition()` before the `.attr` call:

{caption: "js/update.js", line-numbers: false}
```js
...

function updateGroup(d, i) {
    let g = d3.select(this);

    if(g.selectAll('*').empty()) initialiseGroup(g, d);

markua-start-insert
    g.transition()
        .attr('transform', 'translate(' + d.x + ',' + d.y + ')')
markua-end-insert
        .style('opacity', d.visible ? 1 : 0)
        .style('pointer-events', d.visible ? 'all' : 'none');

    g.select('.popup-center')
        .attr('cy', d.popupOffset);

    g.select('.renewable')
        .transition()
        .duration(config.transitionDuration)
        .delay(i * config.transitionDelay)
        .attr('r', d.renewableRadius);

    ...
}

...
```

Save `js/layout.js` and `js/update.js` and load `step13` in the browser. Now when Energy Explorer loads the circles animate into view and when you choose a new indicator the circles animate into new positions. Also notice that the circles fade out if they have a zero value (or no data) for the chosen indicator.

The transition feels a bit too quick so we'll change the duration in the next section.

## Add transition duration and delay

We add two new properties to the `config` object:

{caption: "js/config.js", line-numbers: false}
```js
let config = {
    width: 1200,
    numColumns: 14,
markua-start-insert
    transitionDuration: 500,
    transitionDelay: 8
markua-end-insert
};
```

`transitionDuration` specifies transition duration and `transitionDelay` specifies the transition delay.

We now add calls to `.duration` and `.delay` after the `.transition` in `updateGroup`:

{caption: "js/update.js", line-numbers: false}
```js
...

function updateGroup(d, i) {
    let g = d3.select(this);

    if(g.selectAll('*').empty()) initialiseGroup(g, d);

    g.transition()
markua-start-insert
        .duration(config.transitionDuration)
        .delay(i * config.transitionDelay)
markua-end-insert
        .attr('transform', 'translate(' + d.x + ',' + d.y + ')')
        .style('opacity', d.visible ? 1 : 0)
        .style('pointer-events', d.visible ? 'all' : 'none');

    g.select('.popup-center')
        .attr('cy', d.popupOffset);

    g.select('.renewable')
        .transition()
        .duration(config.transitionDuration)
        .delay(i * config.transitionDelay)
        .attr('r', d.renewableRadius);

    ...
}

...
```

Now save `js/config.js` and `js/update.js` and refresh the browser. The transition should be a bit slower now. The transition is also staggered across the country groups, which is a pleasing effect.

## Initialise the country group positions

Each country group flies in from the top left. This is because the initial translation transform of each country group is `(0,0)`. Rather than flying in we'll fade in each group in its actual position. This is achieved by initialising the `opacity` and `transform` of each `g` element:

{caption: "js/update.js"}
```js
markua-start-insert
function initialiseGroup(g, d) {
markua-end-insert
    g.classed('country', true)
markua-start-insert
        .style('opacity', 0)
        .attr('transform', 'translate(' + d.x + ',' + d.y + ')')
markua-end-insert
        .on('mouseover', handleMouseover)
        .on('mouseout', handleMouseout);

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

function updateGroup(d, i) {
    let g = d3.select(this);

markua-start-insert
    if(g.selectAll('*').empty()) initialiseGroup(g, d);
markua-end-insert

    g.transition()
        .duration(config.transitionDuration)
        .delay(i * config.transitionDelay)
        .attr('transform', 'translate(' + d.x + ',' + d.y + ')')
        .style('opacity', d.visible ? 1 : 0)
        .style('pointer-events', d.visible ? 'all' : 'none');

    g.select('.popup-center')
        .attr('cy', d.popupOffset);

    ...
}

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

function update() {
    updateChart();
    updateMenu();
}
```

In `initialiseGroup` we set the `opacity` on each group to `0` and the transform to `'translate(' + d.x + ',' + d.y + ')'`. Remember that `initialiseGroup` is called when a new country group is created, so this is a good place to initialise the group's style and attributes.

In `updateGroup` we pass the joined data `d` into `initialiseGroup` so that it has access to the group's `x` and `y` properties.

Save `js/update.js` and refresh the browser. Now the circles fade in, without flying in from the top left.

## Add transition to the circle radii

Our final change is to make the circles grow from zero radius. This is a subtle change but one that adds a final bit of polish to the transtions. The default radius of `circle` elements is zero so we don’t need to initialize the radii. We make the following changes:

{caption: "js/update.js", line-numbers: false}
```js
function initialiseGroup(g, d) {
    ...
}

function updateGroup(d, i) {
    let g = d3.select(this);

    if(g.selectAll('*').empty()) initialiseGroup(g, d);

    g.transition()
        .duration(config.transitionDuration)
        .delay(i * config.transitionDelay)
        .attr('transform', 'translate(' + d.x + ',' + d.y + ')')
        .style('opacity', d.visible ? 1 : 0)
        .style('pointer-events', d.visible ? 'all' : 'none');

    g.select('.popup-center')
        .attr('cy', d.popupOffset);

    g.select('.renewable')
markua-start-insert
        .transition()
        .duration(config.transitionDuration)
        .delay(i * config.transitionDelay)
markua-end-insert
        .attr('r', d.renewableRadius);

    g.select('.oilgascoal')
markua-start-insert
        .transition()
        .duration(config.transitionDuration)
        .delay(i * config.transitionDelay)
markua-end-insert
        .attr('r', d.oilGasCoalRadius);

    g.select('.hydroelectric')
markua-start-insert
        .transition()
        .duration(config.transitionDuration)
        .delay(i * config.transitionDelay)
markua-end-insert
        .attr('r', d.hydroelectricRadius);

    g.select('.nuclear')
markua-start-insert
        .transition()
        .duration(config.transitionDuration)
        .delay(i * config.transitionDelay)
markua-end-insert
        .attr('r', d.nuclearRadius);

    g.select('.label')
        .attr('y', d.labelOffset)
        .text(d.labelText);
}

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

function update() {
    updateChart();
    updateMenu();
}
```

Save `js/update.js` and refresh the browser. Now each circle should transition from zero radius to its final radius.

The completed code containing the transitions added in this practical can be found in `step13-complete`.

## Summary

We added transitions to Energy Explorer. Adding a basic transition to the group `transform` attribute was a relatively straightforward change but it made a huge difference to the visualisation. Country groups animate to new positions and fade in or out if their visibility changes. We also fine tuned the transitions, modifying the duration and adding a staggered transition for a nice visual effect.

We also saw how the transitions can be fine tuned. We added code to fade in each group in its actual position (rather than flying in).

If you’ve made it this far and are reasonably happy with what you’ve learned you deserve a big well done! Most of Energy Explorer is complete and all that’s left is adding a legend and some detailed styling.