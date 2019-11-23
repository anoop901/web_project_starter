These instructions will set up a backend with the following features:

- TypeScript
- Express
- Nodemon
- Jest

This guide is split into several parts. The idea is that you can pick and
choose the tools you want for your project, but some combinations may still
need additional research/configuration.

# Setup instructions

Run any commands in these instructions from the `api/` directory.

## package.json

Create the `package.json` by running this command. It will present a series
of prompts.
```sh
npm init
```

Choose appropriate values for each of the prompts. If needed, the values can be
edited later by editing `package.json`.

## TypeScript

TypeScript converts code written using type annotations and new JS features into
code that is runnable by most browsers. It will tell you if you're making a type
error (which would otherwise only be known at runtime).

Install TypeScript:
```sh
npm install --save-dev typescript
```

Add build and start scripts to `package.json`. This will let us build the final
API server code via TypeScript with `npm run build`, and run it with
`npm start`.
```
{
  ...
  "scripts": {
    ...
    "build": "tsc",
    "start": "node dist/main.js"
  },
  ...
}
```

Create a `tsconfig.json` with the following contents:
```json
{
  "compilerOptions": {
    "strict": true,
    "sourceMap": true,
    "outDir": "./dist"
  }
}
```
- `strict`: Enable all strict checks in TypeScript.
- `sourceMap`: Allows tools to trace logs and errors found at runtime in
  generated code back to the original source.
- `outDir`: Directory where generated files go.

Now you can start coding in `src/main.ts`.

## Express

Install Express dependencies:
```sh
npm install --save express
npm install --save-dev @types/express  # if using TypeScript
```

Create a `src/main.ts` with the following contents. This initializes Express
with a router that handles requests beginning with `/api` to be defined in
`src/router.ts`. 
```ts
import * as express from 'express';
import router from './router';

const app = express();
const port = process.env.PORT || 8081;

app.use('/api', router);
app.use(express.static("../frontend/dist")); // if serving a frontend

app.listen(port, () => {
  console.log(`Listening on port ${port}.`);
});
```

Create a `src/router.ts` with the following contents. This is where all the
route definitions will go.
```ts
import * as express from 'express';

const router = express.Router();

// Put routes here, e.g.
router.get('/hello', (req, res) => {
  res.json({hello: "world"});
});

export default router;
```

## Nodemon

Nodemon automatically restarts the server when it detects changes in the input
files. This is useful during incremental development.

Install some dependencies:
```sh
npm install --save-dev nodemon
npm install --save-dev ts-node  # if using TypeScript
```

Add some scripts to package.json. These will let us run commands like `npm run
build` and `npm start`.
```json
{
  ...
  "scripts": {
    ...
    "devserver": "nodemon src/main.ts"
  }
  ...
}
```

Create a `nodemon.json` with the following contents:
```json
{
  "execMap": {
    "ts": "ts-node"
  },
  "ignore": ["*.test.ts"],
  "watch": ["src"]
}
```
- `execMap.ts`: Needed if using TypeScript. `ts-node` executes TypeScript files
  directly, instead of converting to JS first.
- `ignore`: This prevents restarting if the only change was to a test file.
- `watch`: The directory to watch for changing files.

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

