# Improvig developer experience

For the past months, I've been really interested in accessibility, mostly in the sense of disabled people, which I think it's what most people do.

But recently I found that I can apply a lot of concepts to my daily code, to improve my own experience, and realised that accessibility is, in a way, just a word for: making everybody's life easier.

With this in mind, I started to proactively think: is there something I can do with this code, that would make the life of developers using it easier?

And generally, the answer is yes. Of course there are thousands of other approaches and probably better versions of what I'll show next, but this is what I've been including in my libraries.

`tailwindcss-multi-theme` is a plugin for Tailwind CSS that extends the default config to allow theming. It runs in the terminal, usually in a context with other plugins. It expects only one property, but it must have be an array of strings.

I could simply create my code, and if somebody gives that property a string, an error would be thrown. Here lies the problem that I would create for a lot of people. What you, as a developer running a build script that executes, for example, PostCSS and Tailwind, would do seeing something like `map is not a function`?

Even if you read the documentation of `multi-theme` and know what to change, I already stole you 10 seconds until you realised what was going on. But most people would start to look for the problem and a portion could land in other repos trying to figure what this problem is, until someone 6 days later respond to that issue with "I had this problem, it has nothing to do with this library, ...".

My code expects very little from the developer, compared to what is generated, but this is what I thought would save you some time:

```js
// you forgot to create the property
if (!themeVariants) {
  throw new Error('tailwindcss-multi-theme expects a themeVariants property in theme.')
}

// you forgot the type of the property
if (!Array.isArray(themeVariants)) {
  throw new Error('tailwindcss-multi-theme expects themeVariants to be an Array.')
}

// you created everything, but didn't finish it
if (themeVariants.length === 0) {
  throw new Error(
    'tailwindcss-multi-theme found themeVariants but it is empty. Pass it a list of strings or remove it.'
  )
}
```

I'm telling you what is wrong (something is empty), where (multi-theme plugin), and if possible, how to solve (pass it this values).

Another example, now of Windmill React UI, an accessible components library that I'm developing right now (and inspired me to write this). This library exports a `Button` component and I'm trying all I can to bake accessibility into it, but sometimes it's expected that the developer does this, like when creating an icon button.

The problem with icon buttons is that most of the time developers forget to give it an accessible label, and screen reader users will just hear "button". I could put an alert into the docs and say "hey, don't forget to use 'aria-label' when creating icon buttons!".

Another option, is to release you of thinking too much about what you should be doing in this case, and use the same approach as before. So I created this utility:

```js
// utils/warning.js
/**
 * Helper function to show warning in the console during development
 * @param {expression} assert - assertion to test
 * @param {string} scope - location of the warning, usually a component
 * @param {string} message - instructions about the warning
 */
function warn(assert, scope, message) {
  if (process.env.NODE_ENV !== 'production') {
    if (assert) {
      if (window.console.warn) {
        console.warn(`Windmill [${scope}]: ${message}`)
      } else {
        console.log(`Windmill [${scope}]: ${message}`)
      }
    }
  }
}

export default warn
```

That I can call inside my component:

```js
// inside Button.js component
warn(
  size === 'icon' && other['aria-label'],
  'Button',
  'You are using an icon button, but no "aria-label" attribute was found. Add an "aria-label" attribute to work as a label for screen readers.'
)

// prints [Windmill Button]: You are using an icon button, but no "aria-label" attribute was found. Add an "aria-label" attribute to work as a label for screen readers.
```

You know which library and component is failing, why it's failing, how to fix it and why you should fix it.
