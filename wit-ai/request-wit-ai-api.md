# Request Wit.ai API

There's a [node-wit](https://www.npmjs.com/package/node-wit) package to query Wit.ai API but there's also a lower-level way to do that. Given API access token, it's like

```javascript
import axios from 'axios';

const {
  WIT_API_TOKEN
} = process.env;
const WIT_API_URL = 'https://api.wit.ai';

const recognize = message => axios.get(`${WIT_API_URL}/message`, {
  headers: {
    'Authorization': `Bearer ${WIT_API_TOKEN}`
  },
  params: {
    v: '20160924',
    q: message.text
  }
});
```

Now use it as

```javascript
recognize('I am a shape').then(response => console.log(response.data.entities)).catch(error => console.error(error));
```

or in [async/await](/javascript/asynchronous-functions-should-have-try-catch.md) manner:

```javascript
async () => {
  try {
    const {
      data
    } = await recognize('I am a shape');

    console.log(data.entities);
  } catch (error) {
    console.error(error);
  }
}
```
