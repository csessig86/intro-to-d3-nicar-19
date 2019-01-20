# Part 1: Intro to Javascript, D3

### Intro to Javascript
Javascript is one of the three core technologies on the internet, along with HTML and CSS. It is run in the browser and is primarily used for adding, removing and interacting with elements on a page. In contrast, HTML are the elements on the page, while CSS is used to style these elements.

Javascript is typically stored in .js files. It can also be written in html pages. But generally, you want to keep your javascript in one or several .js files, especially as your code gets more complicated.

In this example, we've created an index.html page and a script.js file, which you can use to write your javascript code. You will need to open up the index.html file in a browser, as well as that browser's developer tools. Here's more information on the dev tools for [Chrome](https://developer.chrome.com/devtools) and [Firefox](https://developer.mozilla.org/en-US/docs/Tools).

### 1. Comments, console.log
* Comments allow you to write notes for your future self so you know what certain pieces of code are supposed to do. The script.js file already includes a comment.
* Console.log statements are great for debugging code. Whatever is inside of the console.log statement will be shown in your browser's dev tools.
```javascript
// Example of a comment

// Example of a console.log
console.log(5 + 10);
```

As you work through this exercise, go ahead and type out the lines of code in your dev tool's console panel. When you do this, you will see the code being ran in the browser's console.

Alternatively, one can type these in your script.js file. If you do this, you will need to wrap them in a console.log statement to have the code shown to you in the browser's console.

2. Math, Dates
* The most basic thing you can do in javascript is math.
```javascript
5 + 10  // returns 15
10 / 5  // returns 2
10 * 5  // returns 50

Math.round(10.7);   // returns 11
Math.max(0, 150, 30, 20, -8, -200); // returns 150
Math.floor(4.7);    // returns 4
```

If you want to wrap these in console.log statements, it would look like so:

```javascript
console.log(5 + 10)
console.log(10 / 5)
console.log(10 * 5)

console.log(Math.round(10.7) )
console.log(Math.max(0, 150, 30, 20, -8, -200) )
console.log( Math.floor(4.7) )
```

### 2. Variables
* Variables allow you store pieces of information to use later in your javascript file.
```javascript
var number = 5 + 10; // This is a number
var string = 'All hail Javascript' // This is a string

number // returns 15
string // returns 'All hail Javascript'

number += 15
number // returns 30

string += ', the best programming language ever' // This will return 'All hail Javascript, the best programming language ever'
```

Remember that console.log statements are basically notes to yourself. You don't need console log statements to create and change variables, for instance, but if you want them to show up in your browser's console, one way to do this is wrap the variable name in a console.log statement like so:
```javascript
console.log(number)
console.log(string)
```
