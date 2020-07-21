# How to minify and prefix Tailwind CSS

In this guide I assume you already have a Tailwind CSS project (the basic `tailwind.config.js` is enough).

Purging your Tailwind code will get you just the CSS classes that you're really using, but it won't add broswer specific prefixes nor minify your code.

To add minification and browser prefixes, you'll need to install these packages:

```sh
# npm install --save-dev does the same as npm i -D
npm i -D postcss-cli autoprefixer cssnano
```

PostCSS is a preprocessor and will run Tailwind (with you config file), prefix everything with Autoprefixer and minify it with CSSNano.

For all of this to work together, you'll need a file called `postcss.config.js`:

```js
module.exports = {
  plugins: [
    require('tailwindcss'),
    require('autoprefixer'),
    require('cssnano')({
      preset: 'default',
    }),
  ],
}
```

This will give you just the CSS you need, browser prefixes and minification. In your `package.json` you can create a build script that will run PostCSS:

```js
"build:css": "postcss tailwind.css -o output.css"
```

You can use Tailwind with other preprocessor and [find other setups in the documentation](https://tailwindcss.com/docs/using-with-preprocessors/).

[@estevanmaito](https://twitter.com/estevanmaito)
