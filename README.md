# react-use-keyframe

A simple hook to render keyframes inline and on-demand utilising React's new style hoisting functionality when available
.
<img alt="npm version" src="https://badge.fury.io/js/react-use-keyframe.svg"> <img alt="npm downloads" src="https://img.shields.io/npm/dm/react-use-keyframe.svg"> <a href="https://bundlephobia.com/result?p=react-use-keyframe@latest"><img alt="Bundlephobia" src="https://img.shields.io/bundlephobia/minzip/react-use-keyframe.svg"></a>

## Installation

```sh
# npm
npm i --save react-use-keyframe
# yarn
yarn add react-use-keyframe
# pnpm
pnpm add react-use-keyframe
```

## The Gist

```tsx
import * as React from 'react'
import { useKeyframe } from 'react-use-keyframe'

function Spinner() {
  const [animationName, node] = useKeyframe()

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

> **Note**: This is, of course, a simplified version and you most likely render custom components to handle labelling, error messages and validation styling.<br />For such cases, each field also exposes a `props` property that extends the `inputProps` with non-standard HTML attributes.

## API Reference

### useForm

The core API that connects the form with a zod schema and returns a set of helpers to manage the state and render the actual markup.

| Parameter          |  Type                                        | Default                    |  Description                                       |
| ------------------ | -------------------------------------------- | -------------------------- | -------------------------------------------------- |
| schema             | ZodObject                                    |                            | A valid zod object schema                          |
| formatErrorMessage |  `(error: ZodIssue, name: string) => string` | `(error) => error.message` | A custom formatter that receives the raw zod issue |

```ts
import { z } from 'zod'

const Z_Input = z.object({
  name: z.string().optional(),
  email: z.string().email(),
  // we can also pass custom messages as a second parameter
  password: z
    .string()
    .min(8, { message: 'Your password next to have at least 8 characters.' }),
})

type T_Input = z.infer<typeof Z_Input>

// usage inside react components
const { useField, handleSubmit, reset, formProps } = useForm(Z_Input)
```

#### formatErrorMessage

The preferred way to handle custom error messages would be to add them to the schema directly.<br />
In some cases e.g. when receiving the schema from an API or when having to localise the error, we can leverage this helper.

```ts
import { ZodIssue } from 'zod'

// Note: the type is ZodIssue and not ZodError since we always only show the first error
function formatErrorMessage(error: ZodIssue, name: string) {
  switch (error.code) {
    case 'too_small':
      return `This field requires at least ${error.minimum} characters.`
    default:
      return error.message
  }
}
```

### useField

A hook that manages the field state and returns the relevant HTML attributes to render our inputs.<br />
Also returns a set of helpers to manually update and reset the field.

| Parameter |  Type                          | Default               |  Description                                                |
| --------- | ------------------------------ | --------------------- | ----------------------------------------------------------- |
| name      | `keyof z.infer<typeof schema>` |                       | The name of the schema property that this field connects to |
| config    | [Config](#config)              | See [Config](#config) | Initial field data and additional config options            |

#### Config

| Property         | Type                                 | Default                 |  Description |
| ---------------- | ------------------------------------ | ----------------------- | ------------ |
| value            | `any`                                | `''`                    |              |
| disabled         | `boolean`                            | `false`                 |              |
| touched          | `boolean`                            | `false`                 |              |
| showValidationOn | `"change"` \| `"blur"` \| `"submit"` | `"submit"`              |              |
| parseValue       | `(Event) => any`                     | `(e) => e.target.value` |              |

```ts
const { inputProps, props, errorMessage, update, reset } = useField('email')
```

#### inputProps

Pass these to native HTML `input`, `select` and `textarea` elements.<br />
Use `data-valid` to style the element based on the validation state.

```ts
type InputProps = {
  name: string
  value: any
  disabled: boolean
  'data-valid': boolean
  onChange: React.ChangeEventHandler<HTMLElement>
  onBlur?: React.KeyboardEventHandler<HTMLElement>
}
```

#### props

Pass these to custom components that render label and input elements.<br />
Also includes information such as `errorMessage` or `valid` that's non standard HTML attributes and thus can't be passed to native HTML `input` elements directly.

```ts
type Props = {
  value: any
  name: string
  valid: boolean
  required: boolean
  disabled: boolean
  errorMessage?: string
  onChange: React.ChangeEventHandler<HTMLElement>
  onBlur?: React.KeyboardEventHandler<HTMLElement>
}
```

#### errorMessage

> **Note**: If you're using [`props`](#props), you already get the errorMessage!

A string containing the validation message. Only returned if the field is invalid **and** touched.

#### update

Programmatically change the data of a field. Useful e.g. when receiving data from an API.<br />
If value is changed, it will automatically trigger re-validation.

> **Note**: If you know the initial data upfront, prefer to pass it to the `useField` hook directly though.

```ts
update({
  value: 'Foo',
  touched: true,
})
```

#### reset

Resets the field back to its initial field data.

```ts
reset()
```

### handleSubmit

Helper that wraps the native `onSubmit` event on `<form>` elements.<br />
It prevents default action execution and parses the form data using the zod schema.

| Parameter |  Type                            |  Description                                       |
| --------- | -------------------------------- | -------------------------------------------------- |
| onSuccess | `(data: z.infer<typeof schema>)` | Callback on successful safe parse of the form data |
| onFailure | `(error: ZodError)`              | Callback on failed safe parse                      |

```ts
import { ZodError } from 'zod'

function onSuccess(data: T_Input) {
  console.log(data)
}

function onFailure(error: ZodError) {
  console.error(error)
}

// <form> onSubmit handler
const onSubmit = handleSubmit(onSuccess, onFailure)
```

### reset

Resets the form fields back to their initial field data. Helpful when trying to clear a form after a successful submit.

> **Note**: This API is similar to the `reset` helper that the `useField` hook returns. The only difference is that it resets all fields.

```
reset()
```

### formProps

An object that contains props that are passed to the native `<form>` element.
Currently only consists of a single prop:

```ts
const formProps = {
  noValidate: true,
}
```

## License

react-use-keyframe is licensed under the [MIT License](http://opensource.org/licenses/MIT).<br>
Documentation is licensed under [Creative Common License](http://creativecommons.org/licenses/by/4.0/).<br>
Created with ♥ by [@robinweser](http://weser.io) and all the great contributors.
