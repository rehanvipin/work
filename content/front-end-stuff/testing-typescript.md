---
title: "Testing Typescript"
weight: 3
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---
# It's sort of a pain
Especially when there's DOM stuff and ESM involved.

You would think that in 2022, it would be easy to write tests for your
library that uses TS, DOM manipulation, and ESM.

Haha, no.

## 1. Make a new tsconfig file
Yeah, cuz es6 is not 7 years old apparently.  
Something like this (in `tsconfig.callitwhatyouwant.json`)
```json
{
    "compilerOptions": {
        "outDir": "./dist/",
        "module": "commonjs", // required for tests
        "target": "es5",
        "strict": true,
        "sourceMap": true,
        "jsx": "react",
        "lib": [
            "es2015",
            "es2016",
            "es2017",
            "dom"
        ],
    },
    "include": ["src/**/*.ts", "./test/**/*.ts"]
}
```
remove stuff at your own risk. (But do try, this is somewhat of a mess)

## 2. Install stuff
I'm using Karma and Jasmine cuz they're nice.
```
npm i -D karma karma-chrome-launcher karma-spec-reporter
npm i -D typescript karma-typescipt karma-typescript-es6-transform
npm i -D jasmine-core @types/jasmine karma-jasmine
```

## 3. Setup the karma config file
this should do
```js
// karma.conf.js
module.exports = function (config) {
    config.set({
        frameworks: ["jasmine", "karma-typescript"],
        files: [
            "src/**/*.ts",
            "test/**/*.ts"
        ],
        preprocessors: {
            "**/*.ts": "karma-typescript"
        },
        reporters: ["progress", "karma-typescript"],
        browsers: ["Chrome"],
        karmaTypescriptConfig: {
            tsconfig: 'tsconfig.callitwhatyouwant.json',
            bundlerOptions: {
                transforms: [
                    require("karma-typescript-es6-transform")()
                ]
            }
        },
        singleRun: true
    });
};
```

## 4. Setup the package json
Nothing really.
```
"scripts": {
    "test": "karma start",
}
```
That's it. Nothing else is required in that file.

## 5. Write code & tests
Write them under `src/` and `test/` respectively.