# TypeScript with NodeJS
TypeScript has had *first class* support for NodeJS since inception. Here's how to setup a quick NodeJS project:

> Note: many of these steps are actually just common practice nodejs setup steps

1. Setup a nodejs project `package.json`. Quick one : `npm init -y`
1. Add TypeScript (`npm install typescript --save-dev`)
1. Add `node.d.ts` (`npm install @types/node --save-dev`)
1. Init a `tsconfig.json` for TypeScript options (`node ./node_modules/typescript/lib/tsc --init`)

That's it! Fire up your IDE (e.g. `alm -o`) and play around. Now you can use all the built in node modules (e.g. `import fs = require('fs');`) with all the safety and developer ergonomics of TypeScript!

## Bonus: Live compile + run
* Add `ts-node` which we will use for live compile + run in node (`npm install ts-node --save-dev`)
* Add `nodemon` which will invoke `ts-node` whenever a file is changed (`npm install nodemon --save-dev`)

Let's explicitly tell `ts-node` where to look for types by editing `tsconfig.json`:

```json
{
  "compilerOptions": {
    ...
    "typeRoots": ["node_modules/@types"]
  }
}
```

Now just add a `script` target to your `package.json` based on your application entry e.g. assuming its `index.ts`:

```json
  "scripts": {
    "start": "npm run build:live",
    "build:live": "nodemon --exec ./node_modules/.bin/ts-node -- ./index.ts"
  },
```

So you can now run `npm start` and as you edit `index.ts`:

* nodemon reruns its command (ts-node)
* ts-node transpiles automatically picking up tsconfig.json and the installed typescript version,
* ts-node runs the output javascript through node.

## Creating TypeScript node modules

Using modules written in TypeScript is super fun as you get great compile time safety and autocomplete (essentially executable documentation).

Creating a high quality TypeScript module is simple. Assume the following desired folder structure for your package:

```
package
|_ package.json
|_ tsconfig.json
|_ src
  |_ All your source files
  |_ index.ts
  |_ foo.ts
  |_ ...
|_ lib
  |_ All your compiled files
  |_ index.d.ts
  |_ index.js
  |_ foo.d.ts
  |_ foo.js
  |_ ...
```


* In your `tsconfig.json`
  * have `compilerOptions`: `"outDir": "lib"` and `"declaration": true` < This generates declaration and js files in the lib folder
  * Include all the files from `src` < This includes all the files from the src dir.

* In your `package.json` have
  * `"main": "lib/index"` < This tells NodeJS to load `lib/index.js`
  * `"types": "lib/index"` < This tells TypeScript to load `lib/index.d.ts`


Example package:
* `npm install typestyle` [for TypeStyle](https://www.npmjs.com/package/typestyle)
* Usage: `import { style } from 'typestyle';` will be completely type safe.

MORE:

* If you package depends on other TypeScript authored packages, put them in `dependencies`/`devDependencies`/`peerDependencies` just like you would with raw JS packages.
* If you package depends on other JavaScript authored packages and you want to use it type safely in your project, put their types e.g. `@types/foo` in `devDependencies`. JavaScript types should be managed *out of bound* from the main NPM streams. The JavaScript ecosystem breaks types without semantic versioning too commonly, so if your users need types for these they should install the `@types/foo` version that works for them.

## Bonus points

Such NPM modules work just fine with browserify (using tsify) or webpack (using ts-loader).
