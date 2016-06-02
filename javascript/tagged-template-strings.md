# Tagged template strings

Template strings are a great way to combine static strings with values of
constants and variables. But there's even more interesting way to manipulate
strings.

```javascript
const f = (letters, ...digits) => console.log(letters, digits);
f(`abc${123}def${456}`);
```

The problem is that, what's the application of it? Is there any case when a
function of classic arguments would not be enough so tagged template literal
would be the only option?

Relay + GraphQL could be one:

```javascript
TeaStore = Relay.createContainer(TeaStore, {
  fragments: {
    store: () => Relay.QL`
      fragment on Store {
        teas { ${Tea.getFragment('tea')} },
      }
    `,
  },
});
```

(in progress)
