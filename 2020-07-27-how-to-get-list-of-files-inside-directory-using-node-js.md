# How to get a list of files inside a directory using Node.js

This will get all files inside `pages/blog`, _synchronously_ (use `readdir` for async):

```js
const fs = require('fs')

const DIR = 'pages/blog'
const files = fs.readdirSync(DIR)
```

## Do something to files with specific extension

You probably don't want to just save a list of files to a variable and leave. A common case is to do something, like iterate through the files of a specific extension:

```js
let posts = []

files.forEach((file) => {
  // only push files ending with '.md'
  if (path.extname(file) === '.md') {
    posts.push(file)
  }
})
```

In this case, we iterate over the `files` array created in the first example, and make another list with files using the `.md` extension.
