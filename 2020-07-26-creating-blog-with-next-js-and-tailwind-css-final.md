# Creating a blog with Next.js and Tailwind CSS - Final

In this final post we will add a home and about page, and apply some styles to our post heading links. In the end, you'll get a pretty decent starter, not too much opinionated, but you can still follow along the next posts and plug those improvements here.

Inside `pages/index.js` (note that this is the root index, inside pages!):

```jsx
import Nav from '@/components/Nav'

export default function Home() {
  return (
    <>
      <Nav />
      <main className="max-w-2xl px-4 mx-auto my-10 prose">
        <h1>I'm Estevan Maito</h1>
        <p>Full stack developer, designing with code.</p>
      </main>
    </>
  )
}
```

This is a very minimal landing page, but it's great to add your character to it. The same goes for the About page (`pages/about.js`):

```jsx
import Nav from '@/components/Nav'

export default function About() {
  return (
    <>
      <Nav />
      <main className="max-w-2xl px-4 mx-auto my-10 prose">
        <h1>About me</h1>
        <p>I'm Estevan Maito, full stack developer with 7 years of experience.</p>
        <p className="space-x-4">
          <a href="https://github.com/estevanmaito">Github</a>
          <a href="https://twitter.com/estevanmaito">Twitter</a>
        </p>
      </main>
    </>
  )
}
```

## Add links to post headings

You may remember that in the beginning of this series, we added two Rehype plugins, `slug` and `autolink-headings`. Turns out that in the mean time I learnt that you can add `options` to the plugin so we wouldn't even need to touch CSS (I was thinking of placing an icon that whenever you hover, it shows the icon so you can grab the link), as we can tell it to simply wrap the headings in a link.

In `next.config.js`, make these changes to `rehypePlugins`:

```js
rehypePlugins: [
  require('rehype-slug'),
  [require('rehype-autolink-headings'), { behavior: 'wrap' }],
],
```

`behavior: 'wrap'` is going to do the trick here. But now we have a problem: `prose` (from the typography plugin) adds link styles to the headings. To solve it, we need to override this inside `tailwind.config.js`:

```js
module.exports = {
  purge: ['./pages/**/*.{js,mdx}', './components/**/*.js'],
  theme: {
    extend: {},
    typography: {
      default: {
        css: {
          'h1 > a, h2 > a, h3 > a, h4 > a': {
            textDecoration: 'none',
          },
        },
      },
    },
  },
  variants: {},
  plugins: [require('@tailwindcss/typography')],
}
```

See the code for this tutorial [here](https://github.com/estevanmaito/temp-blog/tree/tutorial-final)

