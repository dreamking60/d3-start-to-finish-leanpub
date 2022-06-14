# Architecture for Web-based Data Visualisation

This chapter introduces two techniques for architecting interactive data visualisations for code clarity and maintainability. These techniques become increasingly important as an application's size increases and they are of significant benefit to Energy Explorer.

The first technique is to use a layout function that takes an array of data and outputs a new array containing information (such as position and size) for rendering the visualisation. The benefit is that the logic for calculating the visual variables (such as position, size and colour) is separate to the rendering code. This makes maintaining and understanding the code easier and also makes porting the code from D3 to a different rendering engine (such as React) simpler.

The second technique is to modularise the code. This means that the code is split over several files, with each file responsible for a particular aspect of the application. This makes the application easier to understand and maintain.

## Layout functions

A layout function is a function that **takes an array of data** and **returns a new array** containing position, size and other visual variables for each item. For example, given the data:

```js
let myData = [30, 10, 20, 40];
```

a layout function could return an array containing position and radius data:

```js
[
  { x: 100, y: 100, r: 30 },
  { x: 200, y: 100, r: 10 },
  { x: 300, y: 100, r: 20 },
  { x: 400, y: 100, r: 40 }
]
```

Each object in the above array corresponds to an element in `myData`. The layout function that produces the above is:

```js
function layout(data) {
  let layoutData = data.map(function(d, i) {
    let item = {};
    item.x = 100 + i * 100;
    item.y = 100;
    item.r = d;
    return item;
  });

  return layoutData;
}
```

This function generates a new array `layoutData` by calling `map` on `data`. The map function iterates through `data` and computes `x`, `y` and `r` for each array element. The result is a new array containing the position and radius data.

D>See the JavaScript Iteration chapter of the [Fundamentals of HTML, SVG, CSS and JavaScript for Data Visualisation book](https://leanpub.com/html-svg-css-js-for-data-visualisation) if you’re not familiar with `map`.

Typically you assign the output of `layout(myData)` to a new variable:

```js
let layoutData = layout(myData);
```

and join `layoutData` to HTML or SVG elements:

```js
d3.select('#chart')
  .selectAll('circle')
  .data(layoutData)
  .join('circle')
  .attr('cx', function(d) { return d.x; })
  .attr('cy', function(d) { return d.y; })
  .attr('r', function(d) { return d.r; });
```

There are a few benefits of this approach:

* the join code is much **cleaner and easier to read** because it doesn’t contain any code for computing size, positions and other visual variables
* the **layout and rendering code is decoupled**, so swapping D3 for a different rendering library (such as React or Vue) will have lower impact
* **debugging is easier** because you can easily inspect the output of the layout function
* it’s relatively easy to run **automated tests** on the layout function

Here’s a similar example on CodePen: [https://codepen.io/createwithdata/pen/gOaezWJ](https://codepen.io/createwithdata/pen/gOaezWJ)

You don’t have to use layout functions but for more complex visualisations they can make your code easier to understand and maintain. D3 also provides a number of layout functions but it adds geometric information directly on each array element. The approach in this section creates a new array and doesn’t change (or mutate) the original array. This is more in keeping with modern JavaScript practice.

## Modules

A **module** is a chunk of code that has a **single purpose** and typically occupies a single file. It’s common practice to use modules when developing applications (especially for medium to large applications) as it makes your code easier to understand and maintain. Modules are often re-usable so they allow you to use other people’s code in your own application. (D3 itself is modular. It has modules for creating selections, for scale functions, for drawing axes and for many other purposes.)

### JavaScript modules

JavaScript modules have a long and complicated history. (To see for yourself, have a look at [Exploring JS’s section on modules](https://exploringjs.com/es6/ch_modules.html).) In this section we look at two approaches:

* script loading
* ES6 modules

### Script loading

This is a simple way to modularise your code. It’s straightforward and reliable but not suitable for large applications. (Once you have more than 5 or 6 modules you ought to look at the other approaches.) You can split your code up into separate files and load them individually using `<script>` tags in `index.html`. The load order is important: each file is **loaded** and **executed** in the same order as the `<script>` tags.

A simple example consists of two JavaScript files `add.js` and `main.js`. Here’s `add.js`:

{caption: "add.js", line-numbers: false}
```js
function add(a, b) {
  return a + b;
}
```

and here’s `main.js`:

{caption: "main.js", line-numbers: false}
```js
let n1 = 10, n2 = 20;
let sum = add(n1, n2);
console.log(sum);
```

They can be included in `index.html` using:

{caption: "index.html", line-numbers: false}
```html
<html>
...
<body>
...
  <script src="add.js"></script>
  <script src="main.js"></script>
</body>
</html>
```

When the page loads in the browser `add.js`  loads and executes. This results in the function `add` being defined. Next `main.js` loads and executes. This defines three variables `n1`, `n2` and `sum`. The latter is set using the `add` function. The `sum` variable is output to the browser’s debug console.

We use this approach in Energy Explorer as it’s simple, it’ll work in all browsers and doesn’t require any additional tooling.

### ES6 modules

JavaScript is an evolving language and one of the most significant releases was ES6 (also known as ECMAScript 2015) which has a built-in module system. Currently it’s fairly well supported in modern browsers but not in Microsoft’s Internet Explorer 11. Therefore to use ES6 modules you either drop support of older browsers or use tooling (such as [Webpack](https://webpack.js.org/)) to transform your code into an older JavaScript version named ES5 which is more widely supported. With ES6 modules you explicitly export and import JavaScript primitives (such as numbers, strings arrays, objects and functions).

For example you can export a function from a file `add.js` using:

{caption: "add.js", line-numbers: false}
```js
function add(a, b) {
  return a + b;
}

export { add };
```

You can then import the function using `import`:

```js
import { sum } from './add.js';

let n1 = 10, n2 = 20;
console.log(add(n1, n2));
```

Currently browser support isn’t complete so you’d typically use a bundler such as Webpack that convert your files to ES5. Module support is a rapidly evolving area and it can be hard to keep up. It looks like the end game is built-in support for modules in all browsers but we aren’t quite there yet.

The focus of this book is to learn D3 and in order to minimise distractions we use the simple script loading approach in Energy Explorer.