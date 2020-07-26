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

We will eventually come back to other properties of this file, but for now, the important bit is `layoutPath`. It points to a directory where your layouts/templates live. As we don't have this folder yet, let's start by creating it in the root of the project and inside it, create a layout called `post.js`.

```jsx
export default function Post(frontMatter) {
  return ({ children: content }) => {
    return <div>{content}</div>
  }
}
```

The default export function receives the front matter object, `frontMatter`, as a parameter. This function returns a rendering function. The rendering function receives an object that contains the page content as children that is destructured and reassigned to `content`.

We're not using `frontMatter` yet, but it will be important for SEO later. Think of front matter as the data for you Markdown files. It's a YAML block of code at the start of every file, where you add data that could be accessed later inside the `layout`.

Our `hello-world.md` will look like this:

```markdown
---
layout: 'post'
title: 'Hello World'
---

# Hello world

This is my exciting new blog.
```

The only required field is `layout: 'post'` to tell the mdx renderer which layout is going to be used for this file.

With the magic of Tailwind's `typography` plugin, we can just add two classes to our `post` layout and it will automatically style our text. Change the return to this:

```jsx
<div className="mx-auto prose">{content}</div>
```

In the next post we will show a list of posts in the home page.
