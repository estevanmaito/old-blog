# Apply box-shadow just to bottom (without side leaks)

I was fighting with a header with higher `z-index` than a sidebar. The problem was that the header had a shadow that would bleed over the sidebar.

So I added this to Tailwind's config:

```js
boxShadow: {
  bottom: '0 5px 6px -7px rgba(0, 0, 0, 0.6), 0 2px 4px -5px rgba(0, 0, 0, 0.06)',
},
```

That translates to this:

```css
.shadow-bottom {
  box-shadow: 0 5px 6px -7px rgba(0, 0, 0, 0.6), 0 2px 4px -5px rgba(0, 0, 0, 0.06);
}
```

To understand why this apply a shadow only to bottom, we should know what each of the `box-shadow` values are doing (the rule above applies two shadows, but I'll explain only the first one, as the same applies to the second):

1. the x-offset (being 0 means that the shadow is centered);
2. the y-offset (a positive number means it moves down)
3. the blur radius (the size of the blur)
4. the spread radius (if the shadow should grow or shrink)
5. the color

This is an over-simplified explanation, but its enough. If you want more details, take a look at [MDN docs](https://developer.mozilla.org/en-US/docs/Web/CSS/box-shadow).

So let's take a look again at our (simplified) code:

```css
box-shadow: 0 5px 6px -7px rgba(0, 0, 0, 0.6);
```

We don't need the shadow to grow to the sides, so x = 0; we just want a bottom shadow, so y = 5px; the trick here is to set the fourth value (spread) to the negative value of the third (blur), plus one.

This way, we are growing our shadow with the blur (6px), but shrinking it with the spread (-7px). As we already positioned it down (y = 5px), all other sides are now smaller than the element, except for the bottom.

[@estevanmaito](https://twitter.com/estevanmaito)
