# Canvas API (for TypeScript and JavaScript)

```shell
npm i @kth/canvas-api
```

Node.JS HTTP client (for both TypeScript and JavaScript) based on [got](https://github.com/sindresorhus/got) for the [Canvas LMS API](https://canvas.instructure.com/doc/api/)

## Getting Started

First, generate a token by going to `«YOUR CANVAS INSTANCE»/profile/settings`. For example https://canvas.kth.se/profile/settings. Then you can do something like:

```js
import CanvasApi from "@kth/canvas-api.ts";

const canvas = new CanvasApi(
  "https://canvas.kth.se/api/v1",
  "«YOUR TOKEN HERE»"
);
const { body } = await canvas.get("accounts/1");

console.log(body);
```

## Concepts

### SIS Imports

This package implements one function to perform SIS Imports (i.e. call the [POST sis_imports] endpoint).

> Note: this is the only function that calls a **specific** endpoint. For other endpoints you should use `canvas.get`, `canvas.requestUrl`, `canvas.listItems` and `canvas.listPages`

```ts

```

[post sis_imports]: https://canvas.instructure.com/doc/api/sis_imports.html#method.sis_imports_api.create

### `listItems` and `listPages`

This package does have pagination support which is offered in two methods: `listItems` and `listPages`. Let's see an example by using the [GET /accounts/1/courses] endpoint.

If you want to get all **pages** you can use `listPages`:

```js
const pages = canvas.listPages("accounts/1/courses");

// Now `pages` is an iterator that goes through every page
for await (const courses of pages) {
  // `courses` is an array of courses in one page
  for (const course of courses) {
    console.log(course.id);
  }
}
```

To avoid writing two `for` loops like above, you can call `listItems`, that iterates elements instead of pages. The following code does exactly the same as before:

```js
const courses = canvas.listItems("accounts/1/courses");

// Now `courses` is an iterator that goes through every course
for await (const course of courses) {
  console.log(course.id);
}
```

[get /accounts/1/courses]: https://canvas.instructure.com/doc/api/accounts.html#method.accounts.courses_api

### Typescript support

This package does not contain type definitions to the objects returned by Canvas. If you want such types, you need to define such types yourself and pass it as type parameter to the methods in this library.

For example, to get typed "account" objects:

```ts
// First you define the "Account" type (or interface)
// following the Canvas API docs: https://canvas.instructure.com/doc/api/accounts.html
interface CanvasAccount {
  id: number;
  name: string;
  workflow_state: string;
}

// Then, you can call our methods by passing your custom type as type parameter
const { body } = await canvas.get<CanvasAccount>("accounts/1");

console.log(body);
```

### Error handling

// TODO

## API Reference

## Design philosophy

1. **Do not implement every endpoint**. This package does **not** implement every endpoint in Canvas API This package also does not implement type definitions for objects returned by any endpoint nor definition for parameters. That would make it unmaintainable.

   Exception: an endpoint is called exactly with the same parameters all the time.

2. **Offer "lower-level" API** instead of trying to implement every possible feature, expose the "internals" to make it easy to extend.

   Example: you can use `.client` to get the `Got` instance that is used internally. With such object, you have access to all options given by the library [got](https://github.com/sindresorhus/got)

#
