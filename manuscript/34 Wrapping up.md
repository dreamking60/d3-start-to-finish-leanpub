# Wrapping up

First of all, congratulations for getting this far. It’s been a long journey and hopefully you’ve learned a lot along the way.

The aim of the book was to teach you the fundamentals of D3 and to show you how a real world, interactive data visualisation is built. Hopefully this book has succeeded in both.

Some of the things you learned were:

* what D3 is and what it’s used for
* D3’s approach to constructing data visualisations – how it’s more of a building blocks approach rather than a collection of ready made charts
* the main functional aspects of D3, namely selections and it’s large collection of modules
* in depth looks at selections and data joins
* data requests using D3
* D3’s scale functions
* layout functions (to separate the geometric computations from the rendering code)
* different approaches to JavaScript modules
* joining arrays to groups of elements
* event handling with D3
* Flourish’s popup library
* simple state management
* using lodash to manipulate data
* D3’s transitions
* detailed styling with CSS

Along the way you built the Energy Explorer. Some key aspects of the build were:

* using a layout function to perform the geometric computations. This makes the application easier to test and also would make it easier to switch to a different rendering library if necessary
* joining an array to a group of elements in order to place a country’s circles in a single `g` element
* a significant amount of detailed styling, which hopefully has shown you the amount of CSS work that is often necessary to build a real world data visualisation
* adding an information popup to the visualisation
* handling state using something similar to the Flux pattern. This is a simple, but powerful pattern that’s useful when building data visualisations (and other interactive applications)
* using D3 to create a menu
* sorting the countries using lodash
* adding transitions (animations) to the visualisation
* adding a legend, header and footer to the visualisation

Most (if not all) of what we’ve covered can apply to other visualisations and hopefully you’ve learned enough to help you build your own interactive data visualisation using D3.

## Where next?

It’s really up to you where you’d like to take the knowledge you’ve learned in this book. However, some ideas are:

* modify the Energy Explorer. For example, you could try changing the color scheme, or other aspects of the look and feel. If you’re feeling ambitious you might want to adapt it to a different data set!
* try building your own data visualisation from scratch, using similar techniques to those your learned in this book. You could use the same data set, or one of your own
* share your experiences in the forum and let others know how you got on
* learn more about D3 through its homepage or other resources such as my own D3 in Depth, or tutorials from [Nadieh Bremer](https://www.visualcinnamon.com/blog/), [Curran Kelleher](https://www.youtube.com/user/currankelleher), [Shirley Wu](https://sxywu.com/) and [Amelia Wattenberger](https://wattenberger.com/blog).

## Wrapping up

Thank you so much for reading D3 Start to Finish, and for getting this far! I hope you found this book both useful and enjoyable. And I truly hope you’ve learned lots from it. I hope you’ve gained the confidence to work on other data visualisations written using D3 and perhaps even to build your own visualisation from scratch.

Stay in touch, either by email at info@createwithdata.com or via Twitter (@createwithdata).

All the best, and keep coding!

Peter