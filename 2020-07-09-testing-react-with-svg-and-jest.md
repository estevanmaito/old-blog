# Testing React with SVG and Jest

Today I fell into a problem that I was already expecting: Jest cannot render SVGs. Actually, it cannot render anything outside JS. This is the error message:

```sh
Jest encountered an unexpected token

This usually means that you are trying to import a file which Jest cannot parse, e.g. it's not plain JavaScript.
```

I needed to add an SVG to a Button inside my tests and check if it was rendered in the right place. I first tried importing it as a `ReactComponent`:

```js
import { ReactComponent as HeartIcon } from './heart.svg'
```

Also tried importing directly, with no success:

```js
import HeartIcon from './utils/heart.svg'
```

It costs nothing to try... but now to the real solution: `npm i -D jest-svg-transformer babel-jest` (or `npm install --save-dev jest-svg-transformer babel-jest`). [Docs here](https://www.npmjs.com/package/jest-svg-transformer).

Add this to `jest.config.js`

```js
transform: {
  '^.+\\.jsx?$': 'babel-jest',
  '^.+\\.svg$': 'jest-svg-transformer',
},
```

And now the test runs with success.

```js
import React from 'react'
import { mount } from 'enzyme'
import Button from '../src/Button'
import HeartIcon from './utils/heart.svg'

describe('Icon', () => {
  it('should contain an svg', () => {
    const wrapper = mount(
      <Button>
        <HeartIcon />
      </Button>
    )

    expect(wrapper.find('svg')).toBeDefined()
  })
})
```

[@estevanmaito](https://twitter.com/estevanmaito)
