# How to add custom fonts to Tailwind CSS

In this tutorial I'll use Google fonts, but the process is the same for every approach.

Choose your approach to add the font to the project. In my case, I'll use a link in the `head` but you could use `@import`, `@font-face`, it doesn't matter, as long as it's available to be used.

```html
<link href="https://fonts.googleapis.com/css2?family=Inter&display=swap" rel="stylesheet"> 
```

Now, Tailwind needs to know that you want to use this font. To do this, there are two approaches: extending an existing `fontFamily` or creating a new one.

## Extending `fontFamily`

Here I'm going to add 'Inter' to the `sans-serif` stack, which is the dafault picked by browsers. Sans font styles in Tailwind live under the `font-sans` class and can be extended in this way:

```js
// tailwind.config.js
const defaultTheme = require('tailwindcss/defaultTheme')

module.exports = {
  purge: [],
  theme: {
    extend: {
      fontFamily: {
        sans: ['Inter', ...defaultTheme.fontFamily.sans],
      },
    },
  },
  variants: {},
  plugins: [],
}
```

This line does the trick:

```js
sans: ['Inter', ...defaultTheme.fontFamily.sans],
```

It is important to destructure the current value of `fontFamily.sans` so you still support the default stack as a fallback (not _required_, but a good practice).

## Creating a new font stack (utility)

In this approach, you will create a new utility alongside the default `font-sans`, `font-mono` and `font-serif` [see docs](https://tailwindcss.com/docs/font-family/).

```js
// tailwind.config.js
module.exports = {
  purge: [],
  theme: {
    fontFamily: {
      custom: ['Inter', 'sans-serif'],
    },
  },
  variants: {},
  plugins: [],
}
```

Note that we're not **extending** `theme` anymore. Doing this, the utility class `font-custom` will be available for you to use in your code.

With this last approach, you could still make use of the fallback as we did earlier, but I'll leave it to you.

Good reads:

- [Tailwind font family docs](https://tailwindcss.com/docs/font-family/)

[@estevanmaito](https://twitter.com/estevanmaito)
