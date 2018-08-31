---
title: Dynamic props
description: Set props as dynamic functions
category: react
next: animating-children
---

# Dynamic pose props

Each pose property can be set as a function that resolves when the pose is entered:

```javascript
const Box = posed.div({
  visible: {
    x: 0,
    y: (props) => 100, // Resolved on `visible` enter
    transition: {
      x: { type: 'tween' },
      y: (props) => ({ type: 'spring' }) // Resolved on `visible` enter
    }
  }
})
```

By using the provided `props` argument, this allows us to create dynamic properties that will react to changes in your app.

<TOC />

## Props

So what are props? They're just component props! Any props you provide to your posed component will be forwarded to these dynamic pose props.

For instance, you could use a component's `i` index prop to write a dynamic `delay` prop:

```javascript
const Item = posed.li({
  visible: {
    opacity: 1,
    transition: ({ i }) => ({ delay: i * 50 })
  },
  props: { i: 0 }
});

export default ({ i, isVisible }) =>
  <Item pose={isVisible ? 'hidden' : 'visible'} i={i} />
```

<CodePen id="jzXzdz" height="400" />

## Transition props

`transition` works a little differently than other pose props.

If set as a function, the function is run **once each for every property being animated**.

That function is provided a few extra props, automatically generated by Pose:

- `from`: The current state of this value
- `to`: The target state defined in the pose
- `velocity`: If a numerical value, the current velocity of the value
- `key`: The name of the current value
- `prevPoseKey`: The name of the pose this value was previously set to

These props can be used to return a different transition definition based on the state of the value:

```javascript
const Sidebar = posed.div({
  open: {
    x: '-100%',
    transition: ({ velocity, to }) => velocity < 0
      ? { to: 0 }
      : { to }
  }
});
```

If `transition` is a named map, **some or all** of these can be defined as functions:

```javascript
const Sidebar = posed.div({
  open: {
    x: 0,
    opacity: 1,
    transition: {
      x: ({ velocity, to }) => velocity < 0 ? { to: -300 } : { to },
      opacity: { type: 'spring' }
    }
  }
});
```