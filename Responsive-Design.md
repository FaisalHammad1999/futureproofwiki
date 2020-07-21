## Responsive vs Fluid
A fluid design is one where the overall layout of a site stays the same but scales based on the device size.
A responsive design also incorporates changes to the layout depending on screen size.

## Normalization
You can keep on top of the currently most used technologies at [StatCounter](https://gs.statcounter.com/) but we should aim for complete cross-browser compatibility. It's not easy but using a pre-set set of rules such as those given in [normalize.css](https://github.com/necolas/normalize.css/) can get you a bit further towards a 'level playing field'. For a full template that aims to conform to some standard conventions, check out [HTMLBoilerplate](https://github.com/h5bp/html5-boilerplate/tree/v7.2.0/dist/css).

## 'Mobile-First'
This is a real buzz-phrase but for good reason. At time of writing, there is a huge amount of media consumption on mobile devices so desigining with mobile in mind as your primary viewing device type is worth putting into practice. Of course, we want it to look good on all devices as much as possible but the idea with 'mobile-first' is that we start with a good looking mobile site and make adjustments to make it desktop-friendly rather than the other way around.

## Media Queries
We can use media queries to make checks on the user's device and adjust our css rules accordingly. We often define breakpoints at which to change the rules.
```css
 article section {
     height: 16.6%;
     width: 100%;
 }

 @media (min-width: 400px) {
    article {
        flex-direction: row;
    }

    article section {
        width: 50%;
        height: 33.3%;
    }
}

@media (min-width: 700px) {
    article section {
        width: 33.33%;
        height: 50%;
    }
} 
```
`min-width` is a common media query but there are many others such as `orientation`, `aspect-ratio`, `hover` and more. For a full run down, check out the [MDN media queries guide](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries)

## Grids
For a long time, developers had to hand-craft grid-type layouts using positioning, floats, clearfixes and it wasn't particulaly approachable. \
**Flexbox**, introduced in 2009, gave an alternative way to approach flexible design. \
**CSS Grid** introduced in 2017, was a welcome offering that brought the traditional grid system up to date with a more semantic, expressive implementation. \
Many **frameworks** such as Bootstrap have their own grid system. Consider this when comparing frameworks as it may be something you want or just an unnecessary extra depending on your preferences.


***

# Resources
[Can I Use](https://caniuse.com/) \
[Normalize via CDN](https://cdnjs.com/libraries/normalize) \
[HTML5Boilerplate](https://html5boilerplate.com/) \
[Media Queries Tester](https://mediaqueriestest.com/) \
[Chrome Dev Tools Device Mode](https://developers.google.com/web/tools/chrome-devtools/device-mode)