---
layout: default
description: ArangoDB uses a Node.js compatible module system. You can use require() in order to load a module or library.
title: JavaScript Modules available in ArangoDB
---
JavaScript Modules
==================

ArangoDB uses a Node.js compatible module system. You can use the function
`require()` in order to load a module or library. It returns the exported
variables and functions of the module.

The following global variables are available throughout ArangoDB and Foxx:

- `global`
- `process`
- `console`
- `Buffer`
- `__filename`
- `__dirname`

Node compatibility modules
--------------------------

ArangoDB supports a number of modules for compatibility with Node.js, including:

- [**assert**](http://nodejs.org/api/assert.html){:target="_blank"}
  implements basic assertion and testing functions.

- [**buffer**](http://nodejs.org/api/buffer.html){:target="_blank"}
  implements a binary data type for JavaScript.

- [**console**](appendix-java-script-modules-console.html)
  is a well known logging facility to all the JavaScript developers.
  ArangoDB implements most of the [Console API](http://wiki.commonjs.org/wiki/Console){:target="_blank"},
  with the exceptions of *profile* and *count*.

- [**events**](http://nodejs.org/api/events.html){:target="_blank"}
  implements an event emitter.

- [**fs**](appendix-java-script-modules-file-system.html)
  provides a file system API for the manipulation of paths, directories, files,
  links, and the construction of file streams. ArangoDB implements most
  [Filesystem/A](http://wiki.commonjs.org/wiki/Filesystem/A){:target="_blank"}
  functions.

- [**module**](http://nodejs.org/api/modules.html){:target="_blank"}
  provides direct access to the module system.

- [**path**](http://nodejs.org/api/path.html){:target="_blank"}
  implements functions dealing with filenames and paths.

- [**punycode**](http://nodejs.org/api/punycode.html){:target="_blank"}
  implements conversion functions for
  [**punycode**](http://en.wikipedia.org/wiki/Punycode){:target="_blank"} encoding.

- [**querystring**](http://nodejs.org/api/querystring.html){:target="_blank"}
  provides utilities for dealing with query strings.

- [**stream**](http://nodejs.org/api/stream.html){:target="_blank"}
  provides a streaming interface.

- [**string_decoder**](https://nodejs.org/api/string_decoder.html){:target="_blank"}
  implements logic for decoding buffers into strings.

- [**url**](http://nodejs.org/api/url.html){:target="_blank"}
  provides utilities for URL resolution and parsing.

- [**util**](http://nodejs.org/api/util.html){:target="_blank"}
  provides general utility functions like `format` and `inspect`.

Additionally ArangoDB provides partial implementations for the following modules:

- `net`:
  only `isIP`, `isIPv4` and `isIPv6`.

- `process`:
  only `env` and `cwd`;
  stubs for `argv`, `stdout.isTTY`, `stdout.write`, `nextTick`.

- `timers`:
  stubs for `setImmediate`, `setTimeout`, `setInterval`, `clearImmediate`,
  `clearTimeout`, `clearInterval` and `ref`.

- `tty`:
  only `isatty` (always returns `false`).

- `vm`:
  only `runInThisContext`.

The following Node.js modules are not available at all:

- `child_process`
- `cluster`
- `constants`
- `crypto` (but see `@arangodb/crypto` below)
- `dgram`
- `dns`
- `domain`
- `http` (but see `@arangodb/request` below)
- `https`
- `os`
- `sys`
- `tls`
- `v8`
- `zlib`

ArangoDB-specific modules
-------------------------

There are a large number of ArangoDB-specific modules using the `@arangodb`
namespace, mostly for internal use by ArangoDB itself. The following modules
noteworthy however and intended to be used by the user:

- [**@arangodb**](appendix-java-script-modules-arango-db.html)
  provides direct access to the database and its collections.

- [**@arangodb/analyzers**](appendix-java-script-modules-analyzers.html)
  provides an interface to manage ArangoSearch Analyzers.

- AQL related modules:

  - [**@arangodb/aql/queries**](appendix-java-script-modules-queries.html)
    offers methods to track and kill AQL queries.

  - [**@arangodb/aql/cache**](aql/execution-and-performance-query-cache.html)
    allows to control the AQL query caching feature.

  - [**@arangodb/aql/explainer**](aql/execution-and-performance-explaining-queries.html)
    provides methods to debug, explain and profile AQL queries.

  - [**@arangodb/aql/functions**](aql/extending-functions.html)
    provides an interface to (un-)register user-defined AQL functions.

- [**@arangodb/crypto**](appendix-java-script-modules-crypto.html)
  provides various cryptography functions including hashing algorithms.

- [**@arangodb/foxx**](foxx.html)
  is the namespace providing the various building blocks of the Foxx
  microservice framework.

  - [**@arangodb/locals**](foxx-reference-modules.html#the-arangodblocals-module)
    is a helper module to use Foxx together with Webpack.

- Graph related modules:

  - [**@arangodb/general-graph**](graphs-general-graphs.html)
    implements a graph management interface for named graphs.

  - [**@arangodb/smart-graph**](graphs-smart-graphs-management.html)
    provides management features for SmartGraphs

  - [**@arangodb/graph-examples/example-graph.js**](graphs.html#example-graphs)
    can load example graphs (creates collections, populates them with documents
    and creates named graphs)

- [**@arangodb/request**](appendix-java-script-modules-request.html)
  provides the functionality for making synchronous HTTP/HTTPS requests.

- [**@arangodb/tasks**](appendix-java-script-modules-tasks.html)
  implements task management methods

- [**@arangodb/users**](administration-managing-users-in-arangosh.html)
  provides an interface for user management.

- [**@arangodb/pregel**](graphs-pregel.html#javascript-api)
  provides an interface for Pregel management.
