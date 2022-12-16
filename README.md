# Gradient

Class to create **linear, radial, elliptical and conic gradients** as bitmaps, even without canvas

**version 1.0.0** (9 kB minified)

Example:

```javascript
const w = 200, h = 200;

function addColorStops(gradient)
{
    const colors = ['red', 'yellow', 'green', 'blue'];
    const stops = [0.0, 0.33, 0.66, 1.0];
    stops.forEach((s, i) => {
        gradient.addColorStop(s, colors[i]);
    });
    return gradient;
}
function applyGradient(gradient1, canvas1, gradient2, canvas2)
{
    addColorStops(gradient1);
    addColorStops(gradient2);
    const ctx1 = canvas1.getContext("2d");
    ctx1.fillStyle = gradient1;
    ctx1.fillRect(0, 0, w, h);
    const ctx2 = canvas2.getContext("2d");
    const imData = ctx2.createImageData(w, h);
    imData.data.set(gradient2.getBitmap(w, h));
    ctx2.putImageData(imData, 0, 0);
}
function drawLinearGradient(x1, y1, x2, y2)
{
    const canvas1 = document.getElementById('linear1');
    const canvas2 = document.getElementById('linear2');
    applyGradient(
        canvas1.getContext("2d").createLinearGradient(x1, y1, x2, y2),
        canvas1,
        Gradient.createLinearGradient(x1, y1, x2, y2),
        canvas2
    );
}
function drawRadialGradient(x1, y1, r1, x2, y2, r2)
{
    const canvas1 = document.getElementById('radial1');
    const canvas2 = document.getElementById('radial2');
    applyGradient(
        canvas1.getContext("2d").createRadialGradient(x1, y1, r1, x2, y2, r2),
        canvas1,
        Gradient.createRadialGradient(x1, y1, r1, x2, y2, r2),
        canvas2
    );
}
function drawEllipticGradient(cx, cy, rx, ry, angle)
{
    const canvas1 = document.getElementById('elliptic1');
    const canvas2 = document.getElementById('elliptic2');
    const ctx = canvas1.getContext("2d");
    const r = Math.max(rx, ry), sx = rx/r, sy = ry/r;
    applyGradient(
        canvas1.getContext("2d").createRadialGradient(cx/sx, cy/sy, 0, cx/sx, cy/sy, r),
        canvas1,
        Gradient.createEllipticGradient(cx, cy, rx, ry, /*angle*/0),
        canvas2
    );
    ctx.scale(sx, sy);
    /*ctx.translate(-cx/sx, -cy/sy);
    ctx.rotate(angle);
    ctx.translate(cx/sx, cy/sy);*/
    ctx.fillRect(0, 0, w/sx, h/sy);
}
function drawConicGradient(angle, cx, cy)
{
    const canvas1 = document.getElementById('conic1');
    const canvas2 = document.getElementById('conic2');
    applyGradient(
        canvas1.getContext("2d").createConicGradient(angle, cx, cy),
        canvas1,
        Gradient.createConicGradient(angle, cx, cy),
        canvas2
    );
}

drawLinearGradient(20, 20, w - 20, h - 20);
drawRadialGradient(100, 100, 10, 160, 120, 80);
drawEllipticGradient(100, 100, 100, 40, Math.PI/4);
drawConicGradient(Math.PI/4, 60, 80);
```

![gradients example](/screenshot.png)

**see also**

* [css-color](https://github.com/foo123/css-color) simple class to parse and manipulate colors in various formats