# postmessage-toolbox-example
Basic example to demonstrate the possibilities of the custom postMessage toolboxes

### [Check out the example](https://edmdesigner.github.io/postmessage-toolbox-example/)

## 1. Communication with the editor

When your toolbox is ready to receive data you have to send a [postMessage](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) with `'toolboxReady'` event type. In the example we attached this operation to the `onload` event handler.

```js
window.onload = function() {
  window.parent.postMessage('toolboxReady', '*');
}
```

## 2. Receive selected element

When you click on a custom element in the editor the corresponding toolbox will receive a message with the selected element's template json.

```js
window.addEventListener('message', receiveMessage, false);

function receiveMessage(event) {
  try {
    console.log(JSON.parse(event.data)); // selected element's json
  } catch(e) {
    console.error("Unknown message", e);
  }
}
```

## 3. Make changes on the element

The received selected element's object looks like this:

```js
{
  _dynId: 42 // unique element id
  userId: "..." // current user's id
  projectId: "..." // template id
  elementJson: {
    originalRoot: { ... } // original element json which you set in the dashboard
    root: { ... } // current state of the element json, it could differ from the originalRoot
  }
}
```
We suggest to make changes only on the `root` object. The `originalRoot` will come in handy when you want to replace a template string with the name of the user for example.

## 4. Send messages back to editor

You have to include `_dynId`, `action`, and `props` object with the root element. Action type should always be `setProps`.

```js
function sendMessage(selectedElement) {
  window.parent.postMessage(JSON.stringify({
    _dynId: selectedElement._dynId,
    action: "setProps",
    props: {
      root: selectedElement.elementJson.root
    }
  }), '*');
}
```

## Additional notes:

We suggest to set maximum width of toolbox to 282 px.
```css
body {
  max-width: 282px;
}
```
