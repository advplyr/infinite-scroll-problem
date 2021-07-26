# Infinite Scroll Problem

This is an example solution to the problem I'm now calling, "the infinite scroll problem".

## Basic Solution

Start with enough widgets to fill the page and continue loading more every time the user scrolls to the bottom. This solution uses the [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API).

[View Basic Demo](https://advplyr.github.io/infinite-scroll-problem/)

---
### What is the problem then?

After loading a few thousand widgets, the page gets very slow. The demo uses a hover event for each widget to make the slow performance more noticeable.

---
## Advanced Solution

Assumes a fixed # of widgets possible. Uses an empty `div` inside the scroll container with the total height if all widgets were loaded.

The widgets will be rendered inside an **absolute** positioned container with a `top` value of the current scroll position minus the height of one row.

Using a `scroll` event listener, if the user scrolls +/- one row then update the visible widgets.

[View Advanced Demo](https://advplyr.github.io/infinite-scroll-problem?v=advanced)


