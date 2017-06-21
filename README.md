# Publication Collector

[![CircleCI](https://img.shields.io/circleci/project/johanbrook/meteor-publication-collector.svg?maxAge=2592000)]()

This package makes testing publications in Meteor easier and nicer.

Instead of resorting to exporting or exposing your publication functions for doing testing, this package lets you "subscribe" to a given publication and assert its returned results.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Publication Collector](#publication-collector)
  - [Installation](#installation)
  - [Usage](#usage)
    - [PublicationCollector](#publicationcollector)
    - [PublicationCollector.collect](#publicationcollectorcollect)
  - [Development](#development)
    - [Tests](#tests)
  - [History](#history)
  - [Releases](#releases)
  - [To do](#to-do)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Installation

```
meteor add johanbrook:publication-collector
```

## Usage

This package is server-only and can't be imported on the client.

```js
// server/myPublication.test.js

import { PublicationCollector } from 'meteor/johanbrook:publication-collector';

describe('myPublication', function() {
  it('should publish 10 documents', function(done) {
    const collector = new PublicationCollector({ userId: Random.id() });

    collector.collect('myPublication', firstPublicationArg, secondPublicationArg, (collections) => {
      assert.equal(collections.myCollection.length, 10);
      done();
    });
  });
});
```

### PublicationCollector

```js
const collector = new PublicationCollector(opts);
```

`opts` may have the following attributes:
- `userId`: Add a `this.userId` to the publication's context
- `delayInMs`: By default, `collect` callbacks are called when the publication is ready. If you use this option, the callbacks will be called `delayInMs` milliseconds after the publication is ready.

An instance of `PublicationCollector` also is an `EventEmitter`, and emits a `ready` event when the publication is marked as ready.

### PublicationCollector.collect

```js
collector.collect(publicationName, publicationArgs..., callback);
```

- `publicationName (String)`:  the name of the publication (String)
- `publicationArgs`: zero or more arguments to the publication
- `callback (Function)`: The function to be called when the publication is ready. The function will be provided with a `collections` object containing key:value pairs where the key is the name of a collection that the publication published and the value is an array of the documents that were published in that collection.

```js
collector.collect('myPublication', firstPublicationArg, secondPublicationArg, (collections) => {
  assert.equal(collections.myCollection.length, 10);
});
```

## Development

```
npm install
```

Follow `.eslintrc`

### Tests

Run tests once with

```
npm test
```

Run tests in watch mode (in console) with

```
npm run test:dev
```

## History

This project was originally a part of MDG's [Todos](https://github.com/meteor/todos) example Meteor app, but later extracted as a separate test package.

Based on https://github.com/stubailo/meteor-rest/blob/devel/packages/rest/http-subscription.js.

## Releases

- `1.0.7` - Fix compatibility with `peerlibrary:reactive-publish`'s `_isDeactivated` function in publications ([#20](https://github.com/johanbrook/meteor-publication-collector/pull/23), thanks @jaskinn!).
- `1.0.6` - Fix an issue with "ready" event being emitted more than once ([#16](https://github.com/johanbrook/meteor-publication-collector/pull/16)). Thanks @nkahnfr!
- `1.0.5` - Fix an issue when publish handlers are using default arguments ([#15](https://github.com/johanbrook/meteor-publication-collector/pull/15)). Thanks @dmihal!
- `1.0.4` - Don't try to operate on a document that doesn't exist in `changed` callback. Thanks @zenweasel, from [#13](https://github.com/johanbrook/meteor-publication-collector/pull/13)!
- `1.0.3` - Fix compatibility with `peerlibrary:reactive-publish` package (bug #3), fixed in [#10](https://github.com/johanbrook/meteor-publication-collector/pull/10). Thanks [@hexsprite](https://github.com/hexsprite)!
- `1.0.2` - Fix bug where `ready()` wasn't called if there were no results from a publication handler.
- `1.0.1`
  - Fixes inconsistent results from publication collector (thanks @PhilippSpo in [#2](https://github.com/johanbrook/meteor-publication-collector/issues/2)).
  - Return an empty array if there are no returned documents from a publication ([#5](https://github.com/johanbrook/meteor-publication-collector/issues/5)).
  - Accept `Mongo.ObjectID` as `_id` attribute ([#8](https://github.com/johanbrook/meteor-publication-collector/issues/8)).
- `1.0.0` - First public release.

## To do

- [x] Make tests pass.
- [x] More docs.
- [ ] Support Promises.
