# Generate social image for a blog with Next.js

This is the next post of the series of how to create a blog using Next.js, Tailwind CSS and Markdown. You can get the file for this script [here](https://github.com/estevanmaito/temp-blog/blob/tutorial-social-images/utils/social-image.js).

What we will do here is:

- Grab a list of all `.md` files inside the blog directory (that's where our posts live)
- Create a social image using the `title` property that is defined in the `frontMatter` of the file
- Run this on build, but don't generate images that were created on past builds

I'm pretty sure most of it could live inside the `extendFrontMatter` in our `next.config.js` file, but to make it reusable and not depend on the project, I'll create a separate script that we will run on build, this way you can easily adapt it to your needs.

Start by creating a new directory in the project's root called utils, and inside it create a file called `social-image.js`. I'm used to write tests not only to make sure the code runs, but to help me think about the overall structure and features of what I'm writing. In this case however, it's a personal project, so I'll just write some comments inside this file and create solutions for them.

```js
// grab all .md files from pages/blog
// grab all images inside public/social (this is where we will be saving our social images)
// for each file that isn't present in both folders, get the title property inside the front matter
// write this title inside the social image
// save the image to the same name as the original post file, but with the proper extension
```

This function will solve the first two problems. It accepts a directory and a file extension and return a list of file names, without extension.

```js
const fs = require('fs')
const path = require('path')
const readline = require('readline')
const renderSocialImage = require('puppeteer-social-image').default
const chalk = require('chalk')

const BLOG_DIR = 'pages/blog'
const IMAGES_DIR = './public/social'

/**
 * Get a list of file names inside the provided directory, matching an extension
 * @param {string} dir Directory path
 * @param {string} ext File extension
 * @return {array} List of file names
 */
function getFileNames(dir, ext) {
  // list of file names
  let fileNames = []

  // get all files inside the provided directory
  const files = fs.readdirSync(dir)

  files.forEach((file) => {
    // only push files ending with the provided extension
    if (path.extname(file) === ext) {
      // get only the file name, without extension
      const fileName = path.basename(file, path.extname(file))
      fileNames.push(fileName)
    }
  })

  return fileNames
}
```

With the list of files in both directories, now we can compare both arrays (they are already sorted by the system and we are using `readdirSync`, so it's an easy O(N)).

```js
// it's important that all md files inside the blog dir reffer to posts
// and you IMAGE_DIR should contain only posts social images
const blogFiles = getFileNames(BLOG_DIR, '.md')
const imageFiles = getFileNames(IMAGES_DIR, '.png')

/**
 * Get a list of posts that don't have a matching social image
 * @param {array} blogFiles List of file names for posts
 * @param {array} imageFiles List of file names for images
 * @return {array} List of file names that don't have a matching social image
 */
function getPostsToGenerateImage(blogFiles, imageFiles) {
  let postsToGenerate = []

  for (let i = 0; i < blogFiles.length; i++) {
    // this performance optimization (O(N)) relies on the fact
    // that you only have post files inside the blog folder ending with .md (index.js or other .js are ok)
    // and that you have an exclusive folder for your post images
    // so this way we can match 1:1 the files in both places
    if (imageFiles[i] !== blogFiles[i]) {
      postsToGenerate.push(blogFiles[i])
    }
  }

  return postsToGenerate
}
```

Notice that in the code above we already assigned to some `const` the values that will later be used by `getPostsToGenerateImage`. This method's name and comments above are self explanatory, but in short, it will return file names that only exist in one of the folders so we always generate images that aren't already generated.

```js
/**
 * Get the title from a .md file
 * @param {string} dir Directory path
 * @param {string} fileName File name
 * @return {Promise[]} Promise to the array containing the title and the file name it belongs to
 */
async function getTitleFromFrontMatter(dir, fileName) {
  // create a read stream for the current file
  const file = fs.createReadStream(dir + '/' + fileName + '.md')

  // create an interface to read each line in the file
  const rl = readline.createInterface({
    input: file,
    crlfDelay: Infinity,
  })

  // iterate through every line
  for await (const line of rl) {
    // the current line has `title: `
    if (line.match(/title:\s/g)) {
      // return an array
      // [0] content of the title
      // [1] file name of the current file
      return [line.match(/["'](.*?)["']/)[1], fileName]
    }
  }
}
```

Random loud thoughts: sometimes I feel stupid commenting my code. I see other people code with comments and think that it's so nice, but then I come to my code and usually my methods and variable names are exactly the same thing as the comment 🤔. Uncle Bob would be slapping me 😬.

So, this method _gets the title from the front matter_ and returns an array containing the value of the `title` property inside the file and the current file name, as it's used in the next and final method to generate the images:

```js
const posts = getPostsToGenerateImage(blogFiles, imageFiles)

/**
 * Generates social images for every post
 * @param {array} posts List of posts
 */
function generateSocialImages(posts) {
  console.log(chalk.green(`Creating ${posts.length} social images...`))

  posts.forEach((p, i) => {
    getTitleFromFrontMatter(BLOG_DIR, p).then((post) => {
      const [postTitle, postFileName] = post
      renderSocialImage({
        template: 'basic',
        templateParams: {
          title: postTitle,
          logo: socialLogo,
          color: 'black',
        },
        output: IMAGES_DIR + '/' + postFileName + '.png',
        size: 'facebook',
      })
    })
  })
}

generateSocialImages(posts)
```

To finish, we generate an image for every post that don't already have one. As always, you can find not only this script, but the complete project with this update [on GitHub](https://github.com/estevanmaito/temp-blog/tree/tutorial-social-images).

Hope it's useful to you and let me know if you improve it!
