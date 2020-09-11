---
layout: post
title:      "Styling Links"
date:       2020-09-10 23:12:21 -0400
permalink:  styling_links
---


Let's talk about links! I have a semi-personal vendetta against links and buttons. The short version is that I always want to alter the styling, and they aren't always super cooperative. Links aren't so bad. The `<button>` tag in HTML can be amazingly stubborn, so I'm going to save that for next week. 

## So, links.
Styling links isn't so tricky, especially when writing an original program. There are five link pseudo-classes that can be used to style links. (Technically two actually, but three others that are still used to style links. [Check out this article for more info on the pseudo-classes.](https://www.webfx.com/blog/web-design/link-pseudo-classes/))

To cover all the bases, styles can be set for a link in these scenarios:
- `a` - general styling for all links. the default
- `a:link` - a link that has not yet been visited by the user
- `a:visited` - a link that *has* been visited by the user
- `a:hover` - the link while the user is hovering over it
- `a:focus` - and the link while the user is *focused* on it, such as if they've tabbed to it, or right after it has been clicked
- `a:active` - the link while the user is actually clicking it

Not all of these need to be styled. Anything that isn't will have the default styling of a link. I often only style the `a`, `a:visited`, `a:active`, and `a:hover`. `a:link` when left alone will inherit the styling of `a`. And I personally don't dislike the usual default appearance of a `focus`ed link. This is all of course up to the discretion of the programmer. 

[By the way, you can play around with this JS Fiddle to see just how customizable your links can be.](https://jsfiddle.net/saranderson13/12vwsLzh/)

Note that the *order* in which each pseudo class is defined is important. `a:hover` must come AFTER `a:link` and `a:visited`. And likewise, `a:active` must be AFTER `a:hover` and `a:focus`.

**Up there in that JS Fiddle,** there is an example where each pseudo-class is individually defined, and one where some of them are grouped, just to illustrate that yes, they can be grouped the same as any other css selectors. For instance, I dislike visually differentiating between a visited vs unvisited link, so I often style those together. However, they still need to abide by the correct order. 

## Fancier styling!
Styling links can get pretty fancy, and one quick way to up the aesthetic is to include transitions. Such as fades and the like. The third example in the JS Fiddle demonstrates a fading link, where there is a smooth transition from the basic state, to the hovered state.

By setting a `transform` attribute in the `a`, `a:link`, and `a:visited`, it is possible to enable any attribute that differs in the `a:hover` to gently transition. In this example, the color will fade to pink quickly, and the font size will grow slowly.

```
.fancy {
  font-family: monospace;
  text-transform: uppercase;
  font-size: 13px;
}

a.fancy, a.fancy:visited { 
  color: black;
  text-decoration: none;
  transition: color .4s ease-in-out, font-size 1s ease-in-out;
}

a.fancy:hover, a.fancy:active { 
  color: pink;
  font-size: 16px;
}
```

If there are multiple attributes that should transition in unison, the first argument can be set to `all`. Such as `transition: all .4s ease-in-out;` And there is plenty more that can be transitioned than just font size and color. [This is the documentation for `transition`.](https://www.w3schools.com/cssref/css3_pr_transform.asp)


## Hijacking Link Styling
There is one other thing I wanted to touch on. Up at the top when I qualified my 'styling links isn't so tricky' comment, with 'when writing an original program', it was because styling links while adapting another person's code *can* be tricky if directly editing the CSS is not possible. (Such as when adding 'additional CSS' to a Wordpress theme. It sometimes involves finding super specific selectors. An anchor tag is very often universally styled at the top of the CSS, and then more specifically styled at other points when it should differ from the universal styling, making finding the correct selector somewhat trial and error.

[A few weeks ago I wrote a tutorial on hunting down CSS selectors.](https://saranderson13.github.io/crafting_your_css_selector) This could help if finding the right selector is proving difficult. The key thing to remember is that *the more specific selector will win*. If something is defined in `a`, but then some attributes are re-defined in `a.specialClass`, the `specialClass` styling will be reflected in the application.


