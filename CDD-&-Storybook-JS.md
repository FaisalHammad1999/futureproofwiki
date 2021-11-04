This is a walkthrough for incorporating Component Driven Development into a React project using [StorybookJS](https://storybook.js.org/) which is one of many component explorer options available.

---

- [What is CDD?](#what-is-cdd)
- [Setup](#setup)
- [CSF & MDX](#csf--mdx)
- [Writing a Story](#mdx)
- [Taking it Further](#taking-it-further)
- [Snapshot Testing](#snapshot-testing)

---

### What is CDD?

**C**omponent **D**riven **D**evelopment is a development methodology that focuses the process around individual components rather than the 'big picture' of a app.

Instead of starting with a concept of a page and building components as per needed for the page, CDD allows us to build the components first without necessarily knowing where in a page/app they will sit. This approach really helps to ensure we build reusable, portable components.

Component explorers are tools that are extremely helpful when adopting a CDD approach. A component explorer is a separate application which allows us to visualise components so that they can be constructed in isolation of an app’s ​business logic and layout. They also showcase the components in various test ‘states’​. These different 'states' are also called 'stories', hence the naming of a very popular component explorer, [Storybook JS](https://storybook.js.org/).

---

### Setup

To create a new Storybook, we can run a simple command which will assess the codebase and build a starter storybook based on what it finds. Therefore, the easiest time to set up a Storybook is **after your initial webpack/React setup**.

As well as installing dependencies and creating configuration files, the generator also provides some example stories which can be very useful to help when learning. They are for example only however and the created `stories` folder in `src` can be safely removed if you like.

The generator can be run with:
```zsh
npx sb init --builder webpack5
```

_Note that the `--builder webpack5` flag is provided here as our current guides create a webpack 5 setup. If you are webpakc 4, the `--builder` flag is not required._

The generator also creates a couple of npm scripts so as soon it has completed, you'll be able to open up your brand new Storybook with:
```zsh
npm run storybook
```
By default, Storybook runs on port 6006 so head to `http://localhost:6006` and check out the examples!

***

## CSF & MDX

Two formats that you will come across when using Storybook and other component explorers, are **C**omponent **S**tory **F**ormat and **MDX** (**Mark**D**own combined with JS**X**). Both can be used for both writing stories and writing documentation although overall, CSF is more optimised for writing stories and MDX is more optimised for writing documentation. You can absolutely combine them!

After running the generator, you will find examples of both in the new `/stories` folder.

***

## Writing a Story

Let's look at how we can write some stories in CSF.

### Create a `stories` file

 Your filename will be `<ComponentName>.stories` with an extension of either `.js`, `.jsx` or `.mdx`. If you'll be using CSF, the `.js` or `.jsx` extension is appropriate. If using MDX, you'll need to use the `.mdx` extension

The location of your file is up to you although with the idea of a component being independent, I might recommend that it lives in your component's folder along with eg. a test file.

For example, the location of stories for a `Card` component may look something like this:

```
|-- src
   |-- components
       |-- Button
       |-- Card
           |-- index.jsx
           |-- Card.stories.jsx
           |-- test.js
           |-- style.css
       |-- NavBar
       |-- index.js
```


### Import what you need

We'll need to use some React for this and the component we're writing stories for so we'll import those

```jsx
// Card.stories.jsx

import React from 'react';
import { Card } from '.';
```

### Create a Template for your stories

We'll set some default rules for our component story and make a basic template version of the component. From this template we will be able to  create stories with different options.

```jsx
// Card.stories.jsx

export default {
  title: 'Card',
  component: Card,
};

const Template = (args) => <Card {...args} />;
```

### Define a Story

From the Template, we can create stories with different arguments. Let's say that our Card component may come in a Small size or a Large size. We can make a story for each of those variations.

```jsx
// Card.stories.js

export const Small = Template.bind({});
Small.args = {
  size: 'small',
  title: 'A Small Story',
  author: 'Gingertonic'
};

export const Large = Template.bind({});
Large.args = {
  size: 'large',
  title: 'A Large Story',
  author: 'A. Non'
};
```

Refresh your Storybook in the browser and have a look at what has been added!

---

## Taking it further

This has just scratched the surface of what is possible with Storybook. For a deeper dive, check out the official [tutorials](https://storybook.js.org/tutorials/) and [documentation](https://storybook.js.org/docs/react/get-started/introduction).


---

## Snapshot Testing

Snapshot testing is a type of testing that compares the current output of eg. a component, to a previous one and flags any changes. This is great for when our components are looking good and we want to make sure there are no unexpected changes! If a change is flagged that you actually intended to make, that's okay, you can tell your snapshots to update to reflect the new version.

Storybook gives us a very easy way to setup snapshot testing alongside Jest.

If you have an existing Jest setup, you can have snapshot tests up and running in just a few short steps!
- Install dependencies: `npm i -D @storybook/addon-storyshots react-test-renderer`
- Create a storyshots test file (location could be eg. in `src` folder): `touch storyshots.test.js`
- Add the code below to the storyshots.test.js file
- Run your test script eg. `npm test`

```js
// storyshots.test.js

import initStoryshots from '@storybook/addon-storyshots';
initStoryshots();
```