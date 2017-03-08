# Download text content as a file

Example: you have a textarea and you want to download whatever is inside this
textarea element (its value) as a file. The HTML would look like  

```html
<textarea id="textbox" placeholder="Type something here"></textarea>
<a download="info.txt" id="create" href="#">Download info.txt</a>
```

So, a corresponding React component stub would look like

```javascript
class DownloadableTextarea extends Component {
  state = {
    text: ''
  }

  render() {
    return (
      <div>
        <textarea
          id="textbox"
          placeholder="Type something here" />
        <a
          download="info.txt"
          id="create"
          href="#">
          Download info.txt
        </a>
      </div>
    );
  }
}
```

Now, the logic is simple:

1. Every time value of textarea element changes (hop la, `onChange`), change
   component's own state property "text".
1. Every time the anchor element is
   clicked, its `href` is generated in a way that it represents a stream (in this
   case, [Blob](https://developer.mozilla.org/en/docs/Web/API/Blob)).

It will be automatically downloaded then.

So, the logic is quite simple. In jQuery, it would look like

```javascript
(function() {
  var textFile = null;
  var makeTextFile = function(text) {
    var data = new Blob([text], {
      type: 'text/plain'
    });

    // If we are replacing a previously generated file we need to
    // manually revoke the object URL to avoid memory leaks.
    if (textFile !== null) {
      window.URL.revokeObjectURL(textFile);
    }

    textFile = window.URL.createObjectURL(data);

    return textFile;
  };


  var create = document.getElementById('create');
  var textbox = document.getElementById('textbox');

  textbox.addEventListener('focus', function (event) {
    create.style.display = 'block';
    create.href = makeTextFile(''); // initially, the text is empty.
  });

  textbox.addEventListener('change', function (event) {
    create.href = makeTextFile(event.target.value); // per every change, update value of href attribute of #create
  });
})();
```

And so in React, the component would look like

```javascript
class DownloadableTextarea extends Component {
  state = {
    text: '',
    data: null
  }

  componentDidMount() {
    this.makeTextDownloadable();
  }

  makeTextDownloadable() {
    const blob = new Blob([this.state.text], {
      type: 'text/plain'
    });

    // If we are replacing a previously generated file we need to
    // manually revoke the object URL to avoid memory leaks.
    if (this.state.data !== null) {
      window.URL.revokeObjectURL(this.state.data);
    }

    this.setState({
      data: window.URL.createObjectURL(blob)
    });
  }

  onChangeTextarea = (event) => {
    const {
      value
    } = event.target;
  }

  render() {
    return (
      <div>
        <textarea
          placeholder="Type something here"
          value={this.state.text}
          onChange={this.onChangeTextarea} />
        <a
          download="info.txt"
          href={this.state.data}>
          Download info.txt
        </a>
      </div>
    );
  }
}
```

---

The code is super crude and worth being optimized, but TBD.

There's also a [Codepen for that](http://codepen.io/rishatmuhametshin/pen/bgaBEM?editors=1010#0).
