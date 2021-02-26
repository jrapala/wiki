# Add Styles

React elements can be styled with:

- CSS
- Inline styles
- CSS-in-JS (with [emotion 👩‍🎤](https://emotion.sh), for example)



Benefits of CSS-in-JS:

\- [A Unified Styling Language](https://medium.com/seek-blog/a-unified-styling-language-d0c208de2660)

\- [Maintainable CSS in React](https://www.youtube.com/watch?v=3-4KsXPO2Q4&list=PLV5CVI1eNcJgNqzNwcs4UKrlJdhfDjshf)



## How to Use Emotion

There are two ways to use Emotion. Either by creating a styled component, where styles are carried with the component, or by applying styles to a component.



### Styled Components

```react
// styled component
import styled from '@emotion/styled'

const Button = styled.button`
  color: turquoise;
`

// <Button>Hello</Button>
//
//     👇
//
// <button className="css-1ueegjh">Hello</button>
```

```react
// object syntax
const Button = styled.button({
  color: 'turquoise',
})
```

```react
// accepting props

const Box = styled.div(props => {
  return {
    height: props.variant === 'tall' ? 150 : 80,
  }
})

// or with the template literal form:
const Box = styled.div`
  height: ${props => (props.variant === 'tall' ? '150px' : '80px')};
`
```



Object syntax accept any number of arguments, but order matters. It's a nice way of overwriting styles:

```react
const buttonVariants = {
  primary: {
    backgroundColor: '#3f51b5',
    color: 'white',
  },
  secondary: {
    backgroundColor: '#f1f2f7',
    color: '#434449',
  },
}

const Button = styled.button(
  {
    padding: '10px 15px',
    border: '0',
    lineHeight: '1',
    borderRadius: '3px',
  },
  ({variant = 'primary'}) => buttonVariants[variant],
)
```





### CSS Props

Best for one-off styles. No need to create `<Wrapper />` components.

You will need to add the `/** @jsx jsx */` pragma. This forces elements to be compiled via `jsx(div)` instead of `React.createElement(div)`. The `jsx` function moves the element through `@emotion`, where the `css` prop can be processed.

```react
/** @jsx jsx */
import {jsx} from '@emotion/core'
import * as React from 'react'

function SomeComponent() {
  return (
    <div
      css={{
        backgroundColor: 'hotpink',
        '&:hover': {
          color: 'lightgreen',
        },
      }}
    >
      This has a hotpink background.
    </div>
  )
}

// or with string syntax:
function SomeOtherComponent() {
  const color = 'darkgreen'

  return (
    <div
      css={css`
        background-color: hotpink;
        &:hover {
          color: ${color};
        }
      `}
    >
      This has a hotpink background.
    </div>
  )
}
```

**Tip:** You an install a Babel preset (https://emotion.sh/docs/@emotion/babel-preset-css-prop) to stop the need for `/** @jsx jsx */`



### Adding Labels to css Props

Adding a label to the generated CSS classes can help with debugging. To do this, simply switch the import:

`import styled from '@emotion/styled/macro'`

...and it will generate `css-1eo10v0-App` (included component name) instead of  `css-1350wxy`.



A macro is a basically a Babel plugin or a code transform that doesn't need to be configured.



Learn more about macros:

\- https://emotion.sh/docs/babel-macros

\- https://github.com/kentcdodds/babel-plugin-macros



### Colors and Media Queries

Emotion has a great theming API. But there are other ways to keep colors consistent:

```jsx
// styles/colors.js
export const base = 'white'
export const text = '#434449'
export const gray = '#f1f2f7'
export const gray10 = '#f1f1f4'
export const gray20 = '#e4e5e9'
export const gray80 = '#6f7077'
export const indigo = '#3f51b5'
export const indigoDarken10 = '#364495'
export const indigoLighten80 = '#b7c1f8'
export const yellow = '#ffc107'
export const green = '#4caf50'
export const danger = '#ef5350'
export const orange = 'orange'

// styles/media-queries.js
export const large = '@media (min-width: 1200px)'
export const medium = '@media (min-width: 992px) and (max-width: 1199px)'
export const small = '@media (max-width: 991px)'

// lib.exercise.js
import styled from '@emotion/styled/macro'
import {Dialog as ReachDialog} from '@reach/dialog'

import * as colors from 'styles/colors'
import * as mq from 'styles/media-queries'

const buttonVariants = {
  primary: {
    background: colors.indigo,
    color: colors.base,
  },
  secondary: {
    background: colors.gray,
    color: colors.text,
  },
}

// Old Code
{/* 
const Dialog = styled(ReachDialog)({
  maxWidth: '450px',
  borderRadius: '3px',
  paddingBottom: '3.5em',
  boxShadow: '0 10px 30px -5px rgba(0, 0, 0, 0.2)',
  margin: '20vh auto',
  '@media (max-width: 991px)': {
    width: '100%',
    margin: '10vh auto',
  },
})
*/}

// New Code
const Dialog = styled(ReachDialog)({
  maxWidth: '450px',
  borderRadius: '3px',
  paddingBottom: '3.5em',
  boxShadow: '0 10px 30px -5px rgba(0, 0, 0, 0.2)',
  margin: '20vh auto',
  [mq.small]: {
    width: '100%',
    margin: '10vh auto',
  },
})

```



### Add a Spinner

Emotion fully supports animations and keyframes. 



```jsx
import styled from '@emotion/styled/macro'
import {keyframes} from '@emotion/core'
import {FaSpinner} from 'react-icons/fa'

const spin = keyframes({
  '0%': {transform: 'rotate(0deg)'},
  '100%': {transform: 'rotate(360deg)'},
})

const Spinner = styled(FaSpinner)({
  animation: `${spin} 1s linear infinite`,
})

// add attribute
Spinner.defaultProps = {
  'aria-label': 'loading',
}
```











