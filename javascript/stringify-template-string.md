# Stringify in a template string

Be careful with `JSON.parse` and `JSON.stringify` when using
template strings:

```javascript
> `{"abc": "${JSON.stringify( 'def' )}"}`
'{"abc": ""def""}'
> JSON.parse(`{ "abc": "${JSON.stringify('def')}" }`)
SyntaxError: Unexpected token d in JSON at position 10
```

omit the quotes:

```javascript
> `{"abc": ${JSON.stringify( 'def' )}}`
'{"abc": "def"}'
> JSON.parse(`{ "abc": ${JSON.stringify('def')} }`)
{ abc: 'def' }
```
