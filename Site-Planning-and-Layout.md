_Designing websites is hard! That's why there are professional designers!_
_As developers we may have the luxury of working with a designer but may also be fulfilling both roles. Either way, we definately need to be able to execute the proposed designs._

***

## Design
### Inspiration
To get inspired for your own designs and to practice taking other's designs to implementation, sites such as [Dribbble](https://dribbble.com/) are great resources where designs are shared.

### Design Tools
When creating your own designs, a napkin and pen are a perfect starting point and when you are ready to get digital prototypes there are lots of tools. A nice basic, free wireframing option is [wireframe.cc](https://wireframe.cc/). For a wider range of tools including interactive prototyping [Figma](https://www.figma.com/) is an extremely popular and excellent tool. It has a great free tier that will be perfect for most smaller projects.

***

## Execution
CSS is a much more powerful language than it is often given credit for and you could spend years learning all there is to know! Here are some of the key properties that you will likely be using on a regular basis.

### display
By default, most elements have a display property of block. Some, such as `span` and `a` are inline. We can change this manually for any element with the `display` property. 
```css
p { display: block|inline|inline-block }
```
**block** level elements will by default will have a new line after it, and will take up the as much width and --height as it requires. Although the height and width can be manually changed/controlled \
**inline** elements the width and height cannot be set, but there is no new line afterwards - hence the name in-line. \
There is the option of setting display to **inline-block** - allowing us to set the width and height but removing the new line that follows a block element.

### position
**static** \
Every element has a static position by default, which fills the space that it needs. However, if there is a left/right/top/bottom/z-index set then there will be no effect on that element. \
**relative** \
A relatively positioned element’s original position remains in the flow of the document, takes up the space it needs but we can adjust the position on the page using the top, bottom, left and right properties. \
**absolute** \ 
An absolutely positioned element is removed from the flow of the document and other elements will behave as if it’s not even there whilst all the other positional properties will work on it. in this case top, bottom, left and right adjust the position of the element from where it’s parent is place.

[Display & Position repl.it](https://repl.it/@bethschofield/DisplayAndPosition)

### box-sizing
A short, sweet solution to an otherwise potentially frustrating issue (I use it to avoid extra maths!).
By default, adding a border to an element will make it larger.
```css
div {
  width: 100px;
  border: 2px solid gold;
}
```
This div will actually be 104px wide thanks to the additional 2px on each side added by the border. Whilst this is okay if you account for it in your layout calculations, might it not be easier to have a stated width be the actual width, border or no border? \
With one CSS rule you can make that the case: `box-sizing: border-box`.
Many developers will add this as a wildcard (apply to everything) as part of their flow.
```css
* { box-sizing: border-box; }
```

### flexbox
In 2009 [flexbox](https://www.w3schools.com/css/css3_flexbox.asp) was released and gave an easier way to create with flexible, responsive layouts, avoiding the floats and clear-fix - filled past!
[CSS Tricks](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) have excellent documentation on flexbox usage. Get started with `display: flex`. Some of your most-used flexbox properties are going to be `flex-direction`, `flex-wrap` and `flex-grow`. [Flexbox Froggy](https://flexboxfroggy.com/) is a great training tool that I recommend re-visiting on a regular basis!

### css-grid
Much more recently, in 2017, CSS Grid came out and was met with much enthusiasm! Grid allows us to be extremely expressive in our layout approach with properties such as `grid-template-areas`. Get started with `display: grid` and set your rows and columns with `grid-template-rows` and `grid-template-columns`. From the same team that made Froggy, the [Grid Garden](https://cssgridgarden.com/) is their excellent tool for learning Grid.

***

## BONUS: Some CSS extras
### Variables
Variables allow us to store data with easy-to-remember names. In CSS we declare variables with `--thisthing: mydata`. The other huge benefit of using variables is that we can change the value in one place (the declaration) and immediately see the change everywhere we have referenced that variable. What a time saver! '
A very common use case would be to store your colour palette values in names that make sense to you. You can declare them anywhere but the convention is to do this in the `:root` pseudo-class.
```css
:root {
    --accent: #a1c9d0;
    --highlight: #6eacb7;
    --warning: #e2b7b9;
}
```

To use your variables elsewhere, you can access them with `var(--varname)`.
```css
header { color: var(--accent); }
```
There are a plethora of colour palette sites out there to assist in colour theory and matching. At time of writing, the author is a fan of [Colormind](http://colormind.io/)

### Numeric Values & Calculation
Most CSS layout tasks are going to require a little bit of maths. Fortunately, css can do calculations for us! This is most useful when we are depending on a dynamic value to make a calculation (if it is a static value, do the maths yourself and hard-code it in to save processing time). CSS uses a variety of units and a full run down can be found on [MDN](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Values_and_units).

Some of the most common ones are `vh` & `vw`, `em` & `rem`, `%` & `px`.

To make a calculation you can mix and match all of these and more. How handy!
```css
section { height: calc(100vh - 50px - 1.5rem) }
```
***

# Resources
[MDN guide to CSS Units](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Values_and_units) \
[Colormind](http://colormind.io/) - a handy colour palette picker \
[css-tricks guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) \
[css-tricks guide to Grid](https://css-tricks.com/snippets/css/complete-guide-grid/)
