These instructions will set up a frontend with:

- Webpack
- Webpack Dev Server
- TypeScript
- React
- Jest

This guide is split into several parts. The idea is that you can pick and
choose the tools you want for your project, but some combinations may still
need additional research/configuration.

# Setup instructions

Run any commands in these instructions from the `frontend/` directory.

## package.json

Create the `package.json` by running this command. It will present a series
of prompts.
```
npm init
```

Choose appropriate values for each of the prompts. If needed, the values can be
edited later by editing `package.json`.

## Webpack

Webpack allows multiple source files to be packaged into a single JS bundle
to send to browsers. It safely separates the global states of each source file
to prevent them from interfering with each other. We will use Webpack to
generate both `index.html` and `bundle.js`.

Install dependencies for Webpack:
```
npm install --save-dev webpack webpack-cli html-webpack-plugin
```

- `webpack`: (I'm not entirely sure why, but this is needed.)
- `webpack-cli`: This will install the `webpack` command that we will use in the
  build script.
- `html-webpack-plugin`: This allows Webpack to generate an HTML page for us
  that properly includes the generated JS bundle.

Add a build script to `package.json`. This will let us build the final frontend
code via Webpack with `npm run build`.
```
{
  ...
  "scripts": {
    ...
    "build": "webpack --mode production"
  },
  ...
}
```

Create a `webpack.config.js` with the following contents:
```js
const HTMLWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: './src/index.tsx', // or maybe .js or .jsx or .ts
  plugins: [
    new HTMLWebpackPlugin({
      title: 'Web Project Starter',
      meta: {viewport: 'width=device-width, initial-scale=1'}
    })
  ],
};
```
- `entry`: Maybe change the extension based on whether you are going to set up
  Typescript and/or React.
- `plugins`:
  - `HTMLWebpackPlugin`: If desired, adjust according to the [plugin
    documentation](https://github.com/jantimon/html-webpack-plugin#options).
      - `title`: Change this.
      - `meta: {viewport: 'width=device-width, initial-scale=1'}`: This adds a
        `<meta>` tag to the `<head>` element that I suppose makes the page
        look big enough on mobile

## Webpack Dev Server

`webpack-dev-server` allows easy incremental development. It will run a server
that refreshes your page any time the code is changed.

Install `webpack-dev-server`:
```
npm install --save-dev webpack-dev-server
```

Add a devserver script to `package.json`. This will let us run a devserver with
`npm run devserver`.
```
{
  ...
  "scripts": {
    ...
    "devserver": "webpack-dev-server -d"
  },
  ...
}
```

The `-d` option causes the dev server to generate source maps, which let tools
map logs and errors encountered when running the compiled bundle to the lines
where they originated in the original source.

If you're going to want to test this frontend with an API server, add the
following to `webpack.config.js`:
```js
module.exports = {
  ...
  devServer: {
    proxy: {
      '/api': 'http://localhost:8081'
    }
  }
}
```
- `devServer.proxy['/api']`: When the devserver receives any requests to a
  path starting with `/api`, it will proxy them to the given address and port.
  This needs to be where the API server is running.

## Typescript

TypeScript converts code written using type annotations and new JS features into
code that is runnable by most browsers. It will tell you if you're making a type
error (which would otherwise only be known at runtime).

Install TypeScript:
```h
npm install --save-dev typescript
```

Create a `tsconfig.json` with the following contents:
```json
{
  "compilerOptions": {
    "strict": true,
    "sourceMap": true,
    "jsx": "react"
  }
}
```
- `strict`: Enable all strict checks in TypeScript.
- `sourceMap`: Allows tools to trace logs and errors found at runtime in
  generated code back to the original source.
- `jsx`: Remove this if you're not using React.

### If using Webpack

(Some of the following instructions were adapted from
[Webpack's Typescript Guide](https://webpack.js.org/guides/typescript/).)

Install `ts-loader`:
```sh
npm install --save-dev ts-loader
```

Add the following to `webpack.config.js`:
```js
  resolve: {
    extensions: [ '.tsx', '.ts', '.js' ],
  },
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
```
- `resolve.extensions`: Allow Webpack to process files with `.ts` and `.tsx`
  extensions. The `.js` is still needed to run the code injected by
  `webpack-dev-server`.
- `modules.rules[0]`: This tells Webpack how to convert `.ts` and `.tsx` files
  into normal JS so that they can be included in the bundle. (I'm not sure why
  the `exclude` key is necessary; Webpack won't include files unless they were
  `import`ed from somewhere anyway.)

### If not using Webpack

WARNING: These instructions are not tested. (Psst... just use Webpack.)

Add this to `tsconfig.json`:
```json
{
  "compilerOptions": {
    ...
    "outDir": "./dist"
  }
}
```

Add a build script to `package.json`. This will let us build the final frontend
code via TypeScript with `npm run build`.
```
{
  ...
  "scripts": {
    ...
    "build": "tsc"
  },
  ...
}
```

## React

React helps make a dynamic web UI that is consistent with the data it presents.

(Some of the following instructions were adapted from
[Creating a React App from Scratch](https://blog.usejournal.com/creating-a-react-app-from-scratch-f3c693b84658).)

Install React:
```sh
npm install --save react react-dom
npm install --save-dev @types/react @types/react-dom  # if using TypeScript
```

WARNING: These instructions assume you're using TypeScript. If not, use `.jsx`
file names and remove any type annotations. Also, find some way to convert
JSX tags into React calls, if you want to use them (Babel + Webpack Babel
loader?).

WARNING: These instructions also assume you're using Webpack. If not, find some
other way to import React, I guess. Also you'll probably have to do this in one
file.

Create `src/index.tsx` with the following contents:
```jsx
import * as React from 'react';
import * as ReactDOM from 'react-dom';
import IndexPage from './components/IndexPage';

const divElem: HTMLElement = document.createElement('div');
document.body.appendChild(divElem);
ReactDOM.render(<IndexPage />, divElem);
```

This will create a `<div>` element in the body and render the `IndexPage` React
component into it.

Create `src/components/IndexPage.tsx` with the following contents:
```jsx
import * as React from 'react';

export default class IndexPage extends React.Component {
  render() {
    return (
      <div>
        Put contents of index page here.
      </div>
    );
  }
}
```

This is the top level component that will be rendered in the `index.html` page.
You can add more React component classes in `src/components`, and import and
use them from here.

## Jest

Jest is a unit testing framework for JS.

Install Jest:
```sh
npm install --save-dev jest
```

Add a test script to `package.json`. This will let you run your tests with the
`npm test` command:
```
{
  ...
  "scripts": {
    ...
    "test": "jest"
  },
  ...
}
```

### If using TypeScript

Install some more stuff:
```sh
npm install --save-dev @types/jest ts-jest
```

Create a `jest.config.js` with the following contents. This allows jest to
handle TypeScript files.
```js
module.exports = {
  "transform": {
    "^.+\\.(ts|tsx)?$": "ts-jest"
  }
}
```

Now you can add unit tests in files ending with `.test.ts`, and they will be run
when you run `npm test`. By my convention,
the test for `foo.ts` is named `foo.test.ts` and placed in the same directory.
