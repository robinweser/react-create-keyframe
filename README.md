# react-create-keyframe

A simple helper to craete and render keyframes on on-demand.<br />
It (optionally) utilises React's new [style hoisting feature](https://react.dev/reference/react-dom/components/style#rendering-an-inline-css-stylesheet) when available.

> **Note**: Style hoisting requires a canary version of React. Install via `react@canary`.

<img alt="npm version" src="https://badge.fury.io/js/react-create-keyframe.svg"> <img alt="npm downloads" src="https://img.shields.io/npm/dm/react-create-keyframe.svg"> <a href="https://bundlephobia.com/result?p=react-create-keyframe@latest"><img alt="Bundlephobia" src="https://img.shields.io/bundlephobia/minzip/react-create-keyframe.svg"></a>

## Installation

```sh
# npm
npm i --save react-create-keyframe
# yarn
yarn add react-create-keyframe
# pnpm
pnpm add react-create-keyframe
```

## The Gist

```tsx
import * as React from 'react'
import { createKeyframe } from 'react-create-keyframe'

const [animationName, node] = createKeyframe({
  from: {
    backgroundColor: 'red',
  },
  to: {
    backgroundColor: 'blue',
    transform: 'rotate(360deg)',
  },
})

function Component() {
  return (
    <>
      {node}
      <div
        style={{
          width: 50,
          height: 50,
          backgroundColor: 'red',
          animationName,
          animationDuration: '1s',
          animationIterationCount: 'infinite',
        }}
      />
    </>
  )
}
```

## API Reference

### createKeyframe

The only export of this package.<br />
It takes a keyframe style object and an optional nonce and returns both the keyframe name as well as a single React `<style>` node.

It converts camel case properties to dash case equivalent, but it does not add units to numbers.

| Parameter |  Type        |  Description              |
| --------- | ------------ | ------------------------- |
| keyframe  | `Keyframe`   | A keyframe style object   |
| nonce     | `string?`    | (_optional_) nonce string |

#### Keyframe

```
Partial<Record<'from' | 'to' | `${number}%`, CSSProperties>>
```

A tuple of our keyframe animation name and a single `<style>` node.

#### Example

```ts
const keyframe = {
  // equivalent to 0%
  from: {
    color: 'red',
  },
  '50%': {
    color: 'green',
  },
  // equivalent to 100%
  to: {
    color: 'blue',
  },
}

const [animationName, node] = createKeyframe(keyframe)
```

## Recipes

### Adding Units

If you want units to be added to your properties automatically, you can create your own helper or utilise existing packages such as [fela-plugin-unit](https://github.com/robinweser/fela/tree/master/packages/fela-plugin-unit#fela-plugin-unit).

> **Note**: Most fela plugins are isolated and do not require fela to be installed or used.

```ts
import { createKeyframe, Keyframe } from 'react-create-keyframe'
import unit from 'fela-plugin-unit'

// further customise by passing a config object to the plugin
const addUnits = unit()

const keyframe = {
  from: {
    fontSize: 16,
  },
  to: {
    fontSize: 20,
  },
}

createKeyframe(addUnits(keyframe))
```

## License

react-create-keyframe is licensed under the [MIT License](http://opensource.org/licenses/MIT).<br>
Documentation is licensed under [Creative Common License](http://creativecommons.org/licenses/by/4.0/).<br>
Created with ♥ by [@robinweser](http://weser.io) and all the great contributors.
