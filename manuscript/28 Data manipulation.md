# Data Manipulation

This chapter introduces another JavaScript library called Lodash ([https://lodash.com/](https://lodash.com/)). Lodash is a general purpose utility library that provides a large number of functions that perform common tasks. It’s especially useful when working with data (in particular arrays and objects). Later on you’ll use it in the Energy Explorer to sort the countries.

One of the advantages of using a library such as Lodash is that it overcomes web browser differences. It’s perhaps not as prevalent as it once was, but some commonly used functions might weren’t available in certain web browsers. Using Lodash was a convenient way of overcoming this issue. It’s beyond this book to show everything that Lodash can do, but we’ll look at some of the functions I commonly use when building data visualisations.

If you wish to try any of the following examples out you can do so by visiting jsconsole at [https://jsconsole.com](https://jsconsole.com) and entering:

```js
:load lodash
```

This loads Lodash into jsconsole and you can type in statements like `_.uniq([40, 30, 40, 10, 10])`.

## Installing Lodash

Lodash can be installed in a number of ways. In this book, we’ll keep things simple and include it by downloading it from the home page at [https://lodash.com/](https://lodash.com/) and including it using a `script` tag in `index.html`.

Let’s start by looking at Lodash’s syntax.

## Lodash syntax

Lodash is an **object** that’s represented by the symbol `_` (underscore) and has a large number of **methods** defined on it. (A method is a function that’s been assigned to an object property.) For example there’s a method `_.sortBy` which takes an array and returns a sorted copy of the array. You invoke it using:

```js
let sortedNumbers = _.sortBy([10, 50, 30, 20]);
```

D>It may seem strange to have a library named `_` but `_` is a valid character as far as variable names are concerned.

Typically Lodash doesn’t change (or mutate) objects or arrays. Instead it generally returns a new object or array. The fact that it doesn’t change anything (or produce ‘side effects’) makes your code more predictable. Let’s look at some of the most useful Lodash methods when working with data.

## _.uniq

The `_.uniq` method takes an array and returns a copy of the array, with all duplicates removed. Here’s an example:

```js
_.uniq([10, 20, 50, 40, 20, 20, 10]);    // [10, 20, 50, 40]
```

I encourage you to try out each of these examples using [jsconsole](https://jsconsole.com/?%3Aload%20lodash).

## _.includes

The `_.includes` method takes an array and a value and returns `true` if the value is in the array. For example:

```js
let data = [10, 20, 40, 50];
_.includes(data, 20);    // true
_.includes(data, 30);    // false
```

## _.without

The `_.without` method takes an array and a value and returns a copy of the array, with instances of the provided value excluded. For example:

```js
_.without([10, 20, 30, 40, 50], 20);    // [10, 30, 40, 50]
```

## _.filter

The `_.filter` method takes an array and a function. It returns a copy of the array containing only the elements for which the function returns `true`. For example, let’s filter an array and only include elements with a value over 30:

```js
_.filter([10, 20, 30, 40, 50], function(d) {
  return d > 30;
});
// [40, 50]
```

`_.filter` can also operate across arrays of objects. For example:

```js
let data = [
  {
    name: "Paris",
    region: "Europe",
    indicator1: 9030,
    indicator2: 13.45
  },
  {
    name: "Madrid",
    region: "Europe",
    indicator1: 3912,
    indicator2: 45.41
  },
  {
    name: "New York",
    region: "North America",
    indicator1: 19481,
    indicator2: 32.53
  }
]

_.filter(data, function(d) {
  return d.region === "Europe";
});

// Returns:
[
  {
    name: "Paris",
    region: "Europe",
    indicator1: 9030,
    indicator2: 13.45
  },
  {
    name: "Madrid",
    region: "Europe",
    indicator1: 3912,
    indicator2: 45.41
  }
];


_.filter(data, function(d) {
  return d.indicator1 > 10000;
});

// Returns:
[
  {
    name: "New York",
    region: "North America",
    indicator1: 19481,
    indicator2: 32.53
  }
]
```

## _.sortBy

The `_.sortBy` method takes an array and returns a sorted copy of the array. For example:

```js
_.sortBy([50, 30, 100, 3, 20])    // [ 3, 20, 30, 50, 100 ]
```

If you have an array of objects you can pass a function as the second argument to specify which property to sort by:

```js
let data = [
  {
    name: "Paris",
    region: "Europe",
    indicator1: 9030,
    indicator2: 13.45
  },
  {
    name: "Madrid",
    region: "Europe",
    indicator1: 3912,
    indicator2: 45.41
  },
  {
    name: "New York",
    region: "North America",
    indicator1: 19481,
    indicator2: 32.53
  }
];

_.sortBy(data, function(d) {
  return d.indicator1;
});

// Returns:
[
  {
    name: "Madrid",
    region: "Europe",
    indicator1: 3912,
    indicator2: 45.41
  },
  {
    name: "Paris",
    region: "Europe",
    indicator1: 9030,
    indicator2: 13.45
  },
  {
    name: "New York",
    region: "North America",
    indicator1: 19481,
    indicator2: 32.53
  }
]
```

You can also pass in the name of a property instead of a function. The following two calls are equivalent:

```js
_.sortBy(data, function(d) {
  return d.indicator1;
});

_.sortBy(data, 'indicator1');
```

JavaScript has a built in sort function but `_.sortBy` has two differences: it returns a new array (rather than replacing the original array) and its number sorting is sane:

```js
let data = [50, 30, 100, 3, 20];

_.sortBy(data);    // [ 3, 20, 30, 50, 100 ]

data.sort();    // data is now [ 100, 20, 3, 30, 50 ]
```

The built in JavaScript sort function converts numbers to strings and sorts alphanumerically which is why you get a surprising result.

### _.orderBy

The `_.orderBy` method is similar to `_.sortBy` except a string `'asc'` or `'desc'` can be passed in as the third parameter to specify whether the sort should be ascending or descending. For example:

```js
let data = [
  {
    name: "Paris",
    region: "Europe",
    indicator1: 9030,
    indicator2: 13.45
  },
  {
    name: "Madrid",
    region: "Europe",
    indicator1: 3912,
    indicator2: 45.41
  },
  {
    name: "New York",
    region: "North America",
    indicator1: 19481,
    indicator2: 32.53
  }
];

_.orderBy(data, function(d) {
  return d.indicator1;
}, 'desc');

// Returns:
[
  {
    name: "New York",
    region: "North America",
    indicator1: 19481,
    indicator2: 32.53
  },
  {
    name: "Paris",
    region: "Europe",
    indicator1: 9030,
    indicator2: 13.45
  },
  {
    name: "Madrid",
    region: "Europe",
    indicator1: 3912,
    indicator2: 45.41
  }
]
```

### Wrapping up

This chapter has introduced the Lodash library and looked at a few of its methods that are useful for data processing. In the next chapter you’ll use Lodash to sort the Energy Explorer countries according to the chosen indicator.