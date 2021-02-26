# React.memo for Reducing Re-Renders

## Lifecycle of a React App

```html
→  render → reconciliation → commit
         ↖                   ↙
              state change
```

- The “render” phase: React calling your function to create React elements `React.createElement`
- The “reconciliation” phase: compare previous elements with the new ones
- The “commit” phase: take the differences and update the DOM (if needed).

You can opt-out of state updates for a part of the React tree by using one of React’s built-in rendering bail-out utilities: `React.PureComponent`, `React.memo`, or `shouldComponentUpdate`.

### What causes rerendering?

Many reasons

	- Props change
	- Internal state changes
	- Consuming context values change
	- Parent re-renders

**Note:** Just because a component re-rendered, doesn't mean that will result in a DOM update. This is called an "unnecessary rerender." Just because a component is rerendered, doesn't mean the DOM will update. This isn't necesssarily a problem. A problem happens **when renders are slow in general.** 

### How to fix slow renders

Using your browser's profiling tools, start profiling your app, do the interaction, then stop profiling it again. 



### React.memo

The function component version of `React.PureComponent`. They make it so your component will not re-render simply because its parent re-rendered which could improve the performance of your app overall.

If your component renders the same result given the same props, you can wrap it in a call to `React.memo` for a performance boost in some cases by memoizing the result. This means that React will skip rendering the component, and reuse the last rendered result.

**Example:**

```jsx
function CountButton({count, onClick}) {
  return <button onClick={onClick}>{count}</button>
}
CountButton = React.memo(CountButton)

function NameInput({name, onNameChange}) {
  return (
    <label>
      Name: <input value={name} onChange={e => onNameChange(e.target.value)} />
    </label>
  )
}
NameInput = React.memo(NameInput)

function Example() {
  const [name, setName] = React.useState('')
  const [count, setCount] = React.useState(0)
  const increment = () => setCount(c => c + 1)
  return (
    <div>
      <div>
        <CountButton count={count} onClick={increment} />
      </div>
      <div>
        <NameInput name={name} onNameChange={setName} />
      </div>
      {name ? <div>{`${name}'s favorite number is ${count}`}</div> : null}
    </div>
  )
}
```



## Exercises

### Using a Custom Comparator Function

Luckily for us, `React.memo` accepts a second argument which is a custom compare function that allows us to compare the props and return `true` if rendering the component again is **unnecessary** and `false` if it is necessary.

```jsx
function ListItem({
  getItemProps,
  item,
  index,
  selectedItem,
  highlightedIndex,
  ...props
}) {
  const isSelected = selectedItem?.id === item.id
  const isHighlighted = highlightedIndex === index
  return (
    <li
      {...getItemProps({
        index,
        item,
        style: {
          fontWeight: isSelected ? 'bold' : 'normal',
          backgroundColor: isHighlighted ? 'lightgray' : 'inherit',
        },
        ...props,
      })}
    />
  )
}
ListItem = React.memo(ListItem, (prevProps, nextProps) => {
  // If any of these things change, trigger a rerender
  if (prevProps.getItemProps !== nextProps.getItemProps) return false
  if (prevProps.item !== nextProps.item) return false
  if (prevProps.index !== nextProps.index) return false
  if (prevProps.selectedItem !== nextProps.selectedItem) return false

  //  We should only re-render if this list item:
  // 1. was highlighted before and now it's not
  // 2. was not highlighted before and now it is
  if (prevProps.highlightedIndex !== nextProps.highlightedIndex) {
    const wasPrevHighlighted = prevProps.highlightedIndex === prevProps.index
    const isNowHighlighted = nextProps.highlightedIndex === nextProps.index
    return wasPrevHighlighted === isNowHighlighted
  }
  // If the above case doesn't exist, then no, we do not have to rerender
  return true
})
```



### Refactor with Primitive Values

An alternative to the custom memoization comparator function is to just pass pre-computed values for `isSelected` and`isHighlighted`.

Just try to make calculations a little higher in the tree.

```jsx
function Menu({
  items,
  getMenuProps,
  getItemProps,
  highlightedIndex,
  selectedItem,
}) {
  return (
    <ul {...getMenuProps()}>
      {items.map((item, index) => (
        <ListItem
          key={item.id}
          getItemProps={getItemProps}
          item={item}
          index={index}
          isSelected={selectedItem?.id === item.id}
          isHighlighted={highlightedIndex === index}
        >
          {item.name}
        </ListItem>
      ))}
    </ul>
  )
}
Menu = React.memo(Menu)

function ListItem({
  getItemProps,
  item,
  index,
  isSelected,
  isHighlighted,
  ...props
}) {
  return (
    <li
      {...getItemProps({
        index,
        item,
        style: {
          fontWeight: isSelected ? 'bold' : 'normal',
          backgroundColor: isHighlighted ? 'lightgray' : 'inherit',
        },
        ...props,
      })}
    />
  )
}
ListItem = React.memo(ListItem)
```

