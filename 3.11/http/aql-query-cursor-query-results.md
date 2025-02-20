---
layout: default
description: Select queries are executed on-the-fly on the server and the resultset will be returned back to the client
---
Retrieving query results
========================

Select queries are executed on-the-fly on the server and the result
set will be returned back to the client.

There are two ways the client can get the result set from the server:

* In a single roundtrip
* Using a cursor

Single roundtrip
----------------

The server will only transfer a certain number of result documents back to the
client in one roundtrip. This number is controllable by the client by setting
the `batchSize` attribute when issuing the query.

If the complete result can be transferred to the client in one go, the client
does not need to issue any further request. The client can check whether it has
retrieved the complete result set by checking the `hasMore` attribute of the
result set. If it is set to `false`, then the client has fetched the complete
result set from the server. In this case no server side cursor will be created.

```js
> curl --data @- -X POST --dump - http://localhost:8529/_api/cursor
{ "query" : "FOR u IN users LIMIT 2 RETURN u", "count" : true, "batchSize" : 2 }

HTTP/1.1 201 Created
Content-type: application/json

{
  "hasMore" : false,
  "error" : false,
  "result" : [
    {
      "name" : "user1",
      "_rev" : "210304551",
      "_key" : "210304551",
      "_id" : "users/210304551"
    },
    {
      "name" : "user2",
      "_rev" : "210304552",
      "_key" : "210304552",
      "_id" : "users/210304552"
    }
  ],
  "code" : 201,
  "count" : 2
}
```

Using a cursor
--------------

If the result set contains more documents than should be transferred in a single
roundtrip (i.e. as set via the `batchSize` attribute), the server will return
the first few documents and create a temporary cursor. The cursor identifier
will also be returned to the client. The server will put the cursor identifier
in the `id` attribute of the response object. Furthermore, the `hasMore`
attribute of the response object will be set to `true`. This is an indication
for the client that there are additional results to fetch from the server.

**Examples**

Create and extract first batch:

```js
> curl --data @- -X POST --dump - http://localhost:8529/_api/cursor
{ "query" : "FOR u IN users LIMIT 5 RETURN u", "count" : true, "batchSize" : 2 }

HTTP/1.1 201 Created
Content-type: application/json

{
  "hasMore" : true,
  "error" : false,
  "id" : "26011191",
  "result" : [
    {
      "name" : "user1",
      "_rev" : "258801191",
      "_key" : "258801191",
      "_id" : "users/258801191"
    },
    {
      "name" : "user2",
      "_rev" : "258801192",
      "_key" : "258801192",
      "_id" : "users/258801192"
    }
  ],
  "code" : 201,
  "count" : 5
}
```

Extract next batch, still have more:

```js
> curl -X POST --dump - http://localhost:8529/_api/cursor/26011191

HTTP/1.1 200 OK
Content-type: application/json

{
  "hasMore" : true,
  "error" : false,
  "id" : "26011191",
  "result": [
    {
      "name" : "user3",
      "_rev" : "258801193",
      "_key" : "258801193",
      "_id" : "users/258801193"
    },
    {
      "name" : "user4",
      "_rev" : "258801194",
      "_key" : "258801194",
      "_id" : "users/258801194"
    }
  ],
  "code" : 200,
  "count" : 5
}
```

Extract next batch, done:

```js
> curl -X POST --dump - http://localhost:8529/_api/cursor/26011191

HTTP/1.1 200 OK
Content-type: application/json

{
  "hasMore" : false,
  "error" : false,
  "result" : [
    {
      "name" : "user5",
      "_rev" : "258801195",
      "_key" : "258801195",
      "_id" : "users/258801195"
    }
  ],
  "code" : 200,
  "count" : 5
}
```

Do not do this because `hasMore` now has a value of false:

```js
> curl -X POST --dump - http://localhost:8529/_api/cursor/26011191

HTTP/1.1 404 Not Found
Content-type: application/json

{
  "errorNum": 1600,
  "errorMessage": "cursor not found: disposed or unknown cursor",
  "error": true,
  "code": 404
}
```

If the `allowRetry` query option is set to `true`, then the response object
contains a `nextBatchId` attribute, except for the last batch (if `hasMore` is
`false`). If retrieving a result batch fails because of a connection issue, you
can ask for that batch again using the `POST /_api/cursor/<cursor-id>/<batch-id>`
endpoint. The first batch has an ID of `1` and the value is incremented by 1
with every batch. Every result response except the last one also includes a
`nextBatchId` attribute, indicating the ID of the batch after the current.
You can remember and use this batch ID should retrieving the next batch fail.

```js
> curl --data @- -X POST --dump - http://localhost:8529/_api/cursor
{ "query": "FOR i IN 1..5 RETURN i", "batchSize": 2, "options": { "allowRetry": true } }

HTTP/1.1 201 Created
Content-type: application/json

{
  "result":[1,2],
  "hasMore":true,
  "id":"3517",
  "nextBatchId":2,
  "cached":false,
  "error":false,
  "code":201
}
```

Fetching the second batch would look like this:

```js
> curl -X POST --dump - http://localhost:8529/_api/cursor/3517
```

Assuming the above request fails because of a connection issue but advances the
cursor nonetheless, you can retry fetching the batch using the `nextBatchId` of
the first request (`2`):

```js
curl -X POST --dump http://localhost:8529/_api/cursor/3517/2

{
  "result":[3,4],
  "hasMore":true,
  "id":"3517",
  "nextBatchId":3,
  "cached":false,
  "error":false,
  "code":200
}
```

To allow refetching of the last batch of the query, the server cannot
automatically delete the cursor. After the first attempt of fetching the last
batch, the server would normally delete the cursor to free up resources. As you
might need to reattempt the fetch, it needs to keep the final batch when the
`allowRetry` option is enabled. Once you successfully received the last batch,
you should call the `DELETE /_api/cursor/<cursor-id>` endpoint so that the
server doesn't unnecessary keep the batch until the cursor times out
(`ttl` query option).

```js
curl -X POST --dump http://localhost:8529/_api/cursor/3517/3

{
  "result":[5],
  "hasMore":false,
  "id":"3517",
  "cached":false,
  "error":false,
  "code":200
}

curl -X DELETE --dump http://localhost:8529/_api/cursor/3517

{
  "id":"3517",
  "error":false,
  "code":202
}
```

Modifying documents
-------------------

The `_api/cursor` endpoint can also be used to execute modifying queries.

The following example appends a value into the array `arrayValue` of the document
with key `test` in the collection `documents`. Normal update behavior is to
replace the attribute completely, and using an update AQL query with the `PUSH()` 
function allows to append to the array.

```js
curl --data @- -X POST --dump http://127.0.0.1:8529/_api/cursor
{ "query": "FOR doc IN documents FILTER doc._key == @myKey UPDATE doc._key WITH { arrayValue: PUSH(doc.arrayValue, @value) } IN documents","bindVars": { "myKey": "test", "value": 42 } }

HTTP/1.1 201 Created
Content-type: application/json; charset=utf-8

{
  "result" : [],
  "hasMore" : false,
  "extra" : {
    "stats" : {
      "writesExecuted" : 1,
      "writesIgnored" : 0,
      "scannedFull" : 0,
      "scannedIndex" : 1,
      "filtered" : 0
    },
    "warnings" : []
  },
  "error" : false,
  "code" : 201
}
```

Setting a memory limit
----------------------

To set a memory limit for the query, the `memoryLimit` option can be passed to
the server.
The memory limit specifies the maximum number of bytes that the query is
allowed to use. When a single AQL query reaches the specified limit value, 
the query will be aborted with a *resource limit exceeded* exception. In a 
cluster, the memory accounting is done per server, so the limit value is 
effectively a memory limit per query per server.

```js
> curl --data @- -X POST --dump - http://localhost:8529/_api/cursor
{ "query" : "FOR i IN 1..100000 SORT i RETURN i", "memoryLimit" : 100000 }

HTTP/1.1 500 Internal Server Error
Server: ArangoDB
Connection: Keep-Alive
Content-Type: application/json; charset=utf-8
Content-Length: 115

{"error":true,"errorMessage":"query would use more memory than allowed (while executing)","code":500,"errorNum":32}
```

If no memory limit is specified, then the server default value (controlled by
startup option `--query.memory-limit` will be used for restricting the maximum amount 
of memory the query can use. A memory limit value of `0` means that the maximum
amount of memory for the query is not restricted. 
