# CSS

## `clamp()` - css-function

> Sets size of the text

- `font-size: clamp(min-ize, preferred-size, max-size)`:

Responsive size for text. Very handy for responsive typography. Depending on the Viewport the text size will be set from **min** over **preferred** to **max**.

**Examples**:

- `font-size: clamp(1rem, 2.5vw, 2rem);`
- `width: clamp(200px, 40%, 400px);` - handy to set `width` of the `<img>`

## `display: grid:`

> Sets own grid-based layout

**Examples**:

```css
.grid {
  display: grid;
  grid-template-columns: repeat(4, 50px);
  grid-auto-rows: 50px;
  gap: 1rem; /* set spacing between flex-box elements */
  row-gap: 1rem; /* set gap only for rows */
  column-gap: 1rem; /* sets gap only for columns
}
```

## `display: flex;`

> Sets element to align it's children flexible

**Examples**:

```css
.flex {
  display: flex;

  flex-direction: column: /* align children elements: possible values: column, column-reverse, row, row-reverse */
  flex-wrap: wrap; /* if children should be align in one column despite set size; possible values: wrap, nowrap */
  flex-flow: row wrap; /* shorthand for both flex-direction and flex-wrap */
  justify-content: center; /* how to align the flex items; possible values: center, flex-start, flex-end, space-around, space-between */
  align-items: center; /* how vertically to align the flex items; possible values: center, flex-start, flex-end, stretch, baseline */
  align-content: space-content; /* how to align flex items in the line; possible values: space-between, space-around, stretch, center, flex-start, flex-end */

  column-gap: 1rem;
}
```

Perfect centering (vertically and horizontally)

```css
.flex {
  display: flex;
  height: 300px;
  justify-content: center;
  align-items: center;
}
```

## `column-count: N`

> Divide element in Columns

**Examples**:

Divide element into to columns

```css
.column {
  column-count: 2;
  column-gap: 1rem;
}
```
