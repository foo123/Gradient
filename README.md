# Gradient

Class to create **linear, radial, conic and elliptic gradients** and **image patterns** as bitmaps without canvas

**version 1.2.1** (13 kB minified)

**API:**

```javascript
// create gradients
const grad = Gradient.createLinearGradient(x1, y1, x2, y2);
const grad = Gradient.createRadialGradient(x1, y1, r1, x2, y2, r2);
const grad = Gradient.createConicGradient(angle, cx, cy);
const grad = Gradient.createEllipticGradient(cx, cy, rx, ry, angle);

// methods
grad.addColorStop(offset, color);

grad.transform.scale(sx, sy);
grad.transform.rotate(angle);
grad.transform.translate(tx, ty);
grad.transform.reflectX();
grad.transform.reflectY();
grad.transform.skewX(s);
grad.transform.skewY(s);
grad.transform.reset();
grad.transform.save();
grad.transform.restore();

grad.getColorAt(x, y);
grad.getBitmap(width, height);

// create patterns
const pat = Gradient.createPattern(imageData, repetition = 'repeat');

// methods
pat.transform.scale(sx, sy);
pat.transform.rotate(angle);
pat.transform.translate(tx, ty);
pat.transform.reflectX();
pat.transform.reflectY();
pat.transform.skewX(s);
pat.transform.skewY(s);
pat.transform.reset();
pat.transform.save();
pat.transform.restore();

pat.getColorAt(x, y);
pat.getBitmap(width, height);
```

**Example:**

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
function drawLinearGradient(x1, y1, x2, y2)
{
    const canvas1 = document.getElementById('linear1');
    const canvas2 = document.getElementById('linear2');
    const ctx1 = canvas1.getContext("2d");
    const ctx2 = canvas2.getContext("2d");

    const gradient1 = ctx1.createLinearGradient(x1, y1, x2, y2);
    const gradient2 = Gradient.createLinearGradient(x1, y1, x2, y2);

    addColorStops(gradient1);
    addColorStops(gradient2);

    ctx1.fillStyle = gradient1;
    ctx1.fillRect(0, 0, w, h);

    const imData = ctx2.createImageData(w, h);
    imData.data.set(gradient2.getBitmap(w, h));
    ctx2.putImageData(imData, 0, 0);
}
function drawRadialGradient(x1, y1, r1, x2, y2, r2)
{
    const canvas1 = document.getElementById('radial1');
    const canvas2 = document.getElementById('radial2');
    const ctx1 = canvas1.getContext("2d");
    const ctx2 = canvas2.getContext("2d");

    const gradient1 = ctx1.createRadialGradient(x1, y1, r1, x2, y2, r2);
    const gradient2 = Gradient.createRadialGradient(x1, y1, r1, x2, y2, r2);

    addColorStops(gradient1);
    addColorStops(gradient2);

    ctx1.fillStyle = gradient1;
    ctx1.fillRect(0, 0, w, h);

    const imData = ctx2.createImageData(w, h);
    imData.data.set(gradient2.getBitmap(w, h));
    ctx2.putImageData(imData, 0, 0);
}
function drawEllipticGradient(cx, cy, rx, ry, angle)
{
    const canvas1 = document.getElementById('elliptic1');
    const canvas2 = document.getElementById('elliptic2');
    const ctx1 = canvas1.getContext("2d");
    const ctx2 = canvas2.getContext("2d");

    const r = Math.max(rx, ry), sx = rx/r, sy = ry/r;

    const gradient1 = ctx1.createRadialGradient(cx/sx, cy/sy, 0, cx/sx, cy/sy, r);
    const gradient2 = Gradient.createEllipticGradient(cx, cy, rx, ry, 0/*angle*/);

    addColorStops(gradient1);
    addColorStops(gradient2);

    // scale radial gradient1 to become an unrotated elliptic
    ctx1.scale(sx, sy);
    ctx1.fillStyle = gradient1;
    ctx1.fillRect(0, 0, w/sx, h/sy);

    // gradient2 is true elliptic gradient
    const imData2 = ctx2.createImageData(w, h);
    imData2.data.set(gradient2.getBitmap(w, h));
    ctx2.putImageData(imData2, 0, 0);
}
function drawConicGradient(angle, cx, cy)
{
    const canvas1 = document.getElementById('conic1');
    const canvas2 = document.getElementById('conic2');
    const ctx1 = canvas1.getContext("2d");
    const ctx2 = canvas2.getContext("2d");

    const gradient1 = ctx1.createConicGradient(angle, cx, cy);
    const gradient2 = Gradient.createConicGradient(angle, cx, cy);

    addColorStops(gradient1);
    addColorStops(gradient2);

    ctx1.fillStyle = gradient1;
    ctx1.fillRect(0, 0, w, h);

    const imData = ctx2.createImageData(w, h);
    imData.data.set(gradient2.getBitmap(w, h));
    ctx2.putImageData(imData, 0, 0);
}

drawLinearGradient(20, 20, w - 20, h - 20);
drawRadialGradient(100, 100, 10, 160, 120, 80);
drawEllipticGradient(100, 100, 100, 40, Math.PI/4);
drawConicGradient(Math.PI/4, 60, 80);
```

![gradients example](/screenshot.png)

**see also**

* [css-color](https://github.com/foo123/css-color) simple class to parse and manipulate colors in various formats