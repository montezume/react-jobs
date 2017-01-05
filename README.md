## ___WIP___

This library is in a complete state of flux at the moment.  I haven't done any releases, not even an alpha.  I am evolving the API through a test process.  This message will disappear after it settles enough for an alpha release.  Until then I wouldn't pay too much attention to this library. :)

Todo:

 - [X] job declaration API
 - [ ] SSR helper
 - [ ] docs
 - [ ] examples

---

# react-jobs 🕴

Attach sync/async "jobs" to your components, with SSR support.

[![npm](https://img.shields.io/npm/v/react-jobs.svg?style=flat-square)](http://npm.im/react-jobs)
[![MIT License](https://img.shields.io/npm/l/react-jobs.svg?style=flat-square)](http://opensource.org/licenses/MIT)
[![Travis](https://img.shields.io/travis/ctrlplusb/react-jobs.svg?style=flat-square)](https://travis-ci.org/ctrlplusb/react-jobs)
[![Codecov](https://img.shields.io/codecov/c/github/ctrlplusb/react-jobs.svg?style=flat-square)](https://codecov.io/github/ctrlplusb/react-jobs)

## TOCs

  - [Introduction](#introduction)
  - [Installation](#installation)
  - [Usage](#usage)
    - [Server Side Rendering](#server-side-rendering)
  - [Examples](#examples)
    - [Simple Async Example](#simple-async-example)
    - [Simple Async Example with Caching](#simple-async-example-with-caching)
    - [Redux Example](#redux-example)
  - [API](#api)
    - [JobsProvider](#jobsprovider)
    - [job](#job)
    - [runJobs](#runJobs)
  - [FAQs](#faqs)

## Introduction

This library takes heavy inspiration from the amazing [`react-apollo`](https://github.com/apollostack/react-apollo) library to provide you with a generic mechanism of attaching "jobs" to your React Components.

You can use these "jobs" to do any of the following:
 - Resolve additional data for your components in a sync/async manner.
 - Execute a sync/async side effect when your component renders, also letting your component know when an async side effect has completed.
 - ?

It provides you with a simple `function` API which allows you to easily provide mechanisms like data caching or integrations with 3rd party libraries (e.g. Redux).

We additionally provide you with a mechanism to process asynchronous jobs when doing server rendering (i.e. within SSR/universal/isomorphic apps).

## Installation

__npm__

```bash
npm i react-jobs -S
```

__yarn__

```bash
yarn add react-jobs
```

## Usage

To get started you need to import our `job` function.

```js
import { job } from 'react-jobs';
```

TODO...

### Server Side Rendering

TODO...

## Examples

Below are a few example use cases on how to use this library.

### Simple Async Example

In the naive example below we will use the `fetch` API (you'll need to [polyfill it](https://github.com/github/fetch) for older browsers) to retrieve data from an API endpoint.

```js
import React from 'react';
import { job } from 'react-jobs';
import Product from './components/Product';

function Products({ job, categoryID }) {
  // We provide a "job" prop containing the status/results of
  // the work you attached to your component.

  if (job.inProgress) {
    // If the work is still being processed (i.e. the Promise
    // is not yet resolved) then the `job.inProgress` boolean flag
    // value will be set to `true`.
    return <div>Retrieving...</div>;
  }

  if (job.error) {
    // If an error occurred whilst trying to execute your work
    // then the `job.error` will contain the error.
    return <div>Oh noes!</div>;
  }

  // Yay! We have the results!
  return (
    <div>
      {
        job.results.map(product =>
          <Product key={product.id} item={product} />
        )}
    </div>
  );
}

export default job(
  // Provide a function defining the work that needs to be done.
  // This function will be provided the props passed to your
  // component and must return a Promise for any asynchronous work.
  function work(props) {
    // Fetch the products for the given `categoryID` prop,
    return fetch(`/products/category/${props.categoryID}`)
      // then convert the response to JSON.
      .then(response => response.json());
  }
)(Products);
```

This component can then be used like so:

```jsx
<Products categoryID={1} />
```

### Simple Async Example with Caching

In the below example we implement a very simple caching strategy within our "job" declaration.  Using a caching strategy you get the following benefits:

 - Reduce unneeded network requests.
 - Immediate render if the results exist within cache.

```js
import React from 'react';
import { job } from 'react-jobs';
import Product from './components/Product';

function Products({ job, categoryID }) {
  // We provide a "job" prop containing the status/results of
  // the work you attached to your component.

  if (job.inProgress) {
    // This will only be true when you return a Promise from
    // your work function below.  So when returning the
    // cache result inProgress will be `false`.
    return <div>Retrieving...</div>;
  }

  if (job.error) {
    return <div>Oh noes!</div>;
  }

  // Yay! We have the results!
  // This could have come from our cache or from the
  // Promise resolution.
  return (
    <div>
      {
        job.results.map(product =>
          <Product key={product.id} item={product} />
        )}
    </div>
  );
}

// We will use a simple variable to hold our results.
let jobResultCache = null;

export default job(
  function work(props) {
    if (jobResultCache) {
      // Our cache is populated, so we will simply return
      // the result.
      return jobResultCache;
    }

    // Fetch the products for the given `categoryID` prop,
    return fetch(`/products/category/${props.categoryID}`)
      // then convert the response to JSON, store it in
      // the cache, and return.
      .then((response) => {
        const result = response.json();
        jobResultCache = result;
        return result;
      });
  }
)(Products);
```

This component can then be used like so:

```jsx
<Products categoryID={1} />
```

This is of course a naive example, but it simply serves to illustrate how easy it is to cache your requests.  You could write your own helper functions to encapsulate some of this behaviour and extend it with other behaviours such as time based cache destruction.

### Redux Example

TODO

## API

### `JobsProvider`

TODO

### `jobs`

TODO

### `runJobs`

TODO

## FAQs

> Let me know your questions...
