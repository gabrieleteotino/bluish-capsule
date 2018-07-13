---
title: "Nodejs typescript simple server"
date: 2018-07-12T11:53:21.859+02:00
subtitle: "A simple demo of an api server running on node written in typescript"
author: Gabriele Teotino
tags: ["node", "typescript", "npm"]
categories: ["dev"]
draft: true
---

Install *typescript* globally

```shell
sudo npm install -g typescript
```

<!--more-->

The basic inpiration for this article is from [risingstack](https://blog.risingstack.com/building-a-node-js-app-with-typescript-tutorial/)

## Create the project

Create a folder and initialize a new project.

```shell
mkdir ts-server-demo
cd ts-server-demo
npm init
```

Add typescript locally

```shell
npm install typescript --save-dev
```

Add the dependency for [expressjs](https://expressjs.com) and the types definitions.

```shell
npm install express --save
npm install @types/express --save-dev
```

Add

```shell
npm install @types/mocha --save-dev
```

Create a folder for the typescript source code

```shell
mkdir src
```

## Implement the application

Create the application **src/app.ts**

```typescript

```

Create the entry point **src/index.ts**

```typescript

```

That's it. A minimalistic hello world.

# Transpile to javascript

Create in the root a [tsconfig.json](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html) file.

```json
{
  "compilerOptions": {
    "target": "es6",
    "module": "commonjs",
    "outDir": "dist",
    "sourceMap": true
  },
  "files": [
    "./node_modules/@types/mocha/index.d.ts",
    "./node_modules/@types/node/index.d.ts"
  ],
  "include": [
    "src/**/*.ts"
  ],
  "exclude": [
    "node_modules"
  ]
}
```

Compile

```shell
tsc
```

#######TODO configure task for tsc

## Add a linter

### ESLint

Install [ESLint](https://github.com/eslint/typescript-eslint-parser) ([user guide](https://eslint.org/docs/user-guide/getting-started)) and the [eslint typescript plugin](https://github.com/nzakas/eslint-plugin-typescript)

```shell
npm install eslint --save-dev
npm install typescript-eslint-parser --save-dev
npm install eslint-plugin-typescript --save-dev

./node_modules/.bin/eslint --init
```

TODO

### TSLint

Install [TSLint](https://palantir.github.io/tslint/)

npm install tslint typescript --save-dev
tslint --init

Check the rules (if using visual studio code this is not necessary)

```shell
tslint 'src/**/*.ts'
```

The sample code for **app* and **index**

## Testing

Testing your application

tsc && mocha dist/**/*.spec.js

## Transpile and run the application

```shell
tsc
node dist
```

## Build a docker image

```
FROM risingstack/alpine:3.4-v6.9.4-4.2.0

ENV PORT 3001

EXPOSE 3001

COPY package.json package.json
RUN npm install

COPY . .
RUN npm run build

CMD ["node", "dist/"]
```
