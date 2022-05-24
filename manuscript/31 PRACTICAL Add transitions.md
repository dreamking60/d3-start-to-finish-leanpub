# Practical: Add transitions to the circles

This is a fun section. You’ll add transitions to the circles so that they:

* fade in when the explorer first loads
* animate into new locations when the sort order changes

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/energy-explorer-transitions.gif)

These are the steps:

* **add an `id` property** to each country (so that you can add a key function to the data join)
* **add a key function** to the data join (using the new `id` property)
* **add a transition** to the countries in `updateGroup` (so that their positions and opacity are animated)
* **initialize the opacity and transform** of each country
* **add a transition** to the each of the radius updates in `updateGroup`

In your code editor open the `step13` directory of the `d3-start-to-finish` code.

### Add id property in layout function

In the previous section we discussed the importance of adding a key function to the data join if:

* the **array is sorted**
* the elements are **positioned according to array index** and
* **transitions** are used

The Energy Explorer meets all 3 of these criteria so you’ll need to add a key function to the data join.

Open `layout.js` and add an `id` property to `item` in the `layout` function:

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
...
let layoutData = sortedData.map(function(d, i) {
let item = {};
item.id = d.id;
let column = i % config.numColumns;
...
return item;
});
return layoutData;
}
```

(Each country has a property named `id` so you can use that to set `item.id`. See the [Load the data practical](https://learn.createwithdata.com/books/d3-start-to-finish/sections/load-the-data/) for a reminder of the structure of `data`.)

Save `layout.js`.

### Add key function to data join

In `update.js` add a key function to the data join in `updateChart`:

{caption: "update.js", line-numbers: false}
```
function updateChart() {
let layoutData = layout();
d3.select('#chart')
.selectAll('g')
.data(layoutData, function(d) {
return d.id;
})
.join('g')
.each(updateGroup);
}
```

The key function returns the `id` property which you added in the previous step.

### Add transition to transform and opacity

In this section you’ll add a transition to the country groups (the `g` elements that contain the 4 circles). This will result in the countries animating into new positions when the sort order changes. The opacity will also animate if its value changes.

In `updateGroup` (`update.js`) add a call to `.transition()` before the `.attr` call:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
let g = d3.select(this);
if(g.selectAll('*').empty()) initializeGroup(g);
g.transition()
.attr('transform', 'translate(' + d.x + ',' + d.y + ')')
.style('opacity', d.visible ? 1 : 0)
.style('pointer-events', d.visible ? 'all' : 'none');
...
}
```

Save `update.js` and refresh the browser (make sure it’s loading `step13`).

Now when the energy explorer loads the circles should animate into view and when you choose a new indicator the circles should animate into new positions.

Also notice that the circles fade out if they have a zero value (or no data) for the chosen indicator because the transition also applies to the opacity.

The transition feels a bit too quick so let’s change the duration.

Add a new property named `transitionDuration` to the `config` object in `config.js`:

{caption: "config.js", line-numbers: false}
```
let config = {
width: 1200,
numColumns: 14,
transitionDuration: 500
};
```

Now add a call to `.duration` after the `.transition` call you added in `updateGroup`:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
let g = d3.select(this);
if(g.selectAll('*').empty()) initializeGroup(g);
g.transition()
.duration(config.transitionDuration)
.attr('transform', 'translate(' + d.x + ',' + d.y + ')')
.style('opacity', d.visible ? 1 : 0)
.style('pointer-events', d.visible ? 'all' : 'none');
...
}
```

Now if you save and refresh the transition should be a bit slower.

Let’s also add a staggered delay to the circles.

Add a new property named `transitionDelay` to `config`:

{caption: "config.js", line-numbers: false}
```
let config = {
width: 1200,
numColumns: 14,
transitionDuration: 500,
transitionDelay: 8
};
```

and add a call to `.delay` just after the call to `.duration` you added in the previous step:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
let g = d3.select(this);
if(g.selectAll('*').empty()) initializeGroup(g);
g.transition()
.duration(config.transitionDuration)
.delay(i * config.transitionDelay)
.attr('transform', 'translate(' + d.x + ',' + d.y + ')')
.style('opacity', d.visible ? 1 : 0)
.style('pointer-events', d.visible ? 'all' : 'none');
...
}
```

(You don’t need to use a function when setting the delay because `updateGroup` is called for each element and `i` is passed in as the second parameter.)

Save and refresh and you should now see a staggered transition.

### Initialize opacity and transform of the groups

Currently each circle group **flies in from the top left**.

We’d prefer each group just to fade in. This can be achieved by initializing the `opacity` and `transform` of each `g` element.

This can be done in the `initializeGroup` function (in `update.js`). This is where each `g` and `circle` element is created.

Currently `initializeGroup` looks like:

{caption: "update.js", line-numbers: false}
```
function initializeGroup(g) {
g.classed('country', true)
.on('mouseover', handleMouseover)
.on('mouseout', handleMouseout);
g.append('circle')
.classed('popup-center', true)
.attr('r', 1);
g.append('circle')
.classed('renewable', true);
...
}
```

The `g` element is initialized at the beginning of `initializeGroup`. This is where you have the opportunity to set the initial style and attributes of the `g` element.

(If you’d like a reminder of what `initializeGroup` does, have another look at the ‘[Add the other circles](https://learn.createwithdata.com/books/d3-start-to-finish/sections/practical-add-more-circles/)‘ practical.)

Start by setting the `opacity` to 0:

{caption: "update.js", line-numbers: false}
```
function initializeGroup(g) {
g.classed('country', true)
.style('opacity', 0)
.on('mouseover', handleMouseover)
.on('mouseout', handleMouseout);
g.append('circle')
.classed('popup-center', true)
.attr('r', 1);
g.append('circle')
.classed('renewable', true);
...
}
```

This causes each `g` element to have an `opacity` of zero when it is first created. When each group is updated in `updateGroup`, the `opacity` is set to 1, using a transition. The result is that the groups fade in.

Save `update.js` and refresh your browser and you should now see that the groups fade in.

Now set the initial `transform` of each group using:

{caption: "update.js", line-numbers: false}
```
function initializeGroup(g, d) {
g.classed('country', true)
.style('opacity', 0)
.attr('transform', 'translate(' + d.x + ',' + d.y + ')')
.on('mouseover', handleMouseover)
.on('mouseout', handleMouseout);
g.append('circle')
.classed('popup-center', true)
.attr('r', 1);
g.append('circle')
.classed('renewable', true);
...
}
function updateGroup(d, i) {
let g = d3.select(this);
if(g.selectAll('*').empty()) initializeGroup(g, d);
...
}
```

`d.x` and `d.y` are the x and y coordinates that were set in the `layout` function in `layout.js`.

Note that we also pass `d` into `initializeGroup` so that we can access the position (`x` and `y`) of each country.

Save `update.js` and refresh the browser. Now the circles should fade in, without flying in from the top left.

## Add a transition to the radius updates in updateGroup

Our final change is to make the circles grow from zero radius. This is a subtle change but one that adds a final bit of polish to the transtions.

The default radius of `circle` elements is zero so we don’t need to initialize the radii.

The circle `r` attributes are updated in `updateGroup` in `update.js`:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
...
g.select('.renewable')
.attr('r', d.renewableRadius);
g.select('.oilgascoal')
.attr('r', d.oilGasCoalRadius);
g.select('.hydroelectric')
.attr('r', d.hydroelectricRadius);
g.select('.nuclear')
.attr('r', d.nuclearRadius);
...
}
```

We want the radii to animate, so add a `.transition` call to each circle. Also add calls to `duration` and `delay` so that they have the same timing as the `transform` transition you added earlier:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
...
g.select('.renewable')
.transition()
.duration(config.transitionDuration)
.delay(i * config.transitionDelay)
.attr('r', d.renewableRadius);
g.select('.oilgascoal')
.transition()
.duration(config.transitionDuration)
.delay(i * config.transitionDelay)
.attr('r', d.oilGasCoalRadius);
g.select('.hydroelectric')
.transition()
.duration(config.transitionDuration)
.delay(i * config.transitionDelay)
.attr('r', d.hydroelectricRadius);
g.select('.nuclear')
.transition()
.duration(config.transitionDuration)
.delay(i * config.transitionDelay)
.attr('r', d.nuclearRadius);
...
}
```

Save `update.js` and refresh your browser. Now each circle should transition from zero radius to its final radius.

The completed code containing the transitions added in this practical can be found in `step13-complete`.

### Summing up

You’ve added transitions to Energy Explorer. Adding a basic transition to the group `transform` attribute was a relatively straightforward change but it made a huge difference to the visualisation. Country groups animate to new positions and fade in or out if their visibility changes.

You also fine tuned the transitions, modifying the duration and adding a staggered transition for a nice visual effect.

You also saw how the transitions when the visualisation first loads can be fine tuned. You added code to fade in each group and initialized each group’s transform so that they didn’t fly in from the top left.

If you’ve made it this far and are reasonably happy with what you’ve learned and built then you’ve done really well. Most of the Energy Explorer is finished now and all that’s left to add is a legend and some detailed styling.

You’ve earned yourself a break and once again, well done! Enjoy the transitions!