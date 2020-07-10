# Testing click inside and outside React component with Enzyme

In my saga to keep 100% coverage in Windmill tests, I had to test if a click happened inside or outside my Dropdown component.

The code to handle it is pretty simple so I'll walk through it. You can [jump straight to the tests](#testing-it) if it doesn't matter to you.

```js
const dropdownRef = useRef()
function handleClickOutside(e) {
  if (dropdownRef.current && !dropdownRef.current.contains(e.target)) {
    onClose()
  }
}

useEffect(() => {
  document.addEventListener('click', handleClickOutside)
  return () => {
    document.removeEventListener('click', handleClickOutside)
  }
})
```

We start by creating a reference that will later be use on the rendered component using the `useRef` hook.

The function `handleClickOutside` is what is going to be passed later on to the event listener. It tests if the target of the current event is not the same as our ref. In other words, our ref is the element that we want to listen for events, so if the event is occurring outside it, it means that... it's a click outside. Stonks. In this case I'm executing the function `onClose` because it's passed from the parent component, but it could be anything that you want to execute here.

To finish it, inside the `useEffect` hook we register the event for a click and clean up it in the `return` (everything returned from `useEffect` will be executed on unmount).

## Testing it

As our events are registered in the `document`, it is outside the scope of React, so we cannot use Enzyme's `simulate` method here. Instead, we have to mock the listeners. I'll explain better after the code:

```js
it('should close dropdown when clicking outside it', () => {
  const map = {}
  document.addEventListener = jest.fn((e, cb) => {
    map[e] = cb
  })
  const onClose = jest.fn()
  mount(<Dropdown isOpen={true} onClose={onClose} />)

  map.click({ target: document.body })

  expect(onClose).toHaveBeenCalled()
})

it('should not close dropdown when clicking inside it', () => {
  const map = {}
  document.addEventListener = jest.fn((e, cb) => {
    map[e] = cb
  })
  const onClose = jest.fn()
  const wrapper = mount(<Dropdown isOpen={true} onClose={onClose} />)

  map.click({ target: wrapper.find('ul').getDOMNode() })

  expect(onClose).not.toHaveBeenCalled()
})
```

The secret sauce here is this:

```js
const map = {}
document.addEventListener = jest.fn((e, cb) => {
  map[e] = cb
})
...
map.click({ target: document.body })
```

We're passing to `jest.fn` the event and a callback. The event is obvious, it's a click that is being called in the last line above. The callback, is the function that our **component** is passing to the event handler, in this case `document.addEventListener('click', handleClickOutside)`.

And why do I test if `onClose` has been called?

```js
expect(onClose).toHaveBeenCalled()
```

Because this is the function that is being called inside the `handleClickOutside`, our _callback_. Got it?

In the end, it's a matter of calling the event with the desired target.

### Bonus

My component also listens for `Esc` press, so here is an example of a test for a `keydown`:

```js
it('should call onClose when Esc is pressed', () => {
  const map = {}
  document.addEventListener = jest.fn((e, cb) => {
    map[e] = cb
  })
  const onClose = jest.fn()
  mount(<Dropdown isOpen={true} onClose={onClose} />)

  map.keydown({ key: 'Esc' })

  expect(onClose).toHaveBeenCalled()
})
```

[@estevanmaito](https://twitter.com/estevanmaito)
