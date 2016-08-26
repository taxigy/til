# Generating PDF on Express server from React components using html-pdf

There are different libraries that help you generate PDF on server, some of them even claim they can work on the client side (and then require `fs` module, lol). To me, [html-pdf](https://github.com/marcbachmann/node-html-pdf) feels most suitable among all of them for lightweight virtual printing. But

- it requires stylesheet to be present in DOM directly, not as a reference via `link` element, and
- default Webpack's [css-loader](https://github.com/webpack/css-loader) looks for `head` element to place the styles but there's none when you render on server!

To make it work, css-loader is not an option. It's just not suitable because it tries to put styles into `link` within `head` but at the compile-time, there's no `head`! Instead, [css-to-string-loader](https://github.com/smithad15/css-to-string-loader) should be used. It lazy-loads all the styles as strings. Next thing is to render to static markup:

```javascript
app.get('/', (req, res) => {
  const render = ReactDOMServer.renderToStaticMarkup(
    <Html {...req.body} />
  );

  pdf.create(render, {}).toBuffer((error, buffer) => {
    res.set('Content-Type', 'application/pdf');
    res.send(buffer);
  });
});
```

while `Html` component looks like

```javascript
class Html extends Component {
  render() {
    return (
      <html>
        <head />
        <body>
          <div className="app">
            ...
          </div>
        </body>
      </html>
    );
  }
};
```

For every component, styles should be put directly into component's hierarchy of elements:

```javascript
import React, { Component } from 'react';
import styles from './MyComponent.css';

class MyComponent extends Component {
  render() {
    return (
      <div className="my-component">
        ...
        <style>{styles}</style>
      </div>
    );
  }
}
```

Repeat for every component.

With styles put directly into DOM, regardless of location, html-pdf is now comfortable with it and applies these styles to corresponding elements properly, producing a PDF that closely resembles the page as it would appear in the browser.
