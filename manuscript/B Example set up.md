# Example set-up

This section takes you through setting up a web development environment and the creation of a simple website (using HTML, CSS and JavaScript).

The steps are:

* **create a directory** for your project
* choose and install a **code editor**
* create a simple **HTML** file
* get a local **web server** up and running
* add **CSS** and **JavaScript** files

We’ll use VS Code and its Live Server extension. If you’re comfortable using a different editor and web server then please feel free to do so.

## Create a project directory

Using File Explorer (Windows) or Finder (Mac) create a directory (or folder) that’ll contain your project. Name it `my_website` (or something similar). You might want to place it in another directory named `coding_projects` (or similar) if you plan on having more than one project.

## Install a code editor

1.  Download VS Code from [here](https://code.visualstudio.com/) and install
2.  Run VS Code

## Create a minimal HTML file

In this section you’ll create a simple HTML file. In VS Code select **New** from the **File** menu. VS Code creates a new (unsaved and untitled) file.

The following code is just about the shortest and valid HTML document possible. It serves as a good starting point to a new web page. Copy it and paste into your new document.

{caption: "index.html"}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>My webpage</title>
  </head>

  <body>
    <h1>Hello!</h1>
  </body>
</html>
```

Usually the only code you’ll need to change is the title (between the `<title>` tags and the content (between the `<body>`) tags.

D>The `<!DOCTYPE>`, `lang="en"` and `charset="utf-8"` are standard bits of code that tell the browser how to interpret your HTML file. Although your webpage may appear to work without these it’s best to leave them in. (See [here](https://www.w3.org/International/questions/qa-what-is-encoding) for more information about character encoding if you’re interested.)

Select **Save** from the **File** menu, navigate to your project directory, name your file **index.html** and click Save. (It’s important to name your file **index.html**.) Once your file is saved you should see **index.html** appear in the sidebar.

## Install Live Server extension

Follow the instructions in the [Web development tools](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/sections/tools-set-up-etc/) section to install the Live Server extension.

## Start Server

If you haven’t already, select **Open Folder…** from the **File** menu and select your `my_website` directory. You should see your new directory appear in the sidebar. Now click on the Go Live button that appears in the bottom bar.

{width: 25%}
![Click the Go Live button in VS Code](https://learn.createwithdata.com/wp-content/uploads/2021/05/image-4.png)

Make sure your browser is pointing at ‘localhost:5500’ (use whichever port number is displayed in the bottom bar).

{width: 50%}
![Server has started message (with active port number displayed)](https://learn.createwithdata.com/wp-content/uploads/2021/05/image-5.png)

Your page should now be visible in the web browser (it should say ‘Hello!’). Try editing your HTML file, saving it, then refreshing your browser. The changes you’ve made should be visible.

## Add CSS and JavaScript files

In this section you’ll add a CSS file and a JavaScript file to your web page. There are usually two steps to adding a file to a web page:

* create the file and save it (with extension `.css` for CSS files or `.js` for JavaScript files)
* include the file in your `index.html` file

### Add a CSS file

Select **New** from the **File** menu and add the following CSS code to the new file:

{caption: "style.css"}
```css
h1 {
  color: orange;
}
```

Select **Save** from the **File** menu and when the dialog appears create a new directory called `css` within your project directory. Go into the `css` directory then save your new file as `style.css`. You’re aiming for the following directory structure:

```text
my_website
  css
    style.css
  index.html
```

You now need link to your CSS file in `index.html`. Start editing your HTML file by clicking `index.html` in the left sidebar. Add the line with the `<link>` tags (highlighted below):

{caption: "index.html"}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>My webpage</title>
markua-markua-start-insert
    <link rel="stylesheet" href="css/style.css">
markua-markua-end-insert
  </head>

	<body>
	  <h1>Hello there!</h1>
  </body>
</html>
```

Save `index.html` and check your browser (you might need to refresh the page). The title should now be **orange**.

### Add a JavaScript file

Select **New** from the **File** menu and add the following JavaScript code to the new file:

{caption: "main.js"}
```js
  alert("Hello!");
```

Select **Save** from the **File** menu and when the dialog appears create a new directory within your project directory called `src`. Go into the `src` directory then save your new file as `main.js`. You’re aiming for the following directory structure:

```text
my_website
  css
    style.css
  src
    main.js
  index.html
```

D> If your files aren’t in the above structure you can drag them into their correct location in the left sidebar of VS Code. You can also right click files and directories for more options.

You now need to add a link to your JavaScript file in `index.html`. Start editing your HTML file by clicking `index.html` in the left sidebar. Add the line containing the `<script>` tag (highlighted below):

{caption: "index.html"}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>My webpage</title>
    <link rel="stylesheet" href="css/style.css">
  </head>

	<body>
    <h1>Hello there!</h1>
markua-markua-start-insert
    <script src="src/main.js"></script>
markua-markua-end-insert
  </body>
</html>
```

Save `index.html` and check your browser (you might need to refresh the page). An alert should appear saying ‘Hello!’.

## Summary

This section has shown how to set up a web development environment consisting of:

* a code editor
* a web browser
* a local web server

You saw how to create a simple web site consisting of an HTML file, a CSS file and a JavaScript file. This is the basis for most web applications including data visualisations.