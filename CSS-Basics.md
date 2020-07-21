### C is for Cascading
The 'Cascading' of 'Cascading Style Sheets' describes the drip-down effect used to find the styling for elements. You can have multiple sheets over multiple files and multiple sources but the cascading effect remains the same: If two rules collide, the most recent will take precedence (consider this in the order adding links to your sheets and any internal styling in your `<head>` tag) but specificity always win!

### Anatomy
```css
selector {
  property: value;
  property: value;
}
```

### Get specific
Rules can range from extremely general eg `*` (selects everything!) to extremely specific eg `a#thingy::hover` (selects the `a` tag with the id of "thingy" when the user hovers over the link)
The more specific, the more 'importantance' the rule has when it comes to deciding what rules are overwritten during the cascade.
For a full detailed view on specificity see [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity) and for a quick way to 'calculate' specificity, tools such as [Polypane](https://polypane.app/css-specificity-calculator/) can be fun to experiment with.

### id & class
```html
  <p class="odds">One</p>
  <p id="even">Two</p>
  <p class="odds">Three</p>
  <a class="odds">Link Me</a>
```
```css
.odds { color: blue;  } /* One, Three and Link Me will have blue text */
#even { font-family: monospace } /* 'Two' will be in monospace */
```
Your `id` attributes should be unique. If you want to use the same identifier on several elements, use `class`
`id` attributes are caught with the `#` selector. Class attributes are selected with the `.`

### Where to put my CSS
#### External Sheets
The most common placement is in a separate `.css` file. To load the file on your page, link to it in the `head` tag of your html. \
`<link href="style.css" rel="stylesheet" type="text/css" />`

#### Internal Sheet
If you'd like to keep your css closer, you can declare it directly in the `<head>` tag of your html, wrapped in a `<style>` tag. Whilst this is okay for short selections and trying things out, it can lead to very bloated, untidy code so use with caution!
```html
<head>
  <style>
    #info {
      font-family: monospace;
    }
  </style>
</head>
```

#### Inline Styling
Quick, dirty and not to be overused!
```html
<h1 style="color:darkolivegreen;text-align:center;">Hello Jackson</h1>
```

### Comments
```css
/* This is a comment */
```
Remember that in most text editors, using [cmd]-[/] or [ctrl]-[/] will comment out any highlighted lines, regardless of the language you are working in.

***

# Resources
[css-tricks](https://css-tricks.com/) - a fantastic blog and guides site on all things CSS.\
[W3School CSS](https://www.w3schools.com/css/default.asp)\
[MDN CSS docs](https://developer.mozilla.org/en-US/docs/Web/CSS)\
[CSS Diner](https://flukeout.github.io/) - a minigame for traning selector-crafting
