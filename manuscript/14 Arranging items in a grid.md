# Arranging items in a grid

Arranging a list of items in a grid is a fairly common exercise but it isn’t always obvious how to do this so this chapter covers the underlying mathematics and code.

Given an array of numbers such as:

```js
[10, 60, 40, 30, 70, 40, 30, 30, 10, 60, 50, 10]
```

we’d like to represent the array using a grid of circles:

{width: 66%}
![Circles arranged in a grid (with 4 columns)](4a336aec62a1d1661056d9b0888e201e.png)

The number within each circle indicates the array index. The first circle is top left and represents the first element of the array. The grid has 4 columns and we’d like to calculate the centers of each circle relative to the origin (where the x and y axes meet). Let’s start by figuring out the row number and column number of each cell. The first row and first column are numbered zero:

{width: 66%}
![Grid of circles with row and column numbers](b2f7bd870b8ead0fe8a17d81bd2e0a69.png)

We can calculate the column number using JavaScript’s modulo operator `%`. This returns the **remainder** of a division.

For example, for the 9th circle, `9 % 4` returns `1` because the remainder of 9 ÷ 4 is 1. Thus the 9th circle is in column 1. (The number after the `%` is the **divisor**.)

If we evaluate the modulo operator with divisor 4 on numbers up to 10 we get:

{width: 50%}
| Modulo operation | Result |
| --- | --- |
| 0 % 4 | 0   |
| 1 % 4 | 1   |
| 2 % 4 | 2   |
| 3 % 4 | 3   |
| 4 % 4 | 0   |
| 5 % 4 | 1   |
| 6 % 4 | 2   |
| 7 % 4 | 3   |
| 8 % 4 | 0   |
| 9 % 4 | 1   |
| 10 % 4 | 2   |

The result of the modulo operation wraps around every `n` times (where `n` is the divisor). Thus we can use `%` to compute the column number from the array index `i`:

```js
let column = i % numColumns;
```

where `numColumns` is the number of columns.

We can calculate the row number using division together with JavaScipt’s `Math.floor` function. `Math.floor` returns the nearest whole number below a given number. For example:

```js
Math.floor(7.65); // returns 7
```

If we divide (using a divisor of 4) each number up to 10 and apply `Math.floor` we get:

{width: 66%}
| Operation | Result of i ÷ n | Result of Math.floor(i ÷ n) |
| --- | --- | --- |
| 0 ÷ 4 | 0   | 0   |
| 1 ÷ 4 | 0.25 | 0   |
| 2 ÷ 4 | 0.5 | 0   |
| 3 ÷ 4 | 0.75 | 0   |
| 4 ÷ 4 | 1   | 1   |
| 5 ÷ 4 | 1.25 | 1   |
| 6 ÷ 4 | 1.5 | 1   |
| 7 ÷ 4 | 1.75 | 1   |
| 8 ÷ 4 | 2   | 2   |
| 9 ÷ 4 | 2.25 | 2   |
| 10 ÷ 4 | 2.5 | 2   |

The right hand column starts at 0 and increments every 4 rows. In general, the result of `Math.floor(i / n)` increments every `n` times. (The JavaScript symbol for division is `/`.) Thus we can use the following to calculate the row number from the array index `i`:

```js
let row = Math.floor(i / numColumns);
```

Now we’ve figured out how to compute the row and column numbers let’s compute the coordinates of the top left corner of each square cell.

The width (`cellWidth`) of each square cell is `width ÷ numColumns` where `width` is the overall width and `numColumns` is the number of columns:

```js
let width = 1000;
let numColumns = 4;
let cellWidth = width / numColumns;
```

We can compute the x coordinate of the top left of a cell by multiplying the cell’s column number `column` by `cellWidth`:

```js
let x = column * cellWidth;
```

Each circle is located in the center of its square cell, so we can compute the the x coordinate of a circle’s center by adding `0.5 * cellWidth` to `x`:

```js
let x = column * cellWidth + 0.5 * cellWidth;
```

The `y` coordinate is computed in a similar manner:

```js
let cellHeight = cellWidth;  // the height is the same as the width
let row = Math.floor(i / numColumns);
let y = row * cellHeight + 0.5 * cellHeight;
```

Therefore the complete code for computing the circle centers is:

```js
let data = [10, 60, 40, 30, 70, 40, 30, 30, 10, 60, 50, 10];
let width = 1000;
let numColumns = 4;

// Compute cell width and height
let cellWidth = width / numColumns;
let cellHeight = cellWidth;

// Iterate through array
data.forEach(function(d, i) {
	// Compute column and row number
  let column = i % numColumns;
  let row = Math.floor(i / numColumns);

	// Compute circle center
	let x = column * cellWidth + 0.5 * cellWidth;
  let y = row * cellHeight + 0.5 * cellHeight;
});
```

To recap, we iterate through the array of data. The column number is calculated using the modulo operator `%` and the row number using divison and `Math.floor`. The x coordinate of each circle is then calculated by multiplying the column number by `cellWidth` and adding another `0.5 * cellWidth` (to locate the circle in the center of the cell). Likewise for the y coordinate.

In the next practical you’ll apply a similar technique to Energy Explorer.