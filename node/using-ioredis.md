# Using ioredis

There's a nice lib for Node to connect to Redis, [ioredis](https://www.npmjs.com/package/ioredis). There are things to keep in mind though.

First, always check the status of connection using `Redis#status` property. It
should be "ready". If you simply return the result of calling `Redis#get` (which
is a Promise) when the status is "connection" or any other non-"ready", you'll
end up with your server not responding and waiting for the connection the be
established again. Given that Redis service may be failing indefinitely, the
server should respond immediately with 503 "Service Unavailable" or similar.

When setting value for key, keep in mind that `Redis#set` returns a Promise that
resolves into "OK" (exactly what Redis returns). To return the value
immediately, while making sure that it's set correctly, return a Promise that
resolves into the data fetched from Redis. It's too cautious, but at least you
can unit-test it properly.

So, given that you have a function to fetch and a function to store in Redis,
and `cache` is result of calling `new require('ioredis')()`, this is how these
functions might look like:

```javascript
export function fetchDataFromRedis(key) {
  if (cache && cache.status === 'ready') {
    return cache.get(key);
  }

  return Promise.reject(503);
}

export function storeDataInRedis(key, data) {
  if (cache && cache.status === 'ready') {
    return cache
      .set(key, JSON.stringify(data), 'EX', ONE_WEEK_IN_SECONDS)
      .then(() => fetchDataFromRedis(key));
  }

  return Promise.reject(503);
}
```
