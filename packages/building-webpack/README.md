# Webpack

[//]: # (AUTO INSERT HEADER PREPUBLISH)

## Default configuration
We provide default configuration to help you get started building your web component project with webpack.

## Setup

```bash
npm init @open-wc building-webpack
```

## Manual setup
To set up the default configuration manually:

1. Install the required dependencies:
```bash
npm i -D @open-wc/building-webpack webpack webpack-cli webpack-dev-server http-server
```

2. Create a file `webpack.config.js`:
```javascript
const path = require('path');
const defaultConfig = require('@open-wc/building-webpack/default-config');

module.exports = defaultConfig();
```

The default configuration assumes default locations for your app's HTML and JavaScript entrypoints.
Youll add these files in steps 3 and 4. If you're adding webpack to an existing project, you may 
need to change the default entrypoints—see [Changing the default entrypoints](#changing-the-default-entrypoints).


3. Create an `index.html`:
```html
<!doctype html>
<html>
  <head></head>
  <body>
    <your-app></your-app>
  </body>
</html>
```

Note that the `index.html` file doesn't reference the JavaScript file—webpack will add a `<script>` tag when it builds your project. Also note that webpack does **not** process inline script tags, like:

```html
<script>
  import { MyApp } from './src/my-app.js';
</script>
```

4. Create a `index.js` which kickstarts your JS code.
For older browsers we need to load the web component polyfills. We use the [polyfills-loader](https://open-wc.org/building/polyfills-loader.html) for that:

```javascript
import loadPolyfills from '@open-wc/polyfills-loader';

loadPolyfills().then(() => {
  import('./my-app.js');
});
```

5. Add the following commands to your `package.json`:
```json
{
  "scripts": {
    "start": "webpack-dev-server --mode development --open",
    "start:es5": "webpack-dev-server --mode development --es5",
    "start:build": "http-server dist/ -o",
    "build": "webpack --mode production",
    "build:stats": "webpack --mode production --profile --json > bundle-stats.json"
  }
}
```
- `start` runs your app with auto reload for development, it only works on browsers which support modules for faster builds
- `start:es5` runs your app for development, it only works on browsers that don't support modules (IE11)
- `build` builds your app for production and outputs it in the `/dist` folder
- `start:build` runs your built app using a plain web server, to prove it works without magic :)
- `build:stats` creates an analysis report of your app bundle to be consumed by Webpack [Visualizer](https://chrisbateman.github.io/webpack-visualizer/) and [Analyser](https://webpack.github.io/analyse/)

### Changing the default entrypoints

You can change the default `index.html` and `index.js` files:

```javascript
module.exports = defaultConfig({
  indexHTML: path.resolve(__dirname, './src/index.html'),
  indexJS: path.resolve(__dirname, './src/index.js'),
});
```

## Features

### Module resolution
This lets you use bare imports: `import { bar } from 'foo'` instead of `import { bar } from '../node_modules/foo/foo.js'`

### Automatic module type selection
Based on `package.json` it selects the most optimal type of module format. es modules and es2015 code is preferred, but it falls back to support other module types such as CommonJS and UMD. These can be a useful way to get done writing code today, but these are not future proof formats. We recommend using es modules as much as possible.

### HTML, JS and CSS minifications
JS is minified for performance. HTML, CSS and is minified when they are used in combination with the `html` and `css` template tags from `lit-html` and `lit-element`.

### es2015 and es5 output
Using [webpack-babel-multi-target-plugin](https://www.npmjs.com/package/webpack-babel-multi-target-plugin), our build outputs an es5 and es2015 version of your app. Using the `nomodule` trick, we can serve es2015 code on modern browsers and es5 on older browsers (IE11 mostly). This significantly reduces the size of your app on modern browsers.

Read more about this trick at [Jakes blog post "ECMAScript modules in browsers"](https://jakearchibald.com/2017/es-modules-in-browsers/).

### Comment code
Comment code gets stripped at build time. So comment away!  
Note: Licence required comments are extracted and provided via {chunkname}.LICENCE files.

### No regenerator runtime / transform
By default, babel's compilation of async functions and `for of` loops using an expensive transform and runtime. A lot of runtime code is added, and a lot of boilerplate code is added in the transform.

For async functions we use the [fast-async](https://github.com/MatAtBread/fast-async) plugin instead.

### es2015 polyfills
We add [Language polyfills](https://github.com/zloirock/core-js) for `es2015` features, such as `Map` and `'/foo'.startsWith('/')`. But only on the `es5` build, so that we don't need to ship it to modern browsers.

### Syntax and javascript APIs
Our config only supports standard javascript syntax and browser APIs. We support stage 3 proposals when they add significant value and are easy to support without performance penalties.

Supported proposals:
- [Dynamic import](https://github.com/tc39/proposal-dynamic-import)
- [`import.meta.url`](https://github.com/tc39/proposal-import-meta)

## Customizing the configuration
The `default-config` function outputs a regular webpack config, it can easily be extended using `webpack-merge`. For example to add an extra entry file and sass loader:

```javascript
const path = require('path');
const merge = require('webpack-merge');
const createDefaultConfig = require('@open-wc/building-webpack/default-config');

const defaultConfig = createDefaultConfig();

module.exports = merge(defaultConfig, {
  entry: ['additional-entry-file.js'],
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: [
          "style-loader",
          "css-loader",
          "sass-loader"
        ]
      },
    ],
  },
});
```

We recommend extending the config only to make small additions.
If your configuration deviates too much from our default setup, simply copy paste what we have and use it as a starting point.
It will keep your configuration easier to understand.

<script>
  export default {
    mounted() {
      const editLink = document.querySelector('.edit-link a');
      if (editLink) {
        const url = editLink.href;
        editLink.href = url.substr(0, url.indexOf('/master/')) + '/master/packages/building-webpack/README.md';
      }
    }
  }
</script>
