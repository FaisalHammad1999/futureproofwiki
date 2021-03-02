[`watchify`](https://www.npmjs.com/package/watchify) allows us to use require in client-side JavaScript, bundling it all up into one file, as with [`browserify`](https://www.npmjs.com/package/browserify). It will also automatically update the bundle when changes are detected in the working files.

Adding the use of [`concurrently`](https://www.npmjs.com/package/concurrently) offers a cross-platform solution to create an npm script to both start `watchify` and start an http server in the same command.

## Prerequisites
- Python 3 installed for python's [http.server](https://docs.python.org/3/library/http.server.html) but any alternative can be used if preferred
- npm installed

## Setup
- `npm init -y`
- `npm install -D watchify concurrently`

```json
// in package.json
"scripts": {
    "dev": "concurrently \"watchify ./index.js -o bundle.js\" \"python -m http.server\""
}
// Update `index.js` to your entry point as required
// Update bundle location as desired
```

```html
<!-- in index.html -->
<script defer src="bundle.js"><script>
<!-- Update bundle location as required -->
```

## Run dev server
`npm run dev`