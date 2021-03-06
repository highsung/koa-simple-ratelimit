# koa-simple-ratelimit

[![NPM version][npm-img]][npm-url]
[![build status][travis-img]][travis-url]
[![coverage][coverage-img]][coverage-url]
[![greenkeeper][greenkeeper-img]][greenkeeper-url]

[npm-img]: https://img.shields.io/npm/v/koa-simple-ratelimit.svg
[npm-url]: https://npmjs.org/package/koa-simple-ratelimit
[travis-img]: https://img.shields.io/travis/scttcper/koa-simple-ratelimit.svg
[travis-url]: https://travis-ci.org/scttcper/koa-simple-ratelimit
[coverage-img]: https://codecov.io/gh/scttcper/koa-simple-ratelimit/branch/master/graph/badge.svg
[coverage-url]: https://codecov.io/gh/scttcper/koa-simple-ratelimit  
[greenkeeper-img]: https://badges.greenkeeper.io/scttcper/koa-simple-ratelimit.svg
[greenkeeper-url]: https://greenkeeper.io/

 Rate limiter middleware for koa v2. Differs from [koa-ratelimit](https://github.com/koajs/ratelimit) by not depending on [ratelimiter](https://github.com/tj/node-ratelimiter) and using redis ttl (time to live) to handle expiration time remaining. This creates only one entry in redis instead of the three that node-ratelimiter does.

## Installation

```js
$ npm install koa-simple-ratelimit
```

## Example

```js
var ratelimit = require('koa-simple-ratelimit');
var redis = require('redis');
var koa = require('koa');
var app = new koa();

// apply rate limit

app.use(ratelimit({
  db: redis.createClient(),
  duration: 60000,
  max: 100,
  id: function (ctx) {
    return ctx.ip;
  },
  blacklist: [],
  whitelist: []
}));

// response middleware

app.use(function (ctx){
  ctx.body = 'Hello';
});

app.listen(3000);
console.log('listening on port 3000');
```

## Options

 - `db` redis connection instance
 - `max` max requests within `duration` [2500]
 - `duration` of limit in milliseconds [3600000]
 - `id` id to compare requests [ip]
 - `whitelist` array of ids to whitelist
 - `blacklist` array of ids to blacklist

## Responses

  Example 200 with header fields:

```
HTTP/1.1 200 OK
X-Powered-By: koa
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
X-RateLimit-Reset: 1384377793
Content-Type: text/plain; charset=utf-8
Content-Length: 6
Date: Wed, 13 Nov 2013 21:22:13 GMT
Connection: keep-alive

Stuff!
```

  Example 429 response:

```
HTTP/1.1 429 Too Many Requests
X-Powered-By: koa
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1384377716
Content-Type: text/plain; charset=utf-8
Content-Length: 39
Retry-After: 7
Date: Wed, 13 Nov 2013 21:21:48 GMT
Connection: keep-alive

Rate limit exceeded, retry in 8 seconds
```

## License

  MIT