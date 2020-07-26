# Creating a blog with Next.js and Tailwind CSS - Part 2

In the last part we setup the project, so now let's start to make the blog. First, I'll delete the `api` folder (created by Next) inside the pages directory and clean up `index.js`.

```jsx
export default function Home() {
  return <div className="container"></div>
}
```

At some point I'll add `Head` or something similar back, because we will need it for SEO and other `head` stuff. With the home empty, it's time to create a test post.

Inside`pages` folder, create a `blog` directory. Inside it there will be all `.md` files representing blog posts. The first post inside it will be `hello-world.md`.

```markdown
# Hello world

This is my exciting new blog.
```

By default, Next.js doesn't know what to do with `.md` files, so we need `next-mdx-enhanced` to extend it. Note that there is an official package called `@next/mdx`. Unless you're willing to create another library on top of it just for basic features (cough next-mdx-enhanced cough), stay away from it.

On top of it, I'll add two plugins, `rehype-slug` and `rehype-autolink-headings` that won't even need to be configured (well, it will need some styling to show up, but it's minimal) and solve one simple problem: together, they create `id`s for text headings and add links to it! This way, people that come to your blog, can share specific parts of the content or at least get a link for somewhere close to it, without having to inspect elements and add a hash to the URL **MDN**...

```sh
npm install next-mdx-enhanced rehype-slug rehype-autolink-headings
```

To make it work, we gotta create a `next.config.js` file

```js
const withMdxEnhanced = require('next-mdx-enhanced')

module.exports = withMdxEnhanced({
  layoutPath: 'layouts',
  fileExtensions: ['md'],
  rehypePlugins: [require('rehype-slug'), require('rehype-autolink-headings')],
  extendFrontMatter: {
    process: (mdxContent, frontMatter) => {},
    phase: 'prebuild|loader|both',
  },
})(/* your normal nextjs config */)
```

Continue from creating a layout


