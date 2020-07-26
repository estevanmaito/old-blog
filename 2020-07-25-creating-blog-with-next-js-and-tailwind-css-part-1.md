# Creating a blog with Next.js and Tailwind CSS - Part 1

This is the most detailed a tutorial about this subject can be, because:

- It's my own blog being developed, not an example project
- I've just opened VS Code to my side monitor and have not yet even run `npm init`

Not a single detail will be left uncovered.

Here are the features I expect:

- Auto generate share images based on title
- RSS
- Dark theme
- Three main pages: home, posts, about
- Code highlighting
- Use `@tailwindcss/typography`
- Write posts in Markdown
- Best possible SEO

## Installing first dependencies

Before even starting to install, we need to initialize the git repo.

```sh
git init
```

The easiest way to start a Next.js project is using `npx`.

```sh
npx create-next-app .
```

It's the same process you would do with `create-react-app`, but for Next.js. The `.` in the end is actually the repository to create the new project, in this case, "just create it in the current directory".

It will offer to start from template, but in this case I'm going for the default.

```sh
? Pick a template » - Use arrow-keys. Return to submit.
>  Default starter app
   Example from the Next.js repo
```

After some time, we can install Tailwind CSS...

```sh
npm i tailwindcss @tailwindcss/typography
```

And create a config file

```sh
npx tailwindcss init
```

This will create a standard `tailwind.config.js`. While we're here, let's configure `purge` and `plugins`. Note that I didn't create any folder or file, but I'm already going to add what I expect to use in the future.

```js
module.exports = {
  purge: ['./pages/**/*.{js,mdx}', './components/**/*.js'],
  theme: {
    extend: {},
  },
  variants: {},
  plugins: [require('@tailwindcss/typography')],
}
```

Another required file is `postcss.config.js`. Next uses PostCSS under the hood, so we don't need to install it nor Autoprefixer.

```js
module.exports = {
  plugins: ['tailwindcss'],
}
```

Now Next knows that we are using PostCSS and Tailwind.

## Add Tailwind to Next.js

This is the last step.

I will create a `css` folder in the root of the project that, for now, will just contain one file: `tailwind.css`

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Now, Next needs to know that we want this CSS used, so inside `pages` folder, let's create an `_app.js` file with this content:

```js
import '@/css/tailwind.css'

function App({ Component, pageProps }) {
  return <Component {...pageProps} />
}

export default App
```

This is enough to setup the project, but you may notice that I imported the CSS using `@`. It comes from a config file in the root of the project, that I'm going to create now, called `jsconfig.json` which is responsible for configuring JavaScript projects in VS Code [read more here](https://code.visualstudio.com/docs/languages/jsconfig). In our case, I'll just create an alias for the project root so even if are 11 directories deep, instead of pointing to a component like `../../../../components` we can just refer to the root with `@/components`.

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./*"]
    }
  }
}
```

If you want to get the code for just this part of the tutorial, it's available in a separate branch [here](https://github.com/estevanmaito/blog/tree/tutorial-part-1).

This is it for the project setup.

[@estevanmaito](https://twitter.com/estevanmaito)
