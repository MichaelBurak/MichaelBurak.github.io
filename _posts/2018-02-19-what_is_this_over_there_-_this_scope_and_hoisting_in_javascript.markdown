---
layout: post
title:      "WHAT IS 'this’ OVER THERE? - THIS, SCOPE AND HOISTING IN JAVASCRIPT"
date:       2018-02-19 16:38:23 -0500
permalink:  what_is_this_over_there_-_this_scope_and_hoisting_in_javascript
---




Siri, what in the world is This?

It’s a big existential question, and as someone whose engagement with and entry into coding was through philosophy, I could probably write an essay or two on This. But there’s another good question Siri likely gets asked more…

Siri, where am I?

These are questions of reference and context and they can become incredibly important and rather confusing in code. In JavaScript, understanding the concepts of ‘This’, Scope and Hoisting can become crucial to writing elegant working code.

In this blog, I’ll explain in some detail these three concepts, along with some sample code to explain the applications of them and yes, how tricky they can be.

However, stick with it, as understanding these concepts will make you a better JavaScript-er.

THIS:

JavaScript’s this keyword has so far been referred to in a capitalized form, but in actual use in coding JS, it would be a lower case - this. This is a keyword used to refer to things, a shortcut, it refers to the subject in the context of where it is in code. It can be very useful for figuring out what’s going on in your code, and referring to parts of your code efficiently.

this is rather important to understand because it’s used often to reference objects in JS code. Being able to point to the correct place and write code that executes properly by say, referring to the right variable in your code instead of one that you don’t desire to use can be very crucial.

Explaining this ought to start with the global context - when one is in JS code and outside any function. When you’re thinking global, this refers to the global object. For example, in the browser, this would refer to the window object. I’m writing this(oh, the puns that could be invoked - which is in itself a possible pun in the blog post I am writing) in Google Docs, and so going into the console and inputting

```this ```

Would produce what it refers to, which is the window, or

```Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …}```

Once JS becomes more complex, like using node modules, however, this can and does refer to different objects. Inside a node module, it is equivalent to the module’s exports.

One of the most important uses of this is inside a function. In that case, this contains the value of the object that invokes a function. 

```
var a = {
   b   :"B",
   c    :"C",
   display:function () {
// The "context"
console.log (this.b + " " + this.c);
 }
}

// The "context", or subject, when invoking display, is the a object, display() is invoked on a.
// ‘this’ in display() has the value of a. 
a.display(); // "B C "


// If we invoke display with a new object:
var d = {
b   :"FOO",
c    :"BAR"
};


// We would need to use the apply() method to change what object invokes display().
// hence:
a.display.apply(d)

// So the context is now d because d invoked a.display through .apply(d)
```

The situation gets a bit more tricky in use cases like closures however. 

``` 
var a = {
    letters: [
        {
            b: "B"
        }, 
        {
            c: "C"
        }
    ],
    display: function () {
        // The original "context" for display is the a variable
        //console.log(this.letters); would return the letters inside a.
        
        // However, things get trickier if you add a closure by adding code here...
        this.letters.forEach(function (letter) {
                console.log(this + "would be a letter object, you'd think, as it's iterating over them?")
         // You'd think so, but you'd be wrong. Instead this refers to the window.
         //Trying to specifically refer to the letters within a garners an undefined. 
                console.log("or maybe you get it by calling all the letters like" + " " + this.letters)
         //Returns "or maybe you get it by calling all the letters like undefined"
            })
}
}

a.display()

 //a.display() will simply iterate over each letter, returning this as the window, and then this as undefined, twice.
```

‘This’ can be very powerful as referring to the invoking object in code can be useful, however, it’s easy to get tripped up in certain use cases, such as closures, on what this is. However, there are methods such as .apply() or setting this to a variable at the right line of code that will allow you to shape the context and thus the subject of the code. 

SCOPE:

A related concept to this is that of scope. Scope is important for understanding the accessibility and visibility of resources in code. Knowing, for example, what variable you can use where, or if you can not use a variable you would like to use at a certain point in your code, can help speed along coding or slow it down into a morass. 

Scope generally helps enhance security when used properly. Objects in JS may be in the global scope - such as the variable a in the previous example. However, this can cause vulnerabilities within one’s code. 

In this blog post we have already developed an intuitive understanding and some frame of reference for scope. Once we entered into that closure while demonstrating the perils of ‘this’ within closures, we moved out of the global scope and no longer had access to the letters within the global variable a. 

It is key to understand the difference between local and global scope and how code is scoped while programming in JS. To better understand beyond an understanding of ‘this’, it is useful to learn about hoisting. 

HOISTING: 

Hoisting is simple on its face but reading this sentence may take a couple tries to fully internalize, I know the idea took a bit to sink in for me: “JavaScript variable and function declarations are hoisted and put into memory before the code executes. This means they can be used before they have been declared.”

This seems strange and counterintuitive. How can something be used before it has been declared? The very definitions of the terms seem to render this nonsensical, backwards thinking. Yet, it has a certain sort of logic to it. 

In crude terms, JS looks for the coder saying what something is first of all, before using it. 

JS first looks for function declarations and determines what the variables are. This is the ‘first pass’. It then executes the code. 

``` 

logX();
function logX() {
  console.log('X!');
}

// This will return "X!" to the console.

```

One asks: how does JS know what logX() IS before the line in which the function is written. Simple, JS looks for the format of a function being written, that declaration, and learns it, puts it into memory, before actually running the function! 

However, function declarations, understood on the first pass, don’t run into the problems that function expressions can. 

Here is an example of a simple function expression: 

```
logX();
var logX = function() {
  console.log('X!');
};

// This will return an error that logX is not a function! That's because JS attempts to execute the code in those two distinct passes and a declaration stores in memory while an expression does not.
// The interpreted code, what JS does on the second pass, and displays the problem is something like this:

var logX;
logX();  //There's nothing for logX() to point to at this point! That comes later!
logX = function() {
  console.log('X!');
};

```

How does this fit into scoping and link to this? Why, variable assignments! 

```
console.log(loggedLetter);
var loggedLetter = 'Y!';

//When JavaScript actually runs this code, the first pass
//discovers the variables then the code is run. So it's actually 
//interpreted like this:

var loggedLetter;
console.log(loggedLetter); //Again, there’s nothing for loggedLetter to really point to, like the problems we ran into with ‘this’ and scope!
loggedLetter = "Y!" // Finally, something to point to, but for JS? Too late. 
```

This, scoping and hoisting are complicated concept, and it’s easy to get tripped up. Tutorials, documentation and practice with live code like in JSBin can help hone one’s skills. Mastering this, scoping and hoisting allows one to understand what is being referred to where, maintain the security of referring to things only where they should be, and be able to refer and set referents accurately so that your code will not run into errors or that dreaded ‘undefined’. 

