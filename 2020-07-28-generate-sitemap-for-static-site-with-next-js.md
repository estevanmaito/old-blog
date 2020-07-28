# Generate a sitemap for a static site using Next.js

In my blog I'm using the package [nextjs-sitemap-generator](https://github.com/IlusionDev/nextjs-sitemap-generator):

```sh
npm i -D nextjs-sitemap-generator
```

Its documentation is a little bit confusing, but a very basic sitemap can be generated this way. Create a script somewhere in your application, in my case it is inside `utils/sitemap.js` (hence the `..` for the `pagesDirectory`).

```js
const sitemap = require('nextjs-sitemap-generator')
const chalk = require('chalk')

const targetDirectory = 'public'

sitemap({
  baseUrl: 'https://estevanmaito.me',
  pagesDirectory: __dirname + '\\..\\pages',
  ignoreIndexFiles: true,
  targetDirectory: targetDirectory,
  pagesConfig: {
    '/blog': {
      priority: '1',
      changefreq: 'daily',
    },
  },
})

console.log(chalk.green(`Sitemap generated and available at /${targetDirectory}`))
```

In my case I wanted wanted a specific `priority` and `changefreq` from the `/blog` route, so I added it under `pagesConfig`. Note: `chalk` is used here only to add colors to the terminal messages and is completely optional (as is the `console.log`).

To run generate the sitemap, I added a script under `scripts` inside `package.json` called `prebuild`, that is called before `build`:

```json
"prebuild": "node utils/sitemap.js",
```

It will generate the sitemap inside `public` folder (but you can change it).
