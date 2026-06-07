## Code Review Exercise

Write your code review here in markdown format. 

### Issue #1: CSS — .form-button

The issue, why this is an issue, and the solution:

There are three issues with the `.form-button` CSS class applied to the submit and reset input elements.

- `display: block` does not behave reliably on `<input type="submit">` and `<input type="reset">` elements. Input elements are replaced elements and ignore certain layout properties. `display: inline-block` should be used instead.
- `box-sizing` is not set. Without `box-sizing: border-box`, the padding adds to the declared width of 200px, making the button wider than intended.
- The `transition` property is placed on the `:hover` rule instead of the base rule. This means the transition animates on hover-in but snaps back instantly on hover-out, which is inconsistent and unintended behavior.

Initial code:

```css
.form-button {
  width: 200px;
  background-color: var(--light-blue);
  color: black;
  border: 1px solid var(--light-blue);
  font-family: inherit;
  font-size: 1rem;
  display: block;
  padding: 10px;
  margin-bottom: 20px;
}
.form-button:hover {
  background-color: var(--darker-blue);
  color: var(--white);
  border: 1px solid var(--darker-blue);
  cursor: pointer;
  transition: 0.25s;
}
```

Updated code:

```css
.form-button {
  width: 200px;
  box-sizing: border-box;
  background-color: var(--light-blue);
  color: black;
  border: 1px solid var(--light-blue);
  font-family: inherit;
  font-size: 1rem;
  display: inline-block;
  padding: 10px;
  margin-bottom: 20px;
  transition: 0.25s;
}
.form-button:hover {
  background-color: var(--darker-blue);
  color: var(--white);
  border: 1px solid var(--darker-blue);
  cursor: pointer;
}
```

## Issue #2: Load New Cats Facts Loading Indicator Bug

### The Issue

The loading indicator is hidden after the initial fetch request and never properly reappears when the user clicks the **Load New Cats Facts** button.

The current implementation uses:

```javascript
finally {
  const loading = document.querySelector('.loading-container');
  loading.setAttribute('class', 'display-none');
}
```

This replaces all existing classes on the loading element with `display-none`.

### Why This Is an Issue

When the user clicks **Load New Cats Facts**, the loading spinner should be displayed while new cat facts are being fetched. Since the entire class list was replaced with `display-none`, the loading indicator remains hidden and users receive no visual feedback that a request is in progress.

This creates a poor user experience and makes the application appear unresponsive.

### Solution

Use `classList.add()` and `classList.remove()` to toggle the visibility of the loading indicator instead of overwriting the entire class attribute.

Also, added `loadingContainer.replaceChildren()` since every click on Load New Cat Facts creates another loader image. After several requests, multiple hidden loader GIFs will exist in the container.


Updated code:

```javascript
const loadingContainer = document.querySelector('.loading-container');

loadingContainer.replaceChildren();
loadingContainer.classList.remove('display-none');

try {
  const response = await fetch('https://catfact.ninja/facts?limit=10');
  const data = await response.json();

  // process cat facts
}
finally {
  const loadingContainer = document.querySelector(".loading-container");
  loadingContainer.classList.add('display-none');
}
```

This allows the loading indicator to appear every time new cat facts are requested and disappear once the request is complete.

---

## Issue #3: Form Submit and Reset Buttons Outside the Form

### The Issue

The submit and reset buttons are located outside the `<form>` element.

Current code:

```html
<form id="RequestInfo" class="content-container form">
  ...
</form>

<div class="form-buttons-container">
  <input class="form-button" type="submit" value="submit" />
  <input class="form-button" type="reset" value="reset" />
</div>
```

### Why This Is an Issue

Submit and reset controls only work automatically when they are associated with a form. Since these buttons are outside the form element, clicking them will not properly submit or reset the form fields.

This breaks expected HTML form behavior and can prevent users from interacting with the form correctly.

### Solution

Move the buttons inside the form element.

Updated code:

```html
<form id="RequestInfo" class="content-container form">
  ...

  <div class="form-buttons-container">
    <input class="form-button" type="submit" value="Submit" />
    <input class="form-button" type="reset" value="Reset" />
  </div>
</form>
```

This ensures that the submit and reset functionality works as intended.
