---
layout: post
title:      "Crafting your CSS Selector"
date:       2020-08-20 00:00:06 -0400
permalink:  crafting_your_css_selector
---


Let's talk about selectors. For the purpose of this blog post I'm going to assume you already know the basics of how to use selectors when you're writing your own CSS, though some of what I'll be going over will be helpful for writing your own style sheets too. But if you've ever tried to edit an existing theme somewhere, or used javascript to manipulate the DOM for one specific element, sometimes just finding the class is not enough. Or maybe you find the class but you only want to change *one* of the things in that class. 

This is by no means a comprehensive guide; these are just some of the selecting tricks that I find to be commonly useful. I'll include some links at the end of the post for further reading and a cool practice game.

As we're talking about trying to select very specific things, one super important thing to remember is that the *most specific* selector wins. If a class is defined in the style sheet as `.coolElement`, and in the HTML it is assigned to a div, such as: `<div class="coolElement">`, you can override the styling set in the style sheet by selecting the element with `div.coolElement`.

Sometimes you don't know how things are declared in the style sheet, and sometimes the style sheets DO have very specific selector definitions because they are trying to style a single instance of a class. So you'll need to do some experimenting to really know what you're working with. This is where your browser's dev tools can help. (It's called Dev Tools in Chrome or Developer Tools in Firefox.) In either browser, you can open it by right clicking, and selecting 'inspect'.

In the 'Elements' tab, when you have an element selected, it will show you the entire stack of selectors. You might need to play with the width of the dev tools window to see all of them.

![](https://i.ibb.co/54QxvN0/css-selector-line.jpg)

I've used the selecting tool, which is the icon in the red square on the top left of the dev tools, to pick out the first paragraph of a wikipedia article. In the second, longer red box towards the bottom, it shows all css tags, ids, and classes that lead up to that specific element. The `p` all the way on the right, is a **type** selector. However, if you tried to change anything about the first paragraph by just defining something with `p`, you would alter every instance of `p` on the page. So you need to be more specific.

Next over is `div.mw-parser-output`. Without looking through the entire markup, you don't really know if there's another div with that class, and if it isn't the only one, you'll be changing all of the `p`'s in those too. So you could try `div.mw-parser-output p`. But even if it was the only div with that class, this would still select every `p` cpntained in it, and if you wanted to only alter the first one, you'd still need to get more specific. 

Enter **structural pseudo-classes**. Structural pseudo-classes are used to select child elements within the context of the parent. There is one in particular that will help in our scenario right now: `:first-child`. This will target the first child element of a parent. To successfully pick out our first paragraph, (as long as there is only one `div` with the `mw-parser-output` class), we could use: *drumroll* 

```
div.mw-parser-output p:first-child
```

(The third linked resource at the end of this post is especially good for showing all of the different kinds of selectors.) 

---

## So now you know how to craft a selector. 
But what happens if you try to implement it, and it doesn't work. And you ask yourself... did I get it wrong?... do I need to be more specific? Luckily, there is a way to test out your selectors with dev tools. If you head over to the console, you can use `document.querySelector` OR `document.querySelectorAll` to see what you get when you use your selector. Using just `querySelector` will return the *first* node that matches that selector. Using `querySelectorAll` will return an array of all nodes that match that selector, which, in fact, is a great way to tell if your selector is specific enough. If you get back an array of multiple nodes, you'll know that any styles you alter will apply to all of them. Let's see an example. You can try it out as well. [This is](https://en.wikipedia.org/wiki/Bucket-brigade_device) the article I'm using for this tutorial. 

```
// First try
document.querySelectorAll('div.mw-parser-output p:first-child')
```

It should return an array of just one node! That's perfect, because it means you've nailed your target. You can even do some live altering, (that won't stick around when you refresh the page).

```
// If you use querySelectorAll, make sure you select the 
// specific node from the array, in this case with [0]
document.querySelectorAll('div.mw-parser-output p:first-child')[0].style.backgroundColor = "pink"

// Or you could just use querySelector, which will return the 
// correct node because it is the only one applicable.
document.querySelector('div.mw-parser-output p:first-child').style.backgroundColor = "pink"
```

---

## One last thing to remember
Say you go to implement your modification, and you've checked your selector, and you're grabbing the right node, but it still doesn't work. This is where that *most specific* rule comes into play. It is entirely possible that the original style sheet defines some styles like this:

```
div.mw-parser-output p:first-child {
     background-color: "blue"
}
```

because who knows... they might. In this case, your selector will lose. But the way to get around that is to keep going up the chain of tags, ids and classes. From the image above, you could expand your selector to `div#mw-content-text.mw-content-ltr div.mw-parser-output p:first-child`. It might look a bit verbose, or like overkill, but in this scenerio, now your styling would win out.

---

## JK, because we love to do that here, there is one more helpful tidbit.
Also, note that there is a space between the `div.mw-parser-output` and the `p`. When stringing selectors together, that space indicates whether the class (or id) is directly assigned to the type selector, or whether it is assigned to a child element within. In this instance, it means that you are selecting a `p` *within* the `div` that is assigned a class of `mw-parser-output`. So, for example:

```
// This would refer to a div element with the class of .mw-parser-output, like in our example.
div.mw-parser-output

// But this would refer to a child element that was assigned 
// the class .mw-parser-output inside of a div.
div .mw-parser-output
```

---

**And as promised,** here is [a cute game to practice your selecting](https://flukeout.github.io/), and also some other helpful resources.

[The 30 CSS Selectors You Must Memorize](https://code.tutsplus.com/tutorials/the-30-css-selectors-you-must-memorize--net-16048)

[CSS Selectors: A Comprehensive Visual Guide](https://dottedsquirrel.com/css/css-selectors/)

[CSS Selectors Cheat Sheet](https://www.web4college.com/questions/css-selectors-cheat-sheet.php)
