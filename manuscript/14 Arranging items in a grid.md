# Arranging items in a grid

Arranging a list of items in a grid is a fairly common exercise but it isn’t always obvious how to do this so this section covers the underlying mathematics and code.

Given an array of numbers such as:

```
[10, 60, 40, 30, 70, 40, 30, 30, 10, 60, 50, 10]
```

we’d like to represent the array using a grid of circles:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-15.png)

The number within each circle indicates the array index. The first circle is top left and represents the first element of the array.

The grid has 4 columns and we’d like to calculate the centers of each circle relative to the origin (where the x and y axes meet).

Let’s start by figuring out the row number and column number of each cell. The first row and first column are numbered zero:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-14.png)

We can calculate the column number using JavaScript’s modulo operator `%`. This returns the **remainder** of a division.

For example for the 9th circle, `9 % 4` returns `1` because the remainder of 9 ÷ 4 is 1. Thus the 9th circle is in column 1. (The number after the `%` is the **divisor**.)

If we evaluate the modulo operator (using a divisor of 4) on numbers up to 10 we get:

<table><tbody><tr><td>0 % 4</td><td>0</td></tr><tr><td>1 % 4</td><td>1</td></tr><tr><td>2 % 4</td><td>2</td></tr><tr><td>3 % 4</td><td>3</td></tr><tr><td>4 % 4</td><td>0</td></tr><tr><td>5 % 4</td><td>1</td></tr><tr><td>6 % 4</td><td>2</td></tr><tr><td>7 % 4</td><td>3</td></tr><tr><td>8 % 4</td><td>0</td></tr><tr><td>9 % 4</td><td>1</td></tr><tr><td>10 % 4</td><td>2</td></tr></tbody></table>

The result of the modulo operation wraps around every `n` times (where `n` is the divisor). This is the same as the column number which wraps around every `n` times where `n` is the number of columns.

Thus we can use `%` to compute the column number from the array index `i`:

```
var column = i % numColumns;
```

where `numColumns` is the number of columns.

We can calculate the row number using division together with JavaScipt’s `Math.floor` function. `Math.floor` returns the nearest whole number below a given number.

For example:

```
Math.floor(7.65); // returns 7
```

If we divide (using a divisor of 4) each number up to 10 and apply `Math.floor` we get:

<table><tbody><tr><td><strong>i </strong>÷ <strong>n</strong></td><td><strong>Result of i</strong> ÷ <strong>n</strong></td><td><strong>Result of Math.floor(i ÷ n)</strong></td></tr><tr><td>0 ÷ 4</td><td>0</td><td>0</td></tr><tr><td>1 ÷ 4</td><td>0.25</td><td>0</td></tr><tr><td>2 ÷ 4</td><td>0.5</td><td>0</td></tr><tr><td>3 ÷ 4</td><td>0.75</td><td>0</td></tr><tr><td>4 ÷ 4</td><td>1</td><td>1</td></tr><tr><td>5 ÷ 4</td><td>1.25</td><td>1</td></tr><tr><td>6 ÷ 4</td><td>1.5</td><td>1</td></tr><tr><td>7 ÷ 4</td><td>1.75</td><td>1</td></tr><tr><td>8 ÷ 4</td><td>2</td><td>2</td></tr><tr><td>9 ÷ 4</td><td>2.25</td><td>2</td></tr><tr><td>10 ÷ 4</td><td>2.5</td><td>2</td></tr></tbody></table>

The right hand column starts at 0 and increments every 4 rows.

In general, the result of `Math.floor(i / n)` increments every `n` times. (The JavaScript symbol for division is `/`.)

Thus we can use the following to calculate the row number from the array index `i`:

```
var row = Math.floor(i / numColumns);
```

Now we’ve figured out how to compute the row and column numbers let’s compute the coordinates of the top left corner of each square cell.

The width (`cellWidth`) of each square cell is `width ÷ numColumns` where `width` is the overall width and `numColumns` is the number of columns:

```
var width = 1000;
var numColumns = 4;
var cellWidth = width / numColumns;
```

We can compute the x coordinate of the top left of a cell by multiplying the cell’s column number `column` by `cellWidth`:

```
var x = column * cellWidth;
```

Each circle is located in the center of its square cell, so we can compute the the x coordinate of a circle’s center by adding `0.5 * cellWidth` to `x`:

```
var x = column * cellWidth + 0.5 * cellWidth;
```

The `y` coordinate is computed in a similar manner:

```
var width = 1000;
var numColumns = 4;
var cellWidth = width / numColumns;
var cellHeight = cellWidth;  // the height is the same as the width
var row = Math.floor(i / numColumns);
var y = row * cellHeight + 0.5 * cellHeight;
```

Therefore complete code for computing the circle centers looks like:

```
var data = [10, 60, 40, 30, 70, 40, 30, 30, 10, 60, 50, 10];
var width = 1000;
var numColumns = 4;
var cellWidth = width / numColumns;
var cellHeight = cellWidth;
data.forEach(function(d, i) {
var column = i % numColumns;
var row = Math.floor(i / numColumns);
var x = column * cellWidth + 0.5 * cellWidth;
var y = row * cellHeight + 0.5 * cellHeight;
});
```

To recap, we iterate through the array of data. The column number is calculated using the modulo operator `%` and the row number using divison and `Math.floor`.

The x coordinate of each circle is then calculated by multiplying the column number by `cellWidth` and adding another `0.5 * cellWidth` (to locate the circle in the center of the cell). Likewise for the y coordinate.

In the next practical you’ll apply a similar technique to the Energy Explorer.