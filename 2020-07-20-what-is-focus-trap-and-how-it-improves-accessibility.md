# What is a focus trap and how it improves accessibility

TL:DR; Focus trap is a technique to keep focus inside an element.

The examples I'll show you use React, but it's not about the implementation but the solution. If you're interested in a JavaScript-only approach, I did it in Windmill Dashboard HTML ([focus-trap.js](https://github.com/estevanmaito/windmill-dashboard/blob/master/public/assets/js/focus-trap.js)).

There's a working example of it in use with a modal on [Windmill React UI docs](https://windmillui.com/react-ui/components/modal).

## Why should you care for focus?

If you open the example modal above, you'll immediately notice that the close ("x") button is focused, and no matter how many times you press Tab, it will never leave the modal. If it wasn't using a focus trap, as soon as you've reached the last element, the next focused component would be something outside of the modal.

Another important thing happening here (but not dependant on the focus trap itself), is that whenever you close the modal (using Esc, clicking outside of it or using a button), focus goes back to the element responsible for opening the focus trap in the beginning.

This is great for people navigating with keyboard or a screen reader (I actually developed it using [NVDA](https://www.nvaccess.org/), a free screen reader for Windows).

Good places to implement focus traps are: forms, dialogs, dropdowns, menus, etc. Basically everywhere you would expect someone to take an action to leave the current interface and it wouldn't just appear from nowhere and disrupt navigation.

## Using focus traps in React

[react-focus-lock](https://www.npmjs.com/package/react-focus-lock) makes it so easy to use focus traps, that I fell silly explaining it 🤭

You just need to install the package above, import and wrap the component you want focus trapped inside:

```jsx
import FocusLock from 'react-focus-lock'
...

<FocusLock returnFocus>
...
</FocusLock>
```

Notice the `returnFocus` prop. It is responsible for returning the focus back to the component that was responsible for opening the trap. You wouldn't need this for a form, for example, that is already in your page, but it's great for things that are hidden at first and depend on interaction to show, like a modal.

Good reads:

- [General guidelines on keyboard navigation by MDN](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Keyboard-navigable_JavaScript_widgets#General_Guidelines)
- [A CSS Approach to Trap Focus Inside of an Element by CSS Tricks](https://css-tricks.com/a-css-approach-to-trap-focus-inside-of-an-element/)
- [Focus Trapping for Accessibility (A11Y) by Rahul Kumar](https://medium.com/@im_rahul/focus-trapping-looping-b3ee658e5177)

[@estevanmaito](https://twitter.com/estevanmaito)
