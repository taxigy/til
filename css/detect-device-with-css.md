# Detect device with CSS only

There's an awesome article about that:
<https://css-tricks.com/snippets/css/media-queries-for-standard-devices/>.
Generally, check min-device-width, max-device-width and pixel
ratio to detect the device _with unavoidable error_, because
viewports come all the time and then they don't go.

For more control and greater reliability of detection, usage of
Javascript is unavoidable. Simple check of the presence of
`window.orientation` would be enough.

But generally, the layout must work regardless of the device,
pixel density, screen dimensions and click/touch controls.
Browsers take care of great part of this problem, so there's no
excuse in making a layout that needs to know the device it's
running on. Except maybe the cases when the device model is the
point, as in the famous ["Fanboys" article by The
Verge](https://www.theverge.com/2014/1/21/5307992/inside-the-mind-of-a-fanboy).
