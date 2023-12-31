# Building a resizable, draggable element
How to build an element in HTML, CSS and JS which is both resizable and draggable.

In specific situations, it's useful to have an HTML element which is *both*:

1) `draggable`
2) `resizable`

That is to say, the element is not only `draggable` but it also has an easily operable `resize handle`.

We can achieve this by handling resizability via **CSS** alone:

```css
  overflow: hidden;
  resize: both;
```

and handling draggability via **CSS** and **JS**:

 - **CSS:** (`.myElement {cursor: grab;}` / `.incomeChart[data-grabbed] {cursor: grabbing;}`)
 - **JS:** (`dragResizableElement()`)

The key to combining `draggability` *with* `resizability` is this line:
```js
// RETURN IF MOUSE DOWN EVENT IS OVER THE RESIZE HANDLE
if (((resizableElement.offsetWidth - mousedownX) < 16) && ((resizableElement.offsetHeight - mousedownY) < 16)) return;
```
which separates the `resize handle` from the grabbable surface of the resizable element.

See the following markup, styling and scripting:

## HTML
```html
<main class="main">
  <div class="myElement"></div>
</main>
```

## CSS
```css
.myElement {
  position: absolute;
  top: 12px;
  left: 12px;
  width: 36%;
  aspect-ratio: 9/5;
  border: 2px solid rgb(191, 191, 191);
  border-radius: 12px;
  overflow: hidden;
  resize: both;
  cursor: grab;
}
```

## JS
```js
const dragResizableElement = function (e) {

  const resizableElement = e.currentTarget;
  resizableElement.setAttribute('data-grabbed', 'grabbed');
  const initialTop = resizableElement.style.getPropertyValue('top').replace('px', '') || 12;
  const initialLeft = resizableElement.style.getPropertyValue('left').replace('px', '') || 12;         
  const mousedownX = (e.clientX - initialLeft);
  const mousedownY = (e.clientY - initialTop);

  // RETURN IF MOUSE DOWN EVENT IS OVER THE RESIZE HANDLE
  if (((resizableElement.offsetWidth - mousedownX) < 16) && ((resizableElement.offsetHeight - mousedownY) < 16)) return;
  
  const main = document.querySelector('.main');
  const mainBoundaryX = (main.clientWidth - resizableElement.offsetWidth);
  const mainBoundaryY = (main.clientHeight - resizableElement.offsetHeight);

  const trackMouse = (e) => {
    let mousemoveX = (e.clientX - mousedownX);
    let mousemoveY = (e.clientY - mousedownY);
    resizableElement.style.setProperty('top', (((mousemoveY > 0) ? ((mousemoveY < mainBoundaryY) ? mousemoveY : mainBoundaryY) : 0) + 'px'));
    resizableElement.style.setProperty('left', (((mousemoveX > 0) ? ((mousemoveX < mainBoundaryX) ? mousemoveX : mainBoundaryX) : 0) + 'px'));
  }; 

  document.addEventListener('mousemove', trackMouse);
  document.addEventListener('mouseup', () => {
  resizableElement.removeAttribute('data-grabbed');
    document.removeEventListener('mousemove', trackMouse);
  });
}

document.querySelector('.myElement').addEventListener('mousedown', dragResizableElement);
```

_______

**N.B.** If a `resize handle` is not a requirement, the drag function may be slightly simpler:

```js
const dragElement = function (e) {

  const draggableElement = e.currentTarget;
  draggableElement.setAttribute('data-grabbed', 'grabbed');
  const initialTop = draggableElement.style.getPropertyValue('top').replace('px', '') || 0;
  const initialLeft = draggableElement.style.getPropertyValue('left').replace('px', '') || 0;         
  const mousedownX = (e.clientX - initialLeft);
  const mousedownY = (e.clientY - initialTop);

  const container = document.getElementById("container");
  const containerBoundaryX = (container.clientWidth - draggableElement.clientWidth);
  const containerBoundaryY = (container.clientHeight - draggableElement.clientHeight);

  const trackMouse = (e) => {
    let mousemoveX = (e.clientX - mousedownX);
    let mousemoveY = (e.clientY - mousedownY);
    draggableElement.style.setProperty('top', (((mousemoveY > 0) ? ((mousemoveY < containerBoundaryY) ? mousemoveY : containerBoundaryY) : 0) + 'px'));
    draggableElement.style.setProperty('left', (((mousemoveX > 0) ? ((mousemoveX < containerBoundaryX) ? mousemoveX : containerBoundaryX) : 0) + 'px'));
  }; 

  document.addEventListener('mousemove', trackMouse);
  document.addEventListener('mouseup', () => {
  draggableElement.removeAttribute('data-grabbed');
    document.removeEventListener('mousemove', trackMouse);
  });
}

document.querySelector('.draggableElement').addEventListener('mousedown', dragElement);
```

**Original Inspiration for the function immediately above:** https://jsfiddle.net/manojmcet/XXTQd/
