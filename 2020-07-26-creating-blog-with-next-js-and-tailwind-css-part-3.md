# Creating a blog with Next.js and Tailwind CSS - Part 3

I want to use the home page for more than listing posts, so I changed my mind and now I'll create a blog index in a separate page. To get a list of the posts, we're going to need another package:

```sh
npm i babel-plugin-import-glob-array
```

In the root of our project, add a `.babelrc` file

```js
{
  "presets": ["next/babel"],
  "plugins": ["import-glob-array"]
}
```

With this plugin registered, now we can import files using a glob (eg.: `*.md`), and all imported files will be available inside an array. This is great in our case because we can now import all `.md` files inside `blog` and iterate over the generate array to create links.

Lets do this. Create an `index.js` file inside the blog directory:

```jsx
import React from 'react'
import { frontMatter as posts } from './*.md'

function Blog() {
  return (
    <main className="max-w-2xl px-4 mx-auto my-10">
      <h1 className="mb-8 text-4xl font-extrabold text-gray-900">Blog</h1>
      {posts.map((post) => (
        <h2 className="mb-2 text-xl font-bold text-gray-900">{post.title}</h2>
      ))}
    </main>
  )
}

export default Blog
```

Note that our import is looking for the `frontMatter` (that part in the head of the file, between `---`), so everything you put there, will be available here like a preview image, some description (will add one later), title, etc.

Running `npm run dev` (if you weren't already) and going to `/blog` will show a list (with one item) of blog posts.

## Navigation

> Random loud thoughts: I've never created a tutorial like this, writing at the same time as I'm developing, without a previous plan or without having tried it before. If I was just developing it, I would be testing stuff and moving a lot faster, but the process of documenting it, makes me think of every step and how someone could reproduce it instead of just, do this and do that.

Time to make it possible to move around without having to change the URL directly. Create a components directory in the root of the project and inside it add a component called... `Nav.js`

```jsx
import React from 'react'
import Link from 'next/link'

function Nav() {
  return (
    <nav className="max-w-2xl p-4 mx-auto">
      <ul className="flex justify-end space-x-6 text-gray-900">
        <li>
          <Link href="/">
            <a className="p-1">Home</a>
          </Link>
        </li>
        <li>
          <Link href="/blog">
            <a className="p-1">Blog</a>
          </Link>
        </li>
        <li>
          <Link href="/about">
            <a className="p-1">About</a>
          </Link>
        </li>
      </ul>
    </nav>
  )
}

export default Nav
```

It's a simple list with our three links (our home is still blank and about don't even exist yet, I know).

Add this new `Nav` component to our existing pages, `blog/index.js` and `layouts/post.js` (note the enclosing fragments `</>`):

```jsx
import Nav from '@/components/nav'
...
function Blog() {
  return (
    <>
      <Nav />
      <main className="max-w-2xl px-4 mx-auto my-10">
        ...
    </>
  )
}
...
```

```jsx
import Nav from '@/components/nav'

export default function Post(frontMatter) {
  return ({ children: content }) => {
    return (
      <>
        <Nav />
        <div className="mx-auto my-10 prose">{content}</div>
      </>
    )
  }
}
```

## Add description to posts

To add a description that will be used as the snippet for our post in the blog page list and later for SEO, simply add a `description` property to the front matter. The first lines of our `hello-world.md` should look like this:

```yaml
---
layout: 'post'
title: 'Hello World'
description: 'We talk about the creation of a personal site with a blog using Tailwind CSS and Next.js'
---
```

With this in place, now we're able to show the description in our blog page. This is the final look of `blog/index.js`, and I'll explain everything in the sequence:

```jsx
import React from 'react'
import Link from 'next/link'
import Nav from '@/components/nav'
import { frontMatter as posts } from './*.md'

function formatPath(path) {
  return path.replace(/\.md$/, '')
}

function Blog() {
  return (
    <>
      <Nav />
      <main className="max-w-2xl px-4 mx-auto my-10">
        <h1 className="mb-8 text-4xl font-extrabold text-gray-900">Blog</h1>
        <div className="space-y-6">
          {posts.map((post) => (
            <Link key={formatPath(post.__resourcePath)} href={formatPath(post.__resourcePath)}>
              <a className="block">
                <h2 className="mb-2 text-xl font-bold text-gray-900">{post.title}</h2>
                <p className="text-gray-700">{post.description}</p>
              </a>
            </Link>
          ))}
        </div>
      </main>
    </>
  )
}

export default Blog
```

We have a new `Link` import that's coming from Next and this little function:

```js
function formatPath(path) {
  return path.replace(/\.md$/, '')
}
```

It is responsible for removing the extension from the file path that is coming with the `frontMatter`, specifically the property `__resourcePath`. You can see below how it is used (basically to create a slug for the links) inside the new post snippet.

Everything now lives under a `div` with some spacing and all the data is coming from the `frontMatter` (that is inside `post`).

```jsx
<div className="space-y-6">
  {posts.map((post) => (
    <Link key={formatPath(post.__resourcePath)} href={formatPath(post.__resourcePath)}>
      <a className="block">
        <h2 className="mb-2 text-xl font-bold text-gray-900">{post.title}</h2>
        <p className="text-gray-700">{post.description}</p>
      </a>
    </Link>
  ))}
</div>
```

If you want to see the code for this post, take a look [here](). The next tutorial will be the last one of this series, where we will build a home and about page.

But don't freak out, I will still document the process to improve SEO, create social images, etc., but in their own posts, as they are more of generic solutions.
