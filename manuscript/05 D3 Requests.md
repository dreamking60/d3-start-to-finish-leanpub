# Requesting data with D3

Let’s start learning D3!

We’ll start with data requests. This is a nice topic to start your D3 journey as it’s not too hard to understand and it allows you to load some data into the browser (in order to visualize it).

First let’s learn about HTTP requests. **HTTP** stands for **Hypertext Transfer Protocol** and is a protocol (or ‘agreement’) used for communication between a web browser and web server.

Any time a web browser wishes to request a resource, be it an HTML file, a JPEG image or a CSV file, it uses an HTTP request.

Typically the data (or resource) will have a URL (Uniform Resource Locator) such as `https://assets.codepen.io/2814973/my-csv.csv`. (The URL can also be relative to the `index.html` of your web application, so it’ll often look like `data/my-csv.csv`.)

Beware that things get more complicated if the resource has a different domain (i.e. the `assets.codepen.io` part) to your web application due to [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) restrictions. In this book, the data will be hosted on the same server so this isn’t an issue.

D3’s request methods make the HTTP request for you and parse the incoming data so that it’s ready to use. (Parsing CSV files is non trivial so this saves a lot of work.)

In this section we’ll look at four of D3’s request methods: `d3.csv`, `d3.tsv`, `d3.dsv` and `d3.json`. These are the most common methods used when visualising data. (In fact, `d3.csv` and `d3.json` are by far the most commonly used.)

Let’s look at `d3.csv` first.

## d3.csv

`d3.csv` makes an HTTP request for a comma-separated value (CSV) file and converts the incoming file into an array of objects.

The syntax of `d3.csv` is:

```
d3.csv(url [, row])
```

(The square brackets denote an optional argument.)

`url` is a string containing the URL while `row` is an optional function that can perform transformations on each row of the CSV file.

We’ll request a CSV file with URL `https://assets.codepen.io/2814973/my-csv.csv` that looks like:

```
name,indicator1,indicator2
Paris,4030,13.45
Tokyo,3912,45.41
New York,19481,32.53
```

A typical pattern is:

```
function dataIsReady(data) {
console.log(data);
}
d3.csv('https://assets.codepen.io/2814973/my-csv.csv').then(dataIsReady);
```

`d3.csv` requests the CSV file. A function (known as a **callback function**) is passed into `.then`. In this example, the callback function is named `dataIsReady`.

When the data loads the callback function is called. The first parameter of the callback function (`data` in our example) is an array of objects, where each object represents a row of the CSV file:

```
[
{
name: "Paris",
indicator1: "4030",
indicator2: "13.45"
},
{
name: "Tokyo",
indicator1: "3912",
indicator2: "45.41"
},
{
name: "New York",
indicator1: "19481",
indicator2: "32.53"
}
]
```

> A more detailed explanation is: `d3.csv` evaluates to a ‘promise’. A [promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) is an object that represents an ‘asynchronous’ operation. This means `d3.csv` makes the request **but doesn’t wait for the resource to arrive**. Instead the program continues to execute while the resource loads. The promise object has a method `.then` which lets you specify a callback function that’s called when the CSV file loads.

D3 converts the CSV file into an **array of objects**. Each object corresponds to a row in the CSV file (excluding the header) and has properties corresponding to each item in the header (`name`, `indicator1` and `indicator2` in our example).

Notice that the `indicator1` and `indicator2` property values are strings instead of numbers. D3 assumes everything in the CSV file is a string and doesn’t convert anything automatically.

It’s a good idea to convert strings to numbers as soon as you can and this can be done by providing a function as the second argument to `d3.csv`:

```
function transformRow(d) {
// returns a new object
}
d3.csv('https://assets.codepen.io/2814973/my-csv.csv', transformRow)
.then(dataIsReady);
```

The `transformRow` function gets called for each row (excluding the header row) of the CSV file. D3 converts the row into an object and passes the object into `transformRow`. You then create a new object with any transformations you wish to apply and return it.

For example let’s convert `indicator1` and `indicator2` into numbers:

```
function dataIsReady(data) {
console.log(data);
}
function transformRow(d) {
var ret = {
name: d.name,
indicator1: +d.indicator1,
indicator2: +d.indicator2
};
return ret;
}
d3.csv('https://assets.codepen.io/2814973/my-csv.csv', transformRow)
.then(dataIsReady);
```

> The `+` operator converts a string to a number.

Now the array of objects will look like:

```
[
{
name: "Paris",
indicator1: 4030,
indicator2: 13.45
},
{
name: "Tokyo",
indicator1: 3912,
indicator2: 45.41
},
{
name: "New York",
indicator1: 19481,
indicator2: 32.53
}
]
```

Each indicator is now a **number** instead of a string.

To recap:

* `d3.csv('my-url', transformRow)` requests a CSV file located at `'my-url'` and returns a promise
* `.then(dataIsReady)` configures the promise such that when the CSV file arrives the function `dataIsReady` is called
* D3 converts the CSV file into an array of objects and transforms each row using the function `transformRow`
* D3 then passes the array of objects into `dataIsReady`

## d3.tsv

`d3.tsv` is the same as `d3.csv` except it processes the incoming file as a tab-separated value (TSV) file.

## d3.dsv

`d3.dsv` is a generalized version of `d3.csv` and `d3.tsv` which processes the incoming file using a specified separator character.

The first argument of `d3.dsv` is the separator character.

Thus:

```
d3.dsv(',', 'my-url', transformRow);
```

is the equivalent of

```
d3.csv('my-url', transformRow);
```

## d3.json

`d3.json` makes a request for a JSON file and parses it into a JavaScript array or object.

Suppose we have a JSON file at `https://assets.codepen.io/2814973/my-json.json` that looks like:

```
[
{
"name": "Paris",
"indicator1": 4030,
"indicator2": 13.45
},
{
"name": "Tokyo",
"indicator1": 3912,
"indicator2": 45.41
},
{
"name": "New York",
"indicator1": 19481,
"indicator2": 32.53
}
]
```

You can request it using:

```
function dataIsReady(data) {
console.log(data);
}
d3.json('https://assets.codepen.io/2814973/my-json.json')
.then(dataIsReady);
```

This uses a similar pattern to `d3.csv`. `d3.json` makes the request and evaluates to a promise. `.then(dataIsReady)` specifies that `dataIsReady` is called when the JSON file arrives. D3 converts the JSON file into a JavaScript array or object.

The console output of our example looks like:

```
[
{
"name": "Paris",
"indicator1": 4030,
"indicator2": 13.45
},
{
"name": "Tokyo",
"indicator1": 3912,
"indicator2": 45.41
},
{
"name": "New York",
"indicator1": 19481,
"indicator2": 32.53
}
]
```

JSON is closely related to JavaScript’s arrays and objects so when you load a JSON file using `d3.json` you’re creating an array or object that mirrors the JSON file.

## CSV, TSV or JSON?

What’s best: CSV, TSV or JSON?

It depends.

In my experience, CSV is most often used when the data has been **exported from a spreadsheet**. JSON is usually used to return results **from an API call** (see the next section).

JSON also has the ability to represent more complex structures than CSV files (which are basically tables).

You’ll see later in this book that we use CSV to represent the data for the Energy Explorer.

## Static resources and APIs

If the data your’re visualising doesn’t change very often then hosting it **statically** should suffice. Static hosting is where files (be it HTML, JavaScript, CSV or JSON files) live on a server’s hard drive and are sent to a web browser when requested.

For example the CSV file (at `https://assets.codepen.io/2814973/my-csv.csv`) is statically hosted.

If the data is frequently updated (or particularly complex) then it’s often served via an API. An API (Application Programming Interface) is a program on a server that receives an HTTP request, makes a database query and returns the result (usually as a JSON file).

Whether your data is statically hosted or comes from an API it doesn’t matter to D3. So long as the incoming data is in the agreed format (i.e. CSV if you’re using `d3.csv`) D3 will load data from a static resource or an API.

For simplicity the Energy Explorer will load data from a statically hosted CSV file.