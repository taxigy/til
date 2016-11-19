# Terminal close event

Every Node process can listen to `SIGTERM` which is fired every
time the process is killed, just a few seconds before `SIGKILL`
is dispatched. If you listen to `SIGTERM`, you'll be able to
catch Control+C pressede by user in terminal window. But what if
the user closes the window without stopping the process (aka
sending `SIGTERM`)?

Then you need to listen to `SIGHUP`. This event is dispatched, in
particular, every time a terminal session is closed. This exact
event will be dispatched when the user closes tab or window of
the terminal, therefore hanging up the Node process running
there.

You can't see the console message directly after `SIGHUP`
because, well, the terminal window or tab will have been closed
by the time the process has responded to the event. A good way to
catch it (and ensure it's really getting caught) is to write into
file:

```javascript
process.on('SIGHUP', () => {
  fs.writeFileSync(`${process.env.PWD}/sighup.txt`, `It happened at ${new Date().toJSON()}`);
  // do something meaningful: stop the HTTP server, etc.
});
```
