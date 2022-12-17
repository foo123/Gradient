# Gradient

Class to create **linear, radial, conic and elliptic gradients** as bitmaps without canvas

**version 1.1.1** (10 kB minified)

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
grad.transform.skewX(s);
grad.transform.skewY(s);
grad.transform.reset();

grad.getColorAt(x, y);
grad.getBitmap(width, height);
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
    const canvas3 = document.getElementById('elliptic3');
    const canvas4 = document.getElementById('elliptic4');
    const ctx1 = canvas1.getContext("2d");
    const ctx2 = canvas2.getContext("2d");
    const ctx3 = canvas3.getContext("2d");
    const ctx4 = canvas4.getContext("2d");
    
    const r = Math.max(rx, ry), sx = rx/r, sy = ry/r;
    
    const gradient1 = ctx1.createRadialGradient(cx/sx, cy/sy, 0, cx/sx, cy/sy, r);
    const gradient2 = Gradient.createRadialGradient(cx, cy, 0, cx, cy, r);
    const gradient3 = ctx3.createRadialGradient(cx, cy, 0, cx, cy, r);
    const gradient4 = Gradient.createEllipticGradient(cx, cy, rx, ry, angle);
    
    addColorStops(gradient1);
    addColorStops(gradient2);
    addColorStops(gradient3);
    addColorStops(gradient4);
    
    // transform radial gradient1 to become an unrotated elliptic
    ctx1.scale(sx, sy);
    ctx1.fillStyle = gradient1;
    ctx1.fillRect(0, 0, w/sx, h/sy);
    // gradient2 is a transformed radial gradient
    gradient2.transform.translate(-cx, -cy);
    gradient2.transform.scale(sx, sy);
    //gradient2.transform.rotate(-angle);
    gradient2.transform.translate(cx, cy);
    const imData2 = ctx2.createImageData(w, h);
    imData2.data.set(gradient2.getBitmap(w, h));
    ctx2.putImageData(imData2, 0, 0);

    // transform radial gradient3 to become a rotated elliptic (does not produce expected result)
    ctx3.scale(sx, sy);
    ctx3.translate(-cx, -cy);
    ctx3.rotate(-angle);
    ctx3.translate(cx, cy);
    ctx3.fillStyle = gradient3;
    ctx3.fillRect(0, 0, w, h);
    // gradient4 is elliptic gradient
    const imData4 = ctx4.createImageData(w, h);
    imData4.data.set(gradient4.getBitmap(w, h));
    ctx4.putImageData(imData4, 0, 0);
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