# How to provide a root configuration file for a package?

You've probably seen JavaScript packages that require a configuration file in the root of the project, things like `webpack.config.js`, `tailwind.config.js`, `jest.config.js`, the list is endless.

But if you're here, probably you're creating a package and want to offer your users a way to configure your package from the outside. But how do you achieve this, from inside your code that will be sitting inside `node_modules`?

I didn't know even the words I should be looking for to search, so I started to look at projects that use this type of config and found that Styleguidist uses a package for this...

Turns out that [Sindre Sorhus](https://github.com/sindresorhus) has a package for that too 😅: it's [find-up](https://www.npmjs.com/package/find-up). You pass it a file for it to look up in parent directories.

In my case, I'm creating a React library that depends on a custom property that resides inside a `tailwind.config.js` in the root of the project. So I can use it in my code like this:

```js
// resolveConfig.js
import findUp from 'find-up'

/**
 * Find a config file
 * @param {string} file - config file name
 * @return path to the file
 */
export default function resolveConfig(file) {
  let configDir
  try {
    configDir = findUp.sync(file)
  } catch (e) {
    return false
  }
  return configDir
}
```

This will find the file I'm looking for, which is then used to get the content of that file or, if it doesn't exist, the default theme:

```js
// resolveTheme.js
import resolveConfig from './resolveConfig'
import defaultTheme from '../defaultTheme'

const CONFIG_FILE = 'tailwind.config.js'

/**
 * Get the content of a file theme
 */
export default function resolveTheme() {
  const tailwindConfigPath = resolveConfig(CONFIG_FILE)
  let config

  // has tailwind config
  if (tailwindConfigPath) {
    config = require(tailwindConfigPath)
    // has custom theme
    if (config['windmill-react-ui-theme']) {
      return config['windmill-react-ui-theme']
    }
  }

  // fallback to default theme
  return defaultTheme
}
```

Inside my component I can now use it like this:

```js
import resolveTheme from '../utils/resolveTheme'

// some component
function Button() {
  const theme = resolveTheme()
  
  // use it somewhere
  classes = theme['button-base']
}
```

[@estevanmaito](https://twitter.com/estevanmaito)




