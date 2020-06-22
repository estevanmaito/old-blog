# Accessible navigation using React Router

This is a quick wrap of my research for creating accessible navigation for Windmill Dashboard (a React aplication) using React Router.

## The problem

As React Router manipulates the native browser to create an illusion of navigation, if you go from one route to another using a screen reader (in my case NVDA - free, Windows), it doesn't announce anything. There's just silence.

## The solution

At the time of writing this article, there's a 3 year discussion going on [React Router](https://github.com/ReactTraining/react-router/issues/5210), so we will need to implement it by ourselves.

Looks like we need this piece of code to appear on every page, and it needs to be updated on every location change:

```html
<span aria-live="polite" aria-atomic="true">
  Navigated to <page> page.
</span>
```

`aria-live` provides a way to programmatically expose dynamic content changes in a way that can be announced by assistive technologies ([MDN](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions)). `aria-atomic` is used to set whether or not the screen reader should always present the live region as a whole, in other words, `true` tells the screen reader to read again the whole page ([MDN](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions#Advanced_live_regions)).

So let's put it inside a component, that I will call `AccessibleNavigationAnnouncer.js`:

```jsx
import React from 'react'

function AccessibleNavigationAnnouncer({page}) {
  return (
    <span aria-live="polite" aria-atomic="true">
      Navigated to {page} page.
    </span>
  )
}

export default AccessibleNavigationAnnouncer
```

Now we need to find a way to make it update on every location change. I was thinking of creating a new Context, and every page would connect to it and receive/dispatch changes. But it seems a bit overkill. Instead, I will use this component in the root router and update itself on changes.

```jsx
import React, { useState, useEffect } from 'react'
import { useLocation } from 'react-router-dom'

function AccessibleNavigationAnnouncer() {
  // the message that will be announced
  const [message, setMessage] = useState('')
  // get location from router
  const location = useLocation()

  // only run this when location change (note the dependency [location])
  useEffect(() => {
    // ignore the /
    if (location.pathname.slice(1)) {
      // make sure navigation has occurred and screen reader is ready
      setTimeout(() => setMessage(`Navigated to ${location.pathname.slice(1)} page.`), 500)
    } else {
      // in my case, / redirects to /dashboard, so I found it better to
      // just ignore the / route
      setMessage('')
    }
  }, [location])

  return (
    // .sr-only comes from Tailwind CSS and makes sure it will only be visible for SRs
    <span className="sr-only" role="status" aria-live="polite" aria-atomic="true">
      {message}
    </span>
  )
}

export default AccessibleNavigationAnnouncer
```

My App looks like this:

```jsx
function App() {
  return (
    <>
      <Router>
        {* Here we have our announcer. Note: useLocation will only work inside a Router *}
        <AccessibleNavigationAnnouncer />
        <Switch>
          <Route path="/login" component={Login} />
          <Route path="/create-account" component={CreateAccount} />
          <Route path="/forgot-password" component={ForgotPassword} />

          {/* Place new routes over this */}
          <Route path="/" component={Layout} />
        </Switch>
      </Router>
    </>
  )
}
```

Hope it helps you create better apps.

[@estevanmaito](https://twitter.com/estevanmaito)

#### Good readings

- [Accessible React Router navigation with ARIA Live Regions and Redux](https://almerosteyn.com/2017/03/accessible-react-navigation)
- [Accessible Routing in React](https://timwright.org/blog/2019/03/23/accessible-routing-in-react/)
