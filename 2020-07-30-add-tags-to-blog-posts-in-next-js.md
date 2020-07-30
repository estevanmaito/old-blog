# Add tags to blog post in Next JS

In this post, we will add tags to blog posts (that's easy) and generate one page for each tag that will list all blog posts using that tag (not so easy).

To add tags to posts, we'll need to add just one line of code to the front matter of our post, listing the tags. If you just dropped here, this is part of a series to create a blog using Next JS and our posts are written in Markdown. You can read the first post [here]().

```md
---
layout: 'post'
title: 'Great post about Next SEO'
description: 'We talk about the creation of a personal site with a blog using Tailwind CSS and Next.js'
datePublished: '2020-07-28T12:00Z'
tags: [seo, mongo, css, tailwind, next]
---

# Hello world

This is my exciting new blog.
```

This is an example post and the important line is the `tags` one. We're just passing it an array of tags. That simple. Time to make it show in the blog listing.

Inside `blog/index.js` ([source code here]()) we already have access to all post properties inside `frontMatter`, and in this context we simply renamed it to `posts`.

```jsx
{sortedPosts.map((post) => (
  <div>
    {/* code related to the post title */}
    <div className="space-x-4">
      {post.tags.map((tag) => (
        <span
          className="inline-block px-2 text-sm leading-5 bg-gray-200 rounded-full"
          key={tag}
        >
          <Link href={`/tags/${tag}`}>
            <a>{tag}</a>
          </Link>
        </span>
      ))}
    </div>
    {/* more code here */}
```

Now we have the list of tags rendered next to the title. Notice that we're also placing a link to the tag, but currently it leads to nowhere. Let's fix it.

I've created in the root of the project a folder called `api` and inside it a file called `index.js` with the content:

```js
import { frontMatter as posts } from '../pages/blog/*.md'

export async function getTags() {
  return posts.reduce((acc, cur) => [...new Set([...acc.concat(cur.tags)])], [])
}

export async function getPostsByTag(tag) {
  return posts.filter((post) => post.tags.indexOf(tag) >= 0)
}
```

`getTags` iterates through all posts and returns a **unique** list of tags (that's what the code inside `reduce` is doing), while `getPostsByTags` will return all posts related to a specific tag.

Those utilities functions will be useful now inside our tags page, but there's a gotcha here. We're going to create a [dynamic route](https://nextjs.org/docs/routing/dynamic-routes).

Inside `pages` folder, create another folder called `tags` and inside it a file called `[tag].js`. With this structure we'll get a URL like `/tags/css`, and in this case, `css` would be available inside our page as a `param`.

This is the content of `[tag].js`:

```jsx
import React from 'react'
import Link from 'next/link'
import { getTags, getPostsByTag } from '@/api/index'

function formatPath(path) {
  return path.replace(/\.md$/, '')
}

function Tag(props) {
  const { posts, tag } = props
  return (
    <>
      <h1>Tag: {tag}</h1>
      <p>{posts.length} posts found</p>
      <div>
        {posts.map((post) => (
          <Link href={`/${formatPath(post.__resourcePath)}`}>
            <a>
              <p key={post.title}>{post.title}</p>
            </a>
          </Link>
        ))}
      </div>
    </>
  )
}

export async function getStaticPaths() {
  const tags = await getTags()
  const paths = tags.map((tag) => ({
    params: { tag },
  }))
  return { paths, fallback: false }
}

export async function getStaticProps(ctx) {
  return {
    props: { posts: await getPostsByTag(ctx.params.tag), tag: ctx.params.tag },
  }
}

export default Tag
```

We're exporting two async functions:

- `getStaticPaths`: will make use of our `getTags` API utility, and is used to tell Next which routes it needs to generate (don't forget we're creating a static site) at build time. Read more in their [docs](https://nextjs.org/docs/basic-features/data-fetching#getstaticpaths-static-generation).
- `getStaticProps`: will pass `props` to our page, in this case, `posts`, coming from the filtered list that we provide with the `getPostsByTag` utility, and `tag` that is simply maping to the param of the url (because we named the file this way: `[tag].js`).

It doesn't have any styles but it's a great starting point and already connects all points: tags listed in your posts now have a URL to land, and each tag contains a list of it's posts.
