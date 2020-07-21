_The [CSS Diner](https://flukeout.github.io/) is worth a regular visit to keep this in your fingers!_

**Select all of tag name**
```css
p { /* rules */ }
```

**Select all of class name**
```css
.these { /* rules */ }
```

**Select the element with unique id**
```css
#this { /* rules */ }
```

**Select everything** (use with caution!)
```css
* { /* rules */ }
```

**Grouping selectors**
Applied to HTML elements that have any of the selectors.
```css
.these, #this { /* rules */ }
```

**Chaining selectors**
Applied to HTML elements that have all the selectors.
```css
p.these { /* rules */ }
```

**Descendant selectors**
Applied to any HTML elements where the second selector is inside the first selector (no matter how far nested).
```css
section#info li.name { /* rules */ }
```

**Direct Descendant selectors**
Applied to any HTML elements where the second selector is inside the first selector as a direct descendant.
```css
section#info > li.name { /* rules */ }
```

**Sibling selectors**
Applied to any HTML elements where the second selector immidiatly follows the first selector on the same level.
```css
h1 + p { /* rules */ }
```

**Attribute selectors**
Applied to any HTML elements with the matching attribute.
```css
input[name="surname"] { /* rules */ }
```

**Attribute selectors**
Applied to any HTML elements with the matching attribute.
```css
a[href="http://google.com/"] { /* rules */ }
```
You can get more flexible with the assignment here eg:
```css
/* Selects all a tags with an href attribute that starts with 'http' */
a[href^="http"] { /* rules */ }

/* Selects all a tags with an href attribute that ends with '.com' */
a[href$=".com/"] { /* rules */ }

/* Selects all a tags with an href attribute that contains the text 'google' */
a[href*="google"] { /* rules */ }
```

**Psuedo Selectors**
Applied to any HTML elements in the matching state.
```css
a:hover { /* rules */ }
```
There are lots of pseudo selectors and [css-tricks](https://css-tricks.com/pseudo-class-selectors/) has a great article running through them all.


### BONUS
**Psuedo Elements**
These look very similar the the pseudo selectors but with two colons [::]
They come in a variety flavours, two very common ones being `::before` and `::after`. Pseudo elements can really help clean up your html and avoid any uneccesary 'hacks' or repetition. They are often overlooked and/or forgotten but it's worth spending a little time to check out what is available. [W3Schools](https://www.w3schools.com/css/css_pseudo_elements.asp) documentation is good for an overview.
```css
.quote::before { content: '"'; }
.quote::after { content: '"'; }
```
