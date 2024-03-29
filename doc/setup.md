# Project setup

If you don't want to clone the package from GitHub or to download it from npm, you can follow the below step-by-step
walkthrough to build the template from scratch yourself.
Some configurations are specific to the IDE I use (IntelliJ) and should be adapted to yours. Beside the optional
configuration of SonarLint, the whole process should take less than 30 minutes to complete.

The OS used is Windows 10, the console is PowerShell and the IDE is IntelliJ.
NodeJS version is 19.x and NPM version is 9.x.

1. [Initialise a NodeJs project](#initialise-a-nodejs-project)
2. [Set up TypeScript](#set-up-typescript)
3. [Set up Linter and code formatter for TypeScript](#set-up-linter-and-code-formatter-for-typescript)
4. [Set up the unit test framework for TypeScript](#set-up-the-unit-test-framework-for-typescript)
5. [Set up the functional test framework for TypeScript](#set-up-the-functional-test-framework-for-typescript)
6. [Set up the mocking framework for TypeScript](#set-up-the-mocking-framework-for-typescript)
7. [Configure the module path mapper](#configure-the-module-path-mapper)

## Initialise a NodeJs project

The goal is the structure project is to facilitate the implementation of the "clean" hexagonal architecture and, by
consequence, the implementation of the DDD principles and white box automated testing.

1. Create a new project from scratch in Webstorm and select NodeJS as the initial template.
2. Create the `/package.json` file with this minimal configuration:

       {
         "name": "",
         "version": "",
         "description": "",
         "keywords": ["", ""],
         "homepage": "https://github.com/...",
         "repository": {
           "type": "git",
           "url": "https://github.com/...",
         },
         "bugs": {
         "url": "https://github.com/.../issues",
         "email": "xxx@gmail.com"
         },
         "license": "",
         "author": "xxx <xxx@gmail.com>",
         "private": true
       }

3. [OPTIONAL] Create a `/README.md` file.
4. Create the below repository structure

       /bin                  - script, helpers, binaries
       /config               - global configurations
       /dist                 - released code (to be pushed to BitBurner)  
       /doc
         /coverage
         /cucumber-report
         /jest-report
       /src  
         /module-X           - an idenpendent module as intented by the "clean" hexagonal architecture
           /config           - module configuration
           /domain  
             /entity   
             /port
             /use-case
           /feature           - place for the feature files
           /infra  
             /driven-side  
             /driver-side
           /test              - place for the test files (.steps.ts, .test.ts)

   To do so, run the following command

       mkdir bin, config, dist, doc/coverage, doc/cucumber-report, doc/jest-report, src/module-X/config, 
       src/module-X/domain/entity, src/module-X/domain/port, src/module-X/domain/use-case, src/module-X/feature,
       src/module-X/infra/driven-side, src/module-X/infra/driver-side, src/module-X/test,

5. Create the `/.gitignore` file with the following content:

       /.idea/
       /node_modules/
       /dist/
       /doc/coverage/
       /doc/cucumber-report/
       /doc/jest-report/

## Set up TypeScript

1. Install the TypeScript and the definitelyTyped type definition for Node to support Implicit, Explicit and Ambient
   types.

       npm install --save-dev typescript @types/node ts-node

2. Create the `/tsconfig.json` file with this minimal configuration:

       {
         "include": ["src/**/*.ts"],
         "exclude": ["node_modules", "src/**/*.test.ts", "src/**/*.steps.ts"],
         "compilerOptions": { /* https://www.typescriptlang.org/tsconfig */
         
           /* Modules */
           "rootDir": "./src",                           /* Specify the root folder within your source files. */
           "module": "ESNext",                           /* Specify what module code is generated. */
           "moduleResolution": "node",                   /* Specify how TypeScript looks up a file from a given module specifier. */
           "lib": ["ES2020"],
           "resolveJsonModule": true,                    /* Enable importing .json files. */
           "allowSyntheticDefaultImports": true,
           "resolveJsonModule": true,                    /* Enable importing .json files. */

           /* Emit */
           "outDir": "./dist",                           /* Specify an output folder for all emitted files. */
           "sourceMap": true,                            /* Create source map files for emitted JavaScript files. */
         
           /* Interop Constraints */
           "esModuleInterop": true,                      /* Emit additional JavaScript to ease support for importing CommonJS modules. This enables 'allowSyntheticDefaultImports' for type compatibility. */
           "forceConsistentCasingInFileNames": true,     /* Ensure that casing is correct in imports. */
         
           /* JavaScript Support */
           "allowJs": true,                              /* Allow JavaScript files to be a part of your program. Use the 'checkJS' option to get errors from these files. */
         
           /* Language and Environment */
           "target": "es2022",                           /* Set the JavaScript language version for emitted JavaScript and include compatible library declarations. */
           "experimentalDecorators": true,               /* Enable experimental support for TC39 stage 2 draft decorators. */
         
           /* Type Checking */
           "strict": true,                               /* Enable all strict type-checking options. */
           "alwaysStrict": true,                         /* Ensure 'use strict' is always emitted. */
           "noImplicitAny": true,                        /* Enable error reporting for expressions and declarations with an implied 'any' type. */
           "noImplicitOverride": true,
           "noImplicitReturns": true
         }
       }

3. In `/package.json`, define a typescript transpilation command by adding

       "scripts": {
          "build": "tsc --build --clean"
       }

4. [OPTIONAL] Configure IDE to transpile on change.

## Set up linter and code formatter for TypeScript

For this part, I have chosen tools that are the most used by the development community (2023). The most commonly used
linter for TypeScript is ESLint. Even if ESLint is able to perform some code formatting checks,
[Prettier](https://prettier.io/) does a better job and has better reviews. In order to use standard linting
configuration, I have
[chosen](https://devstephen.medium.com/style-guides-for-linting-ecmascript-2015-eslint-common-google-airbnb-6c25fd3dff0)
to use the [AirBnB ESling configuration](https://www.npmjs.com/package/eslint-config-airbnb)
and [typescript-eslint](https://github.com/typescript-eslint/typescript-eslint/tree/main/packages/eslint-plugin).

StandardJS [recommends using ts-standard](https://standardjs.com/#can-i-use-a-javascript-language-variant-like-flow-or-typescript)
for TypeScript project. Since this standard is a lot less used than StandardJS
and suffers the same flaws as StandardJS, we won't consider it.

Ref.: [Prettier, ESLint and Typescript](https://dev.to/viniciuskneves/prettier-eslint-and-typescript-491j)  
[Using ESLint and Prettier with Typescript](https://robertcooper.me/post/using-eslint-and-prettier-in-a-typescript-project)  
[ESLint with StandardJs and Prettier](https://medium.com/nerd-for-tech/setting-up-eslint-with-standard-and-prettier-be245cb9fc64)  
[ESLint, Prettier and Typescript in VSCode ](https://khalilstemmler.com/blogs/tooling/prettier/)  
[Webstorm - Eslint](https://www.jetbrains.com/help/webstorm/eslint.html)  
[Webstorm - Prettier](https://www.jetbrains.com/help/webstorm/prettier.html)

1. Install Prettier and ESLint with Airbnb configuration
   `eslint-plugin-import` is required by `airbnb-base` (cf. airbnb-base documentation).

       npm install --save-dev prettier eslint eslint-config-airbnb-base eslint-plugin-import

   To set up airbnb we need to extend the default ESLint configuration. Create `/config/.eslintrc.json`, with the
   following elements:

       {
         "extends": [
           "airbnb-base",
         ]
       }

2. Set up ESLint and Prettier to work together

   Since Prettier and ESLint are trying to make code formatting but with different rule sets, we need to deactivate the
   ESLint code formatting rules to let Prettier do its job. In such configuration, Prettier is the only one responsible
   for code formatting and ESLint for linting. To avoid having to run Prettier and then ESLint, it is possible to add
   Prettier rules to ESLint so calling ESLint will check linting (with ESLint rules) and code formatting checks (with
   Prettier rules). Prettier provides [2 packages](https://prettier.io/docs/en/integrating-with-linters.html) exactly
   for that.

   First, turn off ESLint rules that conflicts with Prettier ones.

       npm install --save-dev eslint-config-prettier

   Second, add Prettier rules to ESLint

       npm install --save-dev eslint-plugin-prettier

   Now we need to extend ESLint configuration with prettier rules: in `/config/.eslintrc.json` modify the ESLint extends
   section like that:

       {
         "extends": [
           "airbnb-base",
           "plugin:prettier/recommended"
         ]
       }

   The order of the extensions is important since the last ones will override the first ones. Here we specifically
   request prettier rules to override eslint-airbnb configuration.

   In Webstorm, don't forget to deactivate the IDE option that "Reformat on action" (CTRL+ALT+L) based on Prettier since
   it will only use Prettier rules and not the ESLint + Prettier setup configured above. Instead, you should go to the
   `/config/.eslintrc.json` file, make a right click and select "Apply ESLint Code Style rules". By doing so, the IDE
   will ensure those are the rules checked automatically and applied when asking for an autoformatting of the code.

3. Set up ESLint and TypeScript to work together

   Ref.: [Webstorm - Linting Typescript](https://www.jetbrains.com/help/webstorm/linting-typescript.html)

   First install a package that enable the parsing of TypeScript files by ESLint

       npm install --save-dev @typescript-eslint/parser

   And then configure ESLint to use that parser and parse only TypeScript files in the project. In
   `/config/.eslintrc.json`, adapt as followed:

       {
         "parser": "@typescript-eslint/parser",
         "settings": {
           "import/resolver": {
             "node": {
               "extensions": [".ts"]
             }
           }
         }
       }

   For linting rules specific to TypeScript language, we can use the default ones proposed
   by [typescript-eslint](https://github.com/typescript-eslint/typescript-eslint/tree/main/packages/eslint-plugin).

       npm install --save-dev @typescript-eslint/eslint-plugin

   And then adapt the ESLint configuration (again, be careful on the typescript-eslint plugin position in the extends
   section):

       {
         "extends": [
           "plugin:@typescript-eslint/recommended",
           "airbnb-base",
           "plugin:prettier/recommended"
         ]
       }

   Finally, make minor changes to the ESLint/Prettier rules to:
    * remove the no-console error
    * remove ethe import error
    * force prettier to use single quotes rather than double quotes.

          {
            "rules": {
              "no-console": "off",
              "import/extensions": "off",
              "prettier/prettier": ["error", {"singleQuote": true}]
            }
          }

4. Configure basic script commands in `package.json`

       "scripts": {
         "lint": "eslint ./**/*.ts",
         "fix": "eslint ./**/*.ts --fix",
         "precompile": "",
         "compile": "tsc --build --clean"
       }

5. [OPTIONAL] Set up SonarLint in IDE

   SonarLint is also a linter and code formatter but is integrated directly into the IDE through plugins. Most rules are
   duplicates from the ones from ESLint or Prettier. There are some that are complementary. That is why I decided to add
   SonarLint as an additional security. To avoid errors being caught multiple times and overcrowding the error logs, I
   have deactivated all SonarLint rules that are already checked by ESLint or Prettier.

## Set up the unit test framework for TypeScript

As for the previous choices, I will use the most commonly used and most simple to set up unit test framework. At the
time of this project, it is Jest.

Ref.:

* [Test framework comparison 1](https://raygun.com/blog/javascript-unit-testing-frameworks/)
* [Test framework comparison 2](https://www.testim.io/blog/best-unit-testing-framework-for-javascript/)

1. Install Jest

       npm install --save-dev jest @types/jest ts-jest

   ts-jest ensures the transpilation on the fly of TypeScript tests files to JavaScript files to make them compatible
   for jest. Thanks to ts-jest, we don't need to transpile our TypeScript tests files and store the output somewhere.

   `@types/jest` is one way to have Jest globals available for our tests files. The main advantage of this module is
   that it doesn't require you to explicitly import Jest methods in the test files. The disadvantage is that this module
   is a third party library not maintained by the Jest project. It is maintained at
   [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/jest) which provides some
   guaranty of quality, but, nonetheless, it is not part of the Jest project, which generates a dependency risk.
   Therefore, as recommended by [Jest documentation](https://jestjs.io/docs/getting-started#type-definitions), try to
   match jest and @types/jest versions as closely as possible to mitigate retro-compatibility issues.

   The other way to handle Jest globals, is to avoid @types/jest and, instead, install @jest/globals (maintained by
   Jest project). Then, you need to explicitly import Jest methods in all your test files:

       import {describe, expect, test} from '@jest/globals';

2. Configure Jest by creating a `/config/jest.config.ts`

       export default {
         preset: 'ts-jest',
         testEnvironment: 'node',
         rootDir: '../',
         verbose: true,
         coverageDirectory: './doc/coverage/',
         coverageThreshold: {
           global: {
             branches: 80,
             functions: 80,
             lines: 80,
             statements: -10
           }
         }
       }
   In this configuration, I made the choice to make the test fails if the global test coverage is below 80%.

3. Add test associated script commands in `package.json`

       "scripts": {
         "pretest": "",
         "unit": "jest --config=\"./config/jest.config.ts\" --coverage .*.test.ts",
         "posttest": "",
       }

4. In `/config/.eslintrc.json`, adapt ESLint configuration to be compatible with jest and avoid false positives from the
   linter:

       {
         "env": {
           "jest": true
         }
       }

5. Change the behaviour of ESLint to avoid false positives on missing imports with typescript files.

       npm install --save-dev eslint-import-resolver-typescript

   Adapt the ESLint configuration:

       {
         "settings": {
           "import/resolver": {
             "typescript": {},
             "node": {
               "extensions": [
                 ".ts"
               ]
             }
           }
         }
       }

## Set up the functional test framework for TypeScript

1. Ensure you have a version of NodeJs that supports ESM loader (even in experimental mode). In my case, NodeJS version
   19.7.0 works.

2. Install cucumberJs

       npm install --save-dev @cucumber/cucumber ts-node

3. Configure cucumber
   By creating the following `cucumber.json` file

       {
         "default": {
           "paths": ["src/**/*.feature"],
           "import": ["src/**/*.steps.ts"],
           "parallel": 1,
           "format": ["html:test-reports/cucumber/cucumber-report.html"],
           "publishQuiet": true
         }
       }

4. Modify the `package.json` file to add the following commands:

       "cucumber": "SET NODE_OPTIONS=--loader ts-node/esm --no-warnings --experimental-specifier-resolution=node --enable-source-maps && cucumber-js --config=\"./config/cucumber.json\"",

   This command will generate the js files needed by CucumberJs on the fly with ts-node, avoiding polluting the project.

5. Create your `*.feature` files in Gherkin and your glue code in `*.steps.ts` files.

6. Modify the `tsconfig.json` file to avoid compiling the `.steps.ts` and `.test.ts` files while building the
   distribution:

       "exclude": ["node_modules", "src/**/*.test.ts", "src/**/*.steps.ts"],

Ref.:

* [Cucumber with TypeScript](https://www.elliotdenolf.com/blog/cucumberjs-with-typescript)

## Set up the mocking framework for TypeScript

Contrary to Jest.mock that need an object instance to create a mock from, ts-mockito can create mocks directly from
interfaces. This is the main reason why I chose to use ts-mockito over jest.mock.

    npm install --save-dev ts-mockito

## Configure the module path mapper

This configuration is very specific to my original project constraints. This is not a best practice and should be
avoided in most cases.

1. Configure Jest and TypeScript to use absolute paths in their import patterns, rather than relative paths
   In `tsconfig.json`,

       "compilerOptions": {
         "baseUrl": ".",
         "paths": { "/*": ["src/*"] }
       }

2. Jest in not aware of the paths mapper defined in `tsconfig.json`. We need to define a similar rule in the jest
   configuration file:

       {
         moduleNameMapper: {
           '^/(.*)$': '<rootDir>/src/$1',
         }
       }

3. Alas, ESM loader doesn't support path mapping. We need to change the loader for a custom one:

         npm install --save-dev ts-paths-esm-loader

   Then, in `package.json`,

         "cucumber": "node --loader ts-paths-esm-loader --no-warnings --experimental-specifier-resolution=node --enable-source-maps node_modules/@cucumber/cucumber/bin/cucumber.js --config=\"./config/cucumber.json\"",

   In the cucumber script, I purposely use the full path of cucumber.js module since the shortcut cucumber-js generates
   an error because the shortcut file has no extension. Beside renaming the shortcut (which won't work easily in a
   CI/CD) workflow, I don't see any other solution at the moment.

## Finalize the template by creating commands for the final build in `package.json`

    "scripts": {
       "lint": "eslint --config=\"./config/.eslintrc.json\" ./**/*.ts",
       "lintTest": "eslint ./**/*.ts",
       "fix": "eslint --config=\"./config/.eslintrc.json\" ./**/*.ts --fix",
       "pretest": "",
       "unit": "jest --config=\"./config/jest.config.ts\" --coverage .*.test.ts",
       "cucumber": "node --loader ts-paths-esm-loader --no-warnings --experimental-specifier-resolution=node --enable-source-maps node_modules/@cucumber/cucumber/bin/cucumber.js --config=\"./config/cucumber.json\"",
       "test": "npm run unit && npm run cucumber",
       "posttest": "",
       "precompile": "npm run test",
       "clean": "mkdir dist && rmdir /s /q dist && mkdir dist",
       "build": "tsc --build --clean",
       "compile": "npm run build"
    }