# Gradient

Class to create **linear, radial, conic and elliptic gradients** and **image patterns** as bitmaps without canvas

**version 1.2.2** (13 kB minified)

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
grad.transform.transform(a, b, c, d, e, f);
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
pat.transform.transform(a, b, c, d, e, f);
pat.transform.reset();
pat.transform.save();
pat.transform.restore();

pat.getColorAt(x, y);
pat.getBitmap(width, height);
```

**Example:**

```javascript
const w = 200, h = 200;
const Matrix = Gradient.Matrix;

function addColorStops(gradient)
{
    const colors = ['red', 'yellow', 'green', 'blue'];
    const stops = [0.0, 0.33, 0.66, 1.0];
    stops.forEach((s, i) => {
        gradient.addColorStop(s, colors[i]);
    });
    return gradient;
}
function fill_transformed_rect(ctx, x, y, w, h)
{
    const m = ctx.getTransform();
    const m2 = (new Matrix(m.a, m.c, m.e, m.b, m.d, m.f)).inv();
    const p1 = m2.transform(x, y),
        p2 = m2.transform(x+w, y),
        p3 = m2.transform(x+w, y+h),
        p4 = m2.transform(x, y+h);
    ctx.beginPath();
    ctx.moveTo(p1.x, p1.y);
    ctx.lineTo(p2.x, p2.y);
    ctx.lineTo(p3.x, p3.y);
    ctx.lineTo(p4.x, p4.y);
    ctx.closePath();
    ctx.fill();
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
    const gradient2 = Gradient.createRadialGradient(cx/sx, cy/sy, 0, cx/sx, cy/sy, r);
    const gradient3 = ctx3.createRadialGradient(cx, cy, 0, cx, cy, r);
    const gradient4 = Gradient.createEllipticGradient(cx, cy, rx, ry, angle);

    addColorStops(gradient1);
    addColorStops(gradient2);
    addColorStops(gradient3);
    addColorStops(gradient4);

    // transform radial gradient1 to become an unrotated elliptic
    ctx1.scale(sx, sy);
    ctx1.fillStyle = gradient1;
    fill_transformed_rect(ctx1, 0, 0, w, h);

    // create a pattern from the gradient, just for fun
    const pattern2 = Gradient.createPattern({
        data: gradient2.getBitmap(w/sx, h/sy),
        width: Math.round(w/sx),
        height: Math.round(h/sy)
    }, 'no-repeat');
    // gradient2/pattern2 is a transformed radial gradient
    //pattern2.transform.translate(-cx, -cy);
    pattern2.transform.scale(sx, sy);
    //pattern2.transform.rotate(-angle);
    //pattern2.transform.translate(cx, cy);
    const imData2 = ctx2.createImageData(w, h);
    imData2.data.set(pattern2.getBitmap(w, h));
    ctx2.putImageData(imData2, 0, 0);

    // transform radial gradient3 to become a rotated elliptic
    ctx3.translate(cx, cy);
    ctx3.rotate(-angle);
    ctx3.translate(-cx*sx, -cy*sy);
    ctx3.scale(sx, sy);
    ctx3.fillStyle = gradient3;
    fill_transformed_rect(ctx3, 0, 0, w, h);
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

**see also:**

* [Rasterizer](https://github.com/foo123/Rasterizer) stroke and fill lines, rectangles, curves and paths, without canvas.
* [Gradient](https://github.com/foo123/Gradient) create linear, radial, conic and elliptic gradients and image patterns without canvas.
* [Geometrize](https://github.com/foo123/Geometrize) Computational Geometry and Rendering Library for JavaScript
* [Plot.js](https://github.com/foo123/Plot.js) simple and small library which can plot graphs of functions and various simple charts and can render to Canvas, SVG and plain HTML
* [MOD3](https://github.com/foo123/MOD3) 3D Modifier Library in JavaScript
* [HAAR.js](https://github.com/foo123/HAAR.js) image feature detection based on Haar Cascades in JavaScript (Viola-Jones-Lienhart et al Algorithm)
* [HAARPHP](https://github.com/foo123/HAARPHP) image feature detection based on Haar Cascades in PHP (Viola-Jones-Lienhart et al Algorithm)
* [FILTER.js](https://github.com/foo123/FILTER.js) video and image processing and computer vision Library in pure JavaScript (browser and node)
* [css-color](https://github.com/foo123/css-color) simple class to parse and manipulate colors in various formats

