{backmatter}

# Appendix A: Tools and Set-up

This book uses CodePen for many of the code examples. It's a reasonable tool for building small applications but larger applications are typically developed locally. This means using tools and files **on your computer** (rather than on the web). This appendix outlines a typical set up for developing web applications on your own computer. This is similar to the set-up most web developers use. The first section gives an overview of the necessary tools and the second section walks you through setting up these tools.

This content originally appeared in my [Fundamentals of HTML, SVG, CSS and JavaScript for Data Visualisation](fundamentalsbook) book.

## Web development tools

The tools necessary for web development are:

* a **code editor**
* a **web browser**
* a **web server** (not so important if you’re not loading data)

You could also add a **terminal emulator** to this list. A terminal emulator allows you to interact with your operating system via text commands. For example Terminal (on MacOS) and the Command shell (on Windows). You can work through all the material in this book without a terminal emulator so I won't cover them in this book. If you'd like to learn more I suggest a web search for 'command line tutorial'.

### Code editor

A code editor is a desktop application that allows you to edit HTML, CSS and JavaScript files. You can use simple applications such as Notepad (on Windows) and TextEdit (on Mac). It’s important that these editors work in plain text mode so that they don’t insert extra characters. However I recommend using a dedicated code editor such as [Visual Studio Code](https://code.visualstudio.com/), [Atom](https://atom.io/) or [Brackets](http://brackets.io/). All of these are free and multi-platform. Advantages of using a dedicated code editor include:

* you can treat your application as a project making it easier to open and navigate
* syntax highlighting (code is coloured according to whether its a keyword, a variable etc.)
* numerous plug-ins (including web servers!) that make your life as a coder easier

D>Adobe dropped support for Brackets but at the time of writing it's been resurrected by the open source community. It does seem to be in a state of flux so keep checking its status if you're interested.

### Web browser

A modern web browser such as Chrome, Firefox, Safari or Edge is recommended for web development. Create With Data books assume that **Chrome** is being used. The majority of material will apply regardless of your browser choice but there may be differences if the debugging tools are used, as these differ from browser to browser.

### Web server

A web server typically returns (or ‘serves’) HTML, CSS and JavaScript files in response to requests from a web browser. For example if you type `createwithdata.com` into a web browser the browser connects with a web server and requests files that make up that website. The web server responds by sending the requested files back to the browser.

When you create a web site on your own computer you don’t necessarily need your own web server running. (You can open an HTML file directly in most web browsers.) However if you need to make requests from within your web application (as is usually the case with web based data visualisations) you will need your own local web server. (This is due to security restrictions in web browsers.) If you’re an experienced developer and are comfortable setting up a local web server, feel free to skip ahead.

The options for running a local web server (starting at the easiest) include:

* Brackets editor built-in web server
* Atom or Visual Studio Code plug-ins
* Python, Ruby, PHP or Node servers

Another option which I’ve not personally explored is [Servez](https://greggman.github.io/servez/) which is a simple GUI based web server.

D>Personally I use a Node server (such as [http-server](https://github.com/http-party/http-serverhttps://github.com/http-party/http-server)) and I recommend this if you get serious about web development. However if you're starting out Brackets is a great choice.

#### Brackets web server

[Brackets](http://brackets.io/) has a built in web server so this is by far the easiest option. This [video](https://youtu.be/KJXdvaY9lTA?t=122) gives a good demonstration. If you’re new to code editors I recommend using Brackets.

#### atom-live-server

Atom has a web server plug-in called [atom-live-server](https://atom.io/packages/atom-live-server).

To install atom-live-server:

* open the Settings panel (in the File menu)
* click **Install** in the Settings panel
* type `live-server` into the Search Packages search box
* look for `atom-live-server` and click Install
* type `ctrl-alt-l` to start the server

Your web browser should then open your web application. More detailed instructions are [here](https://atom.io/packages/atom-live-server) and a video demonstration is [here](https://www.youtube.com/watch?v=0Xy3yDDY4IE).

(If you’ve multiple projects open, the first one in the project browser will be served.)

#### VS Code Live Server

Visual Studio Code has a web server plug-in called [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer).

To install Live Server:

* select ‘View’ and then ‘Command Palette…’ from the menu bar

{width: 50%}
![Open Command Palette in VS Code](d717b39536c6d8db2b2de77247886680.png)

* type ‘extension’ and select ‘Extensions: Install Extensions’

{width: 50%}
![Select Extensions: Install Extensions](758fc48409e013f521b425ce750d31e1.png)

* search for ‘live server’ (by Ritwick Dey) in the extensions search box and click Install

{width: 50%}
![Search for 'live server'](a1d14e404fc8f56eec28c40e0dfd2567.png)

* open a directory containing your code

{width: 50%}
![Open a directory cotaining your code](a4466b2227af9203170369748abdee9f.png)

* click the **Go Live** button in the bottom bar

{width: 25%}
![Click the Go Live button](7639d9be85c152e9a18293484e41967d.png)

Your web browser should then open your web application. If it doesn’t, open a web browser and go to `localhost:5500`. (If a different port number to 5500 is shown in the 'Server is Started' message, use that number instead.) More detailed instructions are [here](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer).

{width: 50%}
![Server has started](e7600c7555f3ff521f83a1b460d49485.png)

#### Python, Ruby, PHP, Node

If you’re a Python, Ruby or PHP developer you can use the language’s built in webserver. There are some useful instructions [here](https://gist.github.com/willurd/5720255).

(MacOS includes Python and Ruby so you can use one of these if you’re a Mac user.)

If you’re a JavaScript developer using Node you can use [http-server](https://www.npmjs.com/package/http-server).