# How to render a different element inside a React component

There are moments that you just want to share the same component style between different elements. Take a button for example, you may want to render one as an anchor at some point.

To do this, you can add a prop, in this case I'll use `tag`, that will be used to define the rendered element. Notice that with `||` (OR), we can define a fallback, in case a `tag` isn't present.

```jsx
import React from 'react'

function Button({ children, tag, ...other }) {
  const Component = tag || 'button'
  
  return (
    <Component
      className="flex items-center ..."
      {...other}
    >
      {children}
    </Component>
  )
}

export default Button
```

The best part of this code is that you don't need to make any other change between a real `button` and an anchor. You could still pass an `href` and it would be present in the final element thanks to the destructuring of `other`.

```jsx
<Button tag="a" href="#">This is a link!</Button>
```

[@estevanmaito](https://twitter.com/estevanmaito)
