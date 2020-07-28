# Add SEO for a Next.js blog

If you just fell in this post, this is part of a tutorial series on creating a blog using Next.js. You can read previous articles starting [here]() or [get the base code](https://github.com/estevanmaito/temp-blog/tree/tutorial-social-images) that I'm using to start this post.

At first I thought about using `next-seo` package, but it is very rigid and incomplete in some areas, specially `@type` for json+ld (this is not a tutorial on json+ld, but rather how to implement it in a project).

## Why json+ld

A brief introduction to json+ld in case you never heard of. It's a way to structure content data of a page in a way that Google will rank you better (my definition, based on past experience).

Most people would add some `meta` to the `head` of the document and call it a day, it works, but Google prefers pages that it could show in special places (you've probably already seen search results with images, lists, search bars, etc). So if you add json+ld to your pages, Google will look at your content with different eyes.

You can find examples with images in their [structured data docs](https://developers.google.com/search/docs/data-types/article).

## Rolling our own SEO component

This is the typical SEO setup you are probably used to see: meta tags. The secret here is what we do with the `children` prop.

```js
import React from 'react'
import Head from 'next/head'

function SEO(props) {
  const { children, title, description, image, canonical } = props
  return (
    <Head>
      {/* General SEO */}
      <meta name="description" content={description} />
      <meta name="canonical" href={canonical} />
      <meta name="author" content="Estevan Maito" />
      <meta name="robots" content="index" />

      {/* Social SEO */}
      <meta property="og:title" content={title} />
      <meta property="og:description" content={description} />
      <meta property="og:url" content={canonical} />
      <meta property="og:site_name" content="Estevan Maito" />
      <meta property="og:type" content="website" />
      <meta property="og:image" content={image} />

      <meta name="twitter:card" content="summary_large_image" />

      <title>{title}</title>
      {children}
    </Head>
  )
}

export default SEO
```

Our `post` template file will end like this (almost all of it's size now come from the SEO component):

```jsx
import Nav from '@/components/Nav'
import SEO from '@/components/SEO'

function formatPath(path, replace = '') {
  return path.replace(/\.md$/, replace)
}

export default function Post(frontMatter) {
  return ({ children: content }) => {
    const canonicalURL = `https://estevanmaito.me/${formatPath(frontMatter.__resourcePath)}`
    const socialImageURL = `https://estevanmaito.me/${formatPath(
      frontMatter.__resourcePath,
      '.png'
    ).replace('blog', 'social')}`
    return (
      <>
        <SEO
          title={`${frontMatter.title} - Estevan Maito`}
          description={frontMatter.description}
          canonical={canonicalURL}
          image={socialImageURL}
        >
          <script
            type="application/ld+json"
            dangerouslySetInnerHTML={{
              __html: `{
                "@context": "https://schema.org",
                "@type": "BlogPosting",
                "url": "${canonicalURL}",
                "name": "${frontMatter.title} - Estevan Maito",
                "description": "${frontMatter.description}",
                "datePublished": "${frontMatter.datePublished}",
                "image": {
                  "@type": "ImageObject",
                  "url": "${socialImageURL}"
                },
                "author": {
                  "@type": "Person",
                  "name": "Estevan Maito",
                  "sameAs": [
                    "https://twitter.com/estevanmaito",
                    "https://github.com/estevanmaito"
                  ]
                }
              }`,
            }}
          ></script>
        </SEO>
        <Nav />
        <div className="mx-auto my-10 prose">{content}</div>
      </>
    )
  }
}
```

We pass the expected props to `SEO` but then comes the content of it, which I'll explain in more detail below:

```js
<script
  type="application/ld+json"
  dangerouslySetInnerHTML={{
    __html: `{
      "@context": "https://schema.org",
      "@type": "BlogPosting",
      "url": "${canonicalURL}",
      "name": "${frontMatter.title} - Estevan Maito",
      "description": "${frontMatter.description}",
      "datePublished": "${frontMatter.datePublished}",
      "image": {
        "@type": "ImageObject",
        "url": "${socialImageURL}"
      },
      "author": {
        "@type": "Person",
        "name": "Estevan Maito",
        "sameAs": [
          "https://twitter.com/estevanmaito",
          "https://github.com/estevanmaito"
        ]
      }
    }`,
  }}
></script>
```

Inside a `script` with `type="application/ld+json"`, we add the schema in a JSON format.

"@type": "BlogPosting" is telling the type of the current page. You'll see soon other types when I start to apply this to the home and about pages.

`url`, `name` and `description` have the same values as the `meta` tags. Note that we added a `datePublished` that is coming from the `frontMatter`. To do this, I had to add a line in the `.md` file of the post:

```md
datePublished: '2020-07-28T12:00Z'
```

The `image` property needs it's own scope, so we give it a `type` and pass the same image to `url`.

To finish it, the `author` property lets you inform a `@Person` and some related urls for `sameAs` (for a person, I usually list social networks).

This is (part of) the code for `blog/index.js`:

```jsx
const title = 'Blog - Estevan Maito'
  const description = 'Random thoughts about web development, design, databases and code in general'
  const canonical = 'https://estevanmaito.me/blog'
  const image = 'https://estevanmaito.me/social-image.png'
  return (
    <>
      <SEO title={title} description={description} canonical={canonical} image={image}>
        <script
          type="application/ld+json"
          dangerouslySetInnerHTML={{
            __html: `{
                "@context": "https://schema.org",
                "@type": "Blog",
                "url": "${canonical}",
                "name": "${title}",
                "description": "${description}",
                "image": {
                  "@type": "ImageObject",
                  "url": "${image}"
                },
                "author": {
                  "@type": "Person",
                  "name": "Estevan Maito",
                  "sameAs": [
                    "https://twitter.com/estevanmaito",
                    "https://github.com/estevanmaito"
                  ]
                }
              }`,
          }}
        ></script>
      </SEO>
      ...
```

Differences? We're now using ["@type": "Blog"](https://schema.org/Blog). And for the home and about pages, the only change (besides the value of each property like `url`, `name`, etc) is the type again: `"@type": "WebSite"`, which is documented [here](https://schema.org/WebSite).
