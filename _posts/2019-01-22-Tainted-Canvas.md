---
layout: single
title: Tainted canvases may not be exported
categories: [software]
tags: [html5,canvas]
---

How to export a tainted HTML5 canvas to Base64.

Pretty specific but I recently was doing some work with HTML5 canvases.
I was reconstructing a type of photo frame and then overlaying an image atop it.

After the canvas was ready, I then wanted to export it to Base64 so that it
could be sent over to the server for some additional processing.

Unfortunately I was hitting an error that would not allow me to do that :(

```
Uncaught DOMException: Failed to execute 'toDataURL' on 'HTMLCanvasElement': Tainted canvases may not be exported.
```

After some debugging, it seems that if the image src is from a different ORIGIN,
then the canvas is deemed as being tainted. We can get around this by setting the
`crossOrigin` attribute on the `HTMLImageElement` to be 'anonymous' before we draw onto our
canvas.  
_Protip: You need to set this before setting the src._

```javascript
let image = new Image()
image.crossOrigin = "anonymous";
image.src = 'HTTPS://REMOTE.HOST/IMG';

ctx.drawImage(image, canvas.width/2 - 40, 25, 80, 120);

canvas.toDataURL();
```

You should now be able export the canvas to Base64.
