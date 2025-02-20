---
layout: default
description: Features listed in this section should no longer be used, because they are considered obsolete and may get removed in a future release
title: Deprecated Features
redirect_from:
  - http/export.html # 3.8 -> 3.9
  - appendix-deprecated-simple-queries.html # 3.8 -> 3.8
  - appendix-deprecated-simple-queries-pagination.html # 3.8 -> 3.8
  - appendix-deprecated-simple-queries-modification-queries.html # 3.8 -> 3.8
  - appendix-deprecated-simple-queries-geo-queries.html # 3.8 -> 3.8
  - appendix-deprecated-simple-queries-fulltext-queries.html # 3.8 -> 3.8
  - http/simple-query.html # 3.8 -> 3.8
  - programs-arangod-compaction.html # 3.9 -> 3.9
  - administration-leader-follower-database-setup.html # 3.8 -> 3.9
  - administration-leader-follower-initialize-from-backup.html # 3.8 -> 3.9
  - administration-leader-follower-replication-applier.html # 3.8 -> 3.9
  - administration-leader-follower-server-level-setup.html # 3.8 -> 3.9
  - administration-leader-follower-setting-up.html # 3.8 -> 3.9
  - administration-leader-follower-syncing-collections.html # 3.8 -> 3.9
  - administration-leader-follower.html # 3.8 -> 3.9
  - architecture-deployment-modes-leader-follower-architecture.html # 3.8 -> 3.9
  - architecture-deployment-modes-leader-follower-limitations.html # 3.8 -> 3.9
  - architecture-deployment-modes-leader-follower.html # 3.8 -> 3.9
  - deployment-leader-follower-manual-start.html # 3.8 -> 3.9
  - deployment-leader-follower.html # 3.8 -> 3.9
---
Deprecated
==========

Features listed in this section should no longer be used, because they are
considered obsolete and may get removed in a future release. They are currently
kept for backward compatibility. There are usually better alternatives to
replace the old features with:

- **Leader/Follower Deployment Mode**:
  The Leader/Follower deployment type is deprecated and already removed from
  documentation. Active Failover and OneShard databases in clusters are better
  alternatives.

- **Skiplist and hash indexes**:
  Skiplist and hash indexes have been deprecated in 3.9 and will be removed in a 
  future version of ArangoDB. Currently, they are an alias for a
  [persistent index](http/indexes.html#persistent-index).

- **Bundled NPM modules**:
  The [bundled NPM modules](appendix-java-script-modules.html#bundled-npm-modules)
  have been deprecated in 3.9 and will be removed in a future version of ArangoDB.
  If you want to use NPM modules in your Foxx service, please refer to the
  [Foxx guide](foxx-guides-bundled-node-modules.html).

- **Batch Requests API**:
  The [batch request REST API](http/batch-request.html) is deprecated and will be 
  removed in a future version. Instead of using this API, please use the 
  [HTTP Interface for Documents](http/document-working-with-documents.html#bulk-document-operations)
  that can insert, update, replace or remove arrays of documents.

- **PUT method in Cursor API**:
  The HTTP endpoint `PUT /_api/cursor/<cursor-id>` in the
  [Cursor REST API](http/aql-query-cursor.html) is deprecated and will be
  removed in a future version. Please use the drop-in replacement
  `POST /_api/cursor/<cursor-id>` instead. The POST endpoint is functionally
  equivalent to the PUT endpoint, but does not violate idempotency requirements
  prescribed by the [HTTP specification](https://tools.ietf.org/html/rfc7231#section-4.2){:target="_blank"}.

- **Simple Queries**: Idiomatic interface in arangosh to perform trivial queries.
  They are superseded by [AQL queries](aql/index.html), which can also
  be run in arangosh. AQL is a language on its own and way more powerful than
  *Simple Queries* could ever be. In fact, the (still supported) *Simple Queries*
  are translated internally to AQL, then the AQL query is optimized and run
  against the database in recent versions, because of better performance and
  reduced maintenance complexity.

- **Accessing collections by ID instead of by name**:
  Accessing collections by their internal ID instead of accessing them by name
  is deprecated and highly discouraged. This functionality may be removed in
  future versions of ArangoDB.

- **Old metrics REST API**:
  The old metrics API under `/_admin/metrics` is deprecated and replaced by
  a new one under `/_admin/metrics/v2` from version 3.8.0 on. This step was
  necessary because the old API did not follow quite a few Prometheus
  guidelines for metrics.

- **Statistics REST API**:
  The endpoints `/_admin/statistics` and `/_admin/statistics-description`
  are deprecated in favor of the new metrics API under `/_admin/metrics/v2`.
  The metrics API provides a lot more information than the statistics API, so
  it is much more useful.

- **Replication logger-follow REST API**:
  The endpoint `/_api/replication/logger-follow` is deprecated since 3.4.0 and
  may be removed in a future version. Client applications should use the REST 
  API endpoint `/_api/wal/tail` instead, which is available since ArangoDB 3.3.

- **Loading and unloading of collections**:
  The JavaScript functions for explicitly loading and unloading collections,
  `db.<collection-name>.load()` and `db.<collection-name>.unload()` and their
  REST API endpoints `PUT /_api/collection/<collection-name>/load` and
  `PUT /_api/collection/<collection-name>/unload` are deprecated in 3.8.
  There should be no need to explicitly load or unload a collection with the
  RocksDB storage engine. The load/unload functionality was useful only with
  the MMFiles storage engine, which is not available anymore since 3.7.

- **Actions**: Snippets of JavaScript code on the server-side for minimal
  custom endpoints. Since the Foxx revamp in 3.0, it became really easy to
  write [Foxx Microservices](foxx.html), which allow you to define
  custom endpoints even with complex business logic.

  From v3.5.0 on, the system collections `_routing` and `_modules` are not
  created anymore when the `_system` database is first created (blank new data
  folder). They are not actively removed, they remain on upgrade or backup
  restoration from previous versions.

- **Outdated AQL functions**: The following AQL functions are deprecated and
  their usage is discouraged:
  - `IS_IN_POLYGON`
  - `NEAR`
  - `WITHIN`
  - `WITHIN_RECTANGLE`

  See [Geo functions](aql/functions-geo.html) for substitutes.

- **`bfs` option** in AQL graph traversal: Using the *bfs* attribute inside
  traversal options is deprecated since v3.8.0. The preferred way to start a
  breadth-first traversal is by using the new `order` attribute, and setting it
  to a value of `bfs`.

- **`overwrite` option**: The `overwrite` option for insert operations (either
  single document operations or AQL `INSERT` operations) is deprecated in favor
  of the `overwriteMode` option, which provides more flexibility.

- **`minReplicationFactor` collection option**: The `minReplicationFactor`
  option for collections has been renamed to `writeConcern`. If
  `minReplicationFactor` is specified and no `writeConcern` is set, the
  `minReplicationFactor` value will still be picked up and used as
  `writeConcern` value. However, this compatibility mode will be removed
  eventually, so changing applications from using `minReplicationFactor` to
  `writeConcern` is advised.

- **Outdated startup options**

  The following _arangod_ startup options are deprecated and will be removed
  in a future version:
  - `--database.old-system-collections` (no need to use it anymore)
  - `--server.jwt-secret` (use `--server.jwt-secret-keyfile`) 
  - `--arangosearch.threads` / `--arangosearch.threads-limit`
    (use the following options instead):
    - `--arangosearch.commit-threads`
    - `--arangosearch.commit-threads-idle`
    - `--arangosearch.consolidation-threads`
    - `--arangosearch.consolidation-threads-idle`
  - `--rocksdb.exclusive-writes` (was intended only as a stopgap measure to
    make porting applications from MMFiles to RocksDB easier)
  - `--http.allow-method-override`: this option allows incoming HTTP POST 
    request to override the actual HTTP method used by setting one of the
    special HTTP headers `x-http-method`, `x-method-override` or 
    `x-http-method-override`. This was originally intended for very restricted
    callers, which only supported HTTP GET and HTTP POST, but seems very
    unnecessary nowadways.
  - `--http.hide-product-header`: whether or not to hide the `Server: ArangoDB`
    header in all responses served by arangod.
  - `--network.protocol`: network protocol to use for cluster-internal 
    communication. The protocol will be auto-decided from version 3.9 onwards.
  - `--query.allow-collections-in-expressions`: allow full collections to be 
    used in AQL expressions. This option defaults to `false` from version 3.9
    onwards and will be removed in a future version. It is only useful to 
    enable it when migrating from older versions.

  The following options are deprecated for _arangorestore_:
  - `--default-number-of-shards` (use `--number-of-shards` instead)
  - `--default-replication-factor` (use `--replication-factor` instead)

  The following startup options are deprecated in _arangod_ and all client tools:
  - `--log` (use `--log.level` instead)
  - `--log.use-local-time` (use `--log.time-format` instead)
  - `--log.use-microtime` (use `--log.time-format` instead)
  - `--log.performance` (use `--log.level` instead)

- **Obsoleted startup options**: Any startup options marked as obsolete can be
  removed in any future version of ArangoDB, so their usage is highly
  discouraged. Their functionality is already removed, but they still exist to
  prevent unknown startup option errors.

- **arangobench test cases**: arangobench provides several test cases that are
  marked as deprecated. These test cases were originally written for internal
  testing and do not provide much value for end users. Therefore they will be
  removed in a future version of arangobench. Whenever a deprecated arangobench
  test case is invoked, there will be a warning message.

- **arangoimp** executable: ArangoDB release packages install an executable named
  _arangoimp_ as an alias for the _arangoimport_ executable. This is done to 
  provide compatibility with older releases, in which _arangoimport_ did not
  yet exist and was named _arangoimp_. The renaming was actually carried out in
  the codebase in December 2017. Using the _arangoimp_ executable is deprecated,
  and it is always favorable to use _arangoimport_ instead. 
  While the _arangoimport_ executable will remain, the _arangoimp_ alias will be 
  removed in a future version of ArangoDB.

- **HTTP and JavaScript traversal APIs**: The [HTTP traversal API](http/traversal.html)
  is deprecated since version 3.4.0. The JavaScript traversal module
  `@arangodb/graph/traversal` is also deprecated since then. The preferred way
  to traverse graphs is via AQL.

- **JavaScript-based AQL graph functions**: The following JavaScript-based AQL
  graph functions are deprecated:
  - `arangodb::GRAPH_EDGES`
  - `arangodb::GRAPH_VERTICES`
  - `arangodb::GRAPH_NEIGHBORS`
  - `arangodb::GRAPH_COMMON_NEIGHBORS`
  - `arangodb::GRAPH_COMMON_PROPERTIES`
  - `arangodb::GRAPH_PATHS`
  - `arangodb::GRAPH_SHORTEST_PATH`
  - `arangodb::GRAPH_DISTANCE_TO`
  - `arangodb::GRAPH_ABSOLUTE_ECCENTRICITY`
  - `arangodb::GRAPH_ECCENTRICITY`
  - `arangodb::GRAPH_ABSOLUTE_CLOSENESS`
  - `arangodb::GRAPH_CLOSENESS`
  - `arangodb::GRAPH_ABSOLUTE_BETWEENNESS`
  - `arangodb::GRAPH_BETWEENNESS`
  - `arangodb::GRAPH_RADIUS`
  - `arangodb::GRAPH_DIAMETER`
