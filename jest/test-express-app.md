# Testing an Express app

Two things to keep in mind:

1. You typically want to run the app and listen on some port in the same file, let's call it src/server.js
1. There's no way for Express app to programmatically execute routes per fake request.
1. But there's a lib for that, [supertest](https://www.npmjs.com/package/supertest).

```javascript
// src/server.test.js
import request from 'supertest';
import { app, instance } from './server';

describe('handling some requests', () => {
  // number of tests to run
  expect.assertions(1);

  test('200 on /', (done) => {
    request(app)
      .get('/')
      .expect(200)
      .end(done);
  });

  // more tests

  afterAll(() => {
    appInstance.close();
  });
});
```

From src/server.js, there have to be two things exported: the instance of the app and the app itself:

```javascript
// src/server.js
import express from 'express';

export const app = express();

app.get('/', (request, response) => res.json(true));

export const instance = app.listen(process.env.PORT);
```

Still, supertest may not stop the server, and `instance.close()` closes the connection but does not stop the server or make the process exit. So, the corresponding NPM task must force exit when Jest has run all the tests:

```bash
jest --forceExit
```

This way, you'll always be sure that your CI will not get stuck because of Node process hanging.
