---
title: "Typescript"
weight: 2
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---
# Write JavaScript with confidence
TODO: The docs are good. Check them out: https://www.typescriptlang.org/docs/.

## Some notes
`declare a` in TS is used to tell the compiler that the variable a is declared somewhere else and it's
usage in this file should not throw an error.
(e.g. it's declared in another file and both of them are used via script tags in HTML)

Any definitions in a class declaration are moved to the constructor when the code is transpiled to JS.
The code is also moved to the top of the constructor so that the constructor code can use the defined values.
e.g.
{{< columns >}}
### TS
```ts
class LOL {
    private a: number;
    private b: string = 'bob';
    private c = 'wow this is cool' + this.b;
    constructor() {
        this.a = 1;
        this.b = 'wow' + this.c;
    }
    public z = 'bongo cats';
}
```
<--->
### Compiled JS
```js
class LOL {
    constructor() {
        this.b = 'bob';
        this.c = 'wow this is cool' + this.b;
        this.z = 'bongo cats';
        this.a = 1;
        this.b = 'wow' + this.c;
    }
}
```
{{< /columns >}}

## Testing
It can be somewhat painful.

A really good series about JavaScript testing (specifically about Jasmine and some parts about Karma) - <https://www.youtube.com/playlist?list=PL_euSNU_eLbcpJdoM-WWzUlNNVM4TwtMl>

### 1. Make a new tsconfig file
Aside from the tsconfig file that you're already using for your source code.
Something like this (in `tsconfig.callitwhatyouwant.json`)
```json
{
    "compilerOptions": {
        "module": "commonjs", // required for tests
        "target": "es5",
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

### 2. Install stuff
I'm using Karma and Jasmine cuz they're nice.
```
npm i -D karma karma-chrome-launcher karma-spec-reporter
npm i -D typescript karma-typescipt karma-typescript-es6-transform
npm i -D jasmine-core @types/jasmine karma-jasmine
```

### 3. Setup the karma config file
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

### 4. Setup the package json
Nothing really.
```
"scripts": {
    "test": "karma start",
}
```
That's it. Nothing else is required in that file.

### 5. Write code & tests
Write them under `src/` and `test/` respectively.