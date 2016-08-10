# Making a very basic GraphQL server in Express and React

Tutorial is not enough!

Let's pretend you have a very basic source of data that can be represented by JSON:

```json
[{
  "name": "John",
  "balance": 1000
}, {
  "name": "Jane",
  "balance": -1000
}]
```

and so on. So, a list of object each having a bunch of properties. Now, let's pretend you need to either return the whole list or a particular item of this list.

So, first, you need to init a Node app and add some packages:

```bash
npm init # do some setup
npm install --save express express-graphql graphql
```

I would also recommend setting up [Webpack](/webpack) to use [stage-0]() features.

Then:

```Javascript
import path from 'path';
import express from 'express';
import graphqlHTTP from 'express-graphql';
import schema from './schema';

const {
  PORT = 3000,
  PWD = __dirname
} = process.env;
const app = express();

app.use('/q', graphqlHTTP(req => ({
  schema,
  context: req.session
})));

app.use('/build/:file', (req, res) => {
  res.sendFile(req.params.file, {
    root: path.resolve(PWD, 'build')
  });
});

app.use('*', (req, res) => {
  res.sendFile('index.html', {
    root: PWD
  });
});

app.listen(PORT, () => console.log(`Running server on port ${PORT}`));
```

See that this server works for three different locations:

- `/q` — this is the endpoint for GraphQL queries!
- `/build/:file` — this is to make `script` and `link[rel=stylesheet]` tags in your HTML file work, and
- `*` to server your root HTML file per any other location.

Then, you need to define the schema because, well, it is imported from `./schema`. Do this:

```javascript
import {
  GraphQLSchema,
  GraphQLObjectType,
  GraphQLList,
  GraphQLString,
  GraphQLFloat
} from 'graphql';
import people from './people.json'; // <- your data source, may be a remote resource, a database, anything that returns values

const person = new GraphQLObjectType({ // <- represents a single item in the collection
  name: 'Person',
  fields: {
    name: {
      type: GraphQLString
    },
    balance: {
      type: GraphQLFloat
    }
  }
});
const list = new GraphQLList(person); // <- represents the whole collection of items

export default new GraphQLSchema({
  query: new GraphQLObjectType({
    name: 'People',
    fields: {
      people: {
        type: list,
        resolve: () => people
      },
      person: {
        type: person,
        args: {
          person: {
            type: GraphQLString
          }
        },
        resolve: (r, {name}) => people.find(person => person.name === name)
      }
    }
  })
});
```

Done. Now try it out: run the server and open something like [http://localhost:3000/q?query={people{name,balance}}](http://localhost:3000/q?query={people{name,balance}}) in the browser.
