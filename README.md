# Spinning Cone
This code renders a cone that spinning in ASCII character hash (#).

## Cone
A cone has:
- 1 circular base
- 1 apex point (the tip)
- A curved surface connecting the base to the apex

In ASCII, we approximate the circular base using a polygon (e.g. 20 points).

### Structure
- **Apex** at the top (e.g. [0, 1, 0])
- **Base** on the bottom plane (y = -1) forming a circle using multiple points evenly spaced by angle

## Vertex Coordinates
We generate 21 points:
- Point 0: Apex
- Points 1–20: Base circle points

```js
const points = [];
const baseCount = 20;
const radius = 1;
const height = 2;

// Apex point
points.push([0, height / 2, 0]);

// Base circle points
for (let i = 0; i < baseCount; i++) {
  const angle = (2 * Math.PI * i) / baseCount;
  const x = radius * Math.cos(angle);
  const z = radius * Math.sin(angle);
  points.push([x, -height / 2, z]);
}
```

## Edges
We define two sets of edges:
- **Side edges** from apex to each base point
- **Base circle** edges connecting adjacent base points

```js
const edges = [];

// Side edges
for (let i = 1; i <= baseCount; i++) {
  edges.push([0, i]);
}

// Base edges (circle)
for (let i = 1; i <= baseCount; i++) {
  const next = i === baseCount ? 1 : i + 1;
  edges.push([i, next]);
}
```
### Drawing Edges

Each edge is drawn using Bresenham’s line algorithm, into a 2D character buffer.

```js
function drawLine(buf, x0, y0, x1, y1) {
  // Classic Bresenham algorithm
}
```
## 3D Rotation
We rotate all points around the Y-axis:

$`\begin{pmatrix} \cos\theta & 0 & -\sin\theta \\ 0 & 1 & 0 \\ \sin\theta & 0 & \cos\theta \end{pmatrix}`$

```js
function rotateY([x, y, z], θ) {
  const c = Math.cos(θ), s = Math.sin(θ);
  return [
    c * x - s * z,
    y,
    s * x + c * z
  ];
}
```

## Perspective Projection
We simulate depth using a simple perspective projection:

$`x_{\text{proj}} = \frac{x'}{z' + d}, \quad y_{\text{proj}} = \frac{y'}{z' + d}`$

Where <kbd>d</kbd> is camera distance, e.g. 3.

```js
function project([x, y, z]) {
  const dist = 3;
  const f = dist / (dist + z);
  return [
    Math.round(x * f * scale),
    Math.round(y * f * scale)
  ];
}
```
