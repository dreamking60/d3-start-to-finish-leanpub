# State Management

The state of an application describes what the application is currently doing. For example the state of a data visualisation might include things like:

* the **type of chart** (bar, line, pie etc.) that the user has chosen
* the **colour palette** the user has chosen
* **zoom level** and **pan position**
* **sort** and **filter** settings

Typically each time there’s a user interaction (e.g. clicking an item, choosing from a menu) the state changes. And each time the state changes, the display updates accordingly. 

There are several approaches to managing state in an application and we’ll look at an approach that is straightforward, effective and based on a very common pattern in web development known as [Flux](https://github.com/facebook/flux/tree/master/examples/flux-concepts). The basic idea is that the entire application state is stored in a JavaScript object. For example:

```js
let state = {
  selectedIndicator: 'GDP',
  filterType: 'region',
  filterValue: 'East Asia',
  colorScheme: 'category10'
}
```

This goes hand in hand with a function named `action` that’s responsible for updating the state object. It accepts two arguments:

* a string that describes the state change that should take place (such as `'setSelectedIndicator'` or `'setFilterType'` )
* any further information that is required for the state change. Typically this is a string or object

For example:

```action('setSelectedIndicator', 'CO2_emissions')```

sets `state.selectedIndicator` to `'CO2_emissions'`.

Typically `action` is called when there’s an event such as a button click. The `action` function consists of a `switch` statement containing `case` blocks for each action type:

```js
function action(type, param) {
  switch(type) {
    case 'setSelectedIndicator':
      state.selectedIndicator = param;
      break;
    case 'setFilterType':
      state.filterType = param;
      break;
  }

  update();
}
```

Each action has a block of code which updates the state accordingly. At the end of `action` the `update` function is called which triggers a redraw of the whole application.

Let’s look at a full example.

## State management example

Suppose you’ve an update function that joins an array of data to SVG circle elements:

```js
let data = [30, 10, 20, 40];

function update() {
  d3.select('#chart')
    .selectAll('circle')
    .data(data)
    .join('circle')
    .attr('r', function(d) {
      return d;
    })
    .attr('cx', function(d, i) {
      return 100 + i * 100;
    })
    .attr('cy', 100);
}

update();
```

Let’s add a feature whereby a circle is selected by clicking it. Only one circle may be selected at a time and we’ll set the colour of the selected circle to red.

We start by adding a state object containing a property `selectedCircle` which indicates the circle that's been selected:

```js
let data = [30, 10, 20, 40];

markua-start-insert
let state = {
  selectedCircle: null
};
markua-end-insert

function update() { ... }
```

`state.selectedCircle` is initialised to `null` to indicate that nothing has been selected. Now let’s add a function named `action` which handles a single action type named `setSelectedCircle`:

```js
let data = [30, 10, 20, 40];

let state = {
  selectedCircle: null
};

markua-start-insert
function action(type, param) {
  switch(type) {
    case 'setSelectedCircle':
      state.selectedCircle = param;
      break;
  }

  update();
}
markua-end-insert

function update() { ... }
```

When `action` is called with action type `'setSelectedCircle'` it’ll set `state.selectedCircle` to `param`. We’ll see in the next step that `param` represents the **index** of the selected circle.

Now let’s add a click event handler named `handleClick` and attach it to each circle. `handleClick` calls `action`, passing in `'setSelectedCircle'` as the action type and the circle index `i` as the action parameter:

```js
let data = [30, 10, 20, 40];

let state = {
  selectedCircle: null
};

function action(type, param) {
  switch(type) {
    case 'setSelectedCircle':
      state.selectedCircle = param;
      break;
  }

  update();
}

markua-start-insert
function handleClick(d, i) {
  action('setSelectedCircle', i);
}
markua-end-insert

function update() {
  d3.select('#chart')
  .selectAll('circle')
  .data(data)
  .join('circle')
  ...
  .attr('cy', 100)
markua-start-insert
  .on('click', handleClick);
markua-end-insert
}
```

Finally let’s set the fill of each circle according to whether its index is the same as `state.selectedCircle`:

```js
let data = [30, 10, 20, 40];

let state = {
  selectedCircle: null
};

function action(type, param) {
  switch(type) {
    case 'setSelectedCircle':
      state.selectedCircle = param;
      break;
  }

  update();
}

function handleClick(d, i) {
  action('setSelectedCircle', i);
}

function update() {
  d3.select('#chart')
  .selectAll('circle')
  .data(data)
  .join('circle')
  ...
  .attr('cy', 100)
markua-start-insert
  .style('fill', function(d, i) {
    return state.selectedCircle === i ? 'red' : null;
  })
markua-end-insert
  .on('click', handleClick);
}
```

Now when a circle is clicked:

* `handleClick` is called which calls `action` passing in `setSelectedCircle` as the action type and the circle index `i` as the second argument
* `action` updates `state.selectedCircle` with the circle index and calls `update`
* `update` updates the fill colour of each circle. If the circle index matches `state.selectedCircle` it’ll get coloured red

Navigate to [https://codepen.io/createwithdata/pen/YzwGaBw](https://codepen.io/createwithdata/pen/YzwGaBw) to view this example in CodePen.

As a further example, we can modify `action` so that if the clicked circle is already selected it’ll get deselected (by setting `state.selectedCircle` to `null`):

```js
...

function action(type, param) {
  switch(type) {
    case 'setSelectedCircle':
markua-start-insert
      if(state.selectedCircle === param) {
        state.selectedCircle = null;
      } else {
        state.selectedCircle = param;
      }
markua-end-insert
      break;
  }

  update();
}

...
```

As an exercise, modify the CodePen example with the modified `action` function. Now when you click a selected circle it’ll get deselected.

## Summary

We’ve introduced a state management pattern based on the Flux pattern. There are two main aspects:

* an object (named `state`) which contains application state
* a function (named `action`) which modifies `state` and triggers a redraw

This is a simple pattern but it’s surprisingly powerful. It’s by far the pattern I use the most when building interactive data visualisations.