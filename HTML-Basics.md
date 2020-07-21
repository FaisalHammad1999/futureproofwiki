### Pairs of Tags

HTML tags should almost always have be in pairs, an opening and a closing tag with content between them.

```HTML
<h1>This is a heading</h1>
```

The exception to this rule is a self-closing tag, which does not contain content.

```HTML
<br/>
```

### Mandatory Tags

There are four tags which are mandatory on a website, these are:
```HTML
<!DOCTYPE>
<html>

    <head>
    </head>

    <body>
    </body>

</html>

```

### Nesting

We can create tags inside of tags to better organise the content of our HTML, this is know as nesting. 

```HTML
<header>
    <h1>Title of Page<h1>
    <h2>Subtitle of Page<h1>
</header>
```

### Attributes

Attributes enable us to add extra information to tags, such an `#id` or a `.class`, to differentiate between them.

_**It's important to rememebr that IDs are unique and classes are not as they can be used to label a group of tags!**_

```HTML
<div id=newsfeed class=blue-border>
```

An attribute can also be used to provide data such as the `src` of an image tag or the value of an input tag.

```HTML
<img src='image.png' alt='An image'/>
```

### Comments

As developers, a big part of our job is to write readable code. 

One way in which we can achieve this when code gets bloated or confusing is to leave notes to ourselves in the form of comments.

```HTML
<!-- This is our main header -->

<h1>Title</h1>
```

### Whitespace

Another way in which we can make our code readable is to properly indent and leave the appropriate white space.

This is only for our benefit as when the browser reads our code to display back to us, it removes all of the whitespace.

### Googling

Developers spend a lot of time on google searching for syntax, helpful articles, etc.

This isn't a bad thing, developers are not meant to be databases for knowledge, that's why we built the internet. The resources on the web are available for us to pull on as and when we need it. 

_**Being a developer is like one big open book test - so please make use of these resources yourself!**_


## Resources
- [MDN Web Docs: HTML](https://developer.mozilla.org/en-US/docs/Web/HTML)
- [W3 Schools HTML Tutorials](https://www.w3schools.com/html/)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/html) - approach with caution, this is an open forum where some answers may be out of date, bad practice or straight up incorrect. You will get the hang of weeding through, it's worth it!
