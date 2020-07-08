Working on a modal component for Windmill React UI, I found a problem while trying to render my component with Next.js. I don't recall the exact error message, but was something along the lines of 'document is not defined'. The typical error when you're trying to use something that is DOM exclusive while still in the server, like `document` or `window`.

I tried following the [`with-portals` example](https://github.com/vercel/next.js/tree/canary/examples/with-portals) on Next's repo, but it would give me this error: 'Target container is not a DOM element.'

Inspecting it, I found that the `ref` wasn't being updated and it would return always `null`. More investigation could find the issue, but I found another way of making it work without depending on a new component (my case is simple so I don't see a problem for the solution living inside the only place that needs it).

```jsx
import { createPortal } from 'react-dom'
...
const [mounted, setMounted] = useState(false)

useEffect(() => {
  setMounted(true)
}, [])

const modalComponent = <span>the component to be rendered using the Portal</span>

return mounted ? createPortal(modalComponent, document.body) : null
...
```

It's that simple. The `[]` inside the `useEffect` will make sure that this only run on mount, so as soon as it is mounted, the `mounted` state is updated and the portal is created. It fixes the problem because `useEffect` is only triggered on client.

Client mounts won't be affected while server is fixed.
