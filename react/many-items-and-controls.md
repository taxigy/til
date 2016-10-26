# Many items and controls

I found it surprisingly useful for those who learn React to
explain this little thing, hence this note.

Let's pretend you have a collection of something that is
represented in your code as an array of something. For
simplicity and to keep it more generic, let's say it's an array
or objects, just like this:

```javascript
const people = [{
  name: 'John Doe',
  phone: '+1234567890'
}, {
  name: 'Jane Doe',
  phone: '+1987654321'
}];
```

You need to render it into a table. Your component that does it
would look like

```javascript
function peopleTable(people) {
  return (
    <table>
      {people.map((person, index) => (
        <tr>
          <td>{person.name}</td>
          <td>{person.phone}</td>
        </tr>
      ))}
    </table>
  );
}
```

And now you want to assign a bunch of actions to every item of
this list. Say, you want _a button next to every person_ that
would open that certain person's details and such. It would look
like:

```javascript
function peopleTable(people) {
  return (
    <table>
      {people.map((person, index) => (
        <tr>
          <td>{person.name}</td>
          <td>{person.phone}</td>
          <td>
            <button>Details</button>
          </td>
        </tr>
      ))}
    </table>
  );
}
```

Good. Now, you want this button to perform some operation.
Talking about buttons, you want it to perform it _on click_, and
the operation itself is a function you want to call. More than
that, you want this function to be called with a particular
argument, the corresponding person. Then it's like this:

```javascript
function peopleTable(people) {
  return (
    <table>
      {people.map((person, index) => (
        <tr>
          <td>{person.name}</td>
          <td>{person.phone}</td>
          <td>
            <button
              onClick={() => console.log(person)}>
              Details
            </button>
          </td>
        </tr>
      ))}
    </table>
  );
}
```

Here, in every row, there is a column (the last one) that contains a button, and clicking this button executes

```javascript
() => console.log(person)
```

while `person` is the item of a collection we map through. For
the first item, `person.name` is "John Doe", for the second, it's
"Jane Doe".

Using `console.log` is a good practice when you're writing new
code and need to debug it along the way. Using `console.log`, you
don't introduce new functions to your code until it's perfect
time for that, and you see the result in your browser console.
Double benefit!

----

One possible mistake you can make is to declare `person` as an
argument of that function instead of referring to `person` that
is the argument to outer scope function:

```javascript
(person) => console.log(person)
```

This way, you'll instantiate `person` variable in context of this
little function. Try it and see what happens.

----

There's also a bit more advanced way of passing a function with
the right context to `onClick` prop of button element:

```javascript
function peopleTable(people) {
  const handleClick = person => () => console.log(person);

  return (
    <table>
      {people.map((person, index) => (
        <tr>
          <td>{person.name}</td>
          <td>{person.phone}</td>
          <td>
            <button
              onClick={handleClick(person)}>
              Details
            </button>
          </td>
        </tr>
      ))}
    </table>
  );
}
```

Here, `handleClick` is a function that, when called, returns a
(kind of partially applied) function that will hold the value of
`person` in it forever. The perfectly right value!

Since calling `handleClick(•)` returns a function immediately when
component is rendered, the returned function will be passed to
`onClick` and will maintain the value of `person`. The point in
time when this returned function is going to be executed is
completely unknown, for it's an _event handler_, and events
(especially those originated by a user) happen unpredictably. But
since you encapsulated the value of `person` into that function,
it's going to be fine.

Try to do something meaningful instead of `console.log`ging and
see how it works!
