# Window Large Lists with react-virtual

Virtualization/windowing can be used to render lots of items on a page. There is a tool called `react-virtual` that can help us with this. You only need to render what is visible to the user on the screen at that time.

**Example:**

```jsx
// before
function MyListOfData({items}) {
  return (
    <ul style={{height: 300}}>
      {items.map(item => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  )
}

// after
function MyListOfData({items}) {
  const listRef = React.useRef()
  const rowVirtualizer = useVirtual({
    size: items.length,
    parentRef: listRef,
    estimateSize: React.useCallback(() => 20, []),
    overscan: 10,
  })

  return (
    <ul ref={listRef} style={{position: 'relative', height: 300}}>
      <li style={{height: rowVirtualizer.totalSize}} />
      {rowVirtualizer.virtualItems.map(({index, size, start}) => {
        const item = items[index]
        return (
          <li
            key={item.id}
            style={{
              position: 'absolute',
              top: 0,
              left: 0,
              width: '100%',
              height: size,
              transform: `translateY(${start}px)`,
            }}
          >
            {item.name}
          </li>
        )
      })}
    </ul>
  )
}
```

In summary, rather than iterating over all the items in your list, you simply tell `useVirtual` how many rows are in your list, give it a callback that it can use to determine what size they each should be, and then it will give you back `virtualItems` and a `totalSize` which you can then use to only render the items the user should be able to see within the window.



## Exercise

```jsx
// Window large lists with react-virtual
// http://localhost:3000/isolated/exercise/04.js

import * as React from 'react'
import {useVirtual} from 'react-virtual'
import {useCombobox} from '../use-combobox'
import {getItems} from '../workerized-filter-cities'
import {useAsync, useForceRerender} from '../utils'

// 💰 I made this for you, you'll need it later:
const getVirtualRowStyles = ({size, start}) => ({
  position: 'absolute',
  top: 0,
  left: 0,
  width: '100%',
  height: size,
  transform: `translateY(${start}px)`,
})

function Menu({
  items,
  getMenuProps,
  getItemProps,
  highlightedIndex,
  selectedItem,
  // 4. Accept new props
  listRef,
  virtualRows,
  totalHeight,
}) {
  return (
    // 5. Pass ref prop to the prop getter
    <ul {...getMenuProps({ref: listRef})}>
      {/* 8. Create new list item with height */}
      {/* this is to ensure that the scrollable area of the <ul /> is the
        same height it would be if we were actually rendering everything */}
      <li style={{height: totalHeight}} />
      {/* 6. Map over virtualRows instead of items */}
      {virtualRows.map(({index, size, start}) => {
        /* 7. Create item */
        const item = items[index]
        return (
          <ListItem
            key={item.id}
            getItemProps={getItemProps}
            item={item}
            index={index}
            isSelected={selectedItem?.id === item.id}
            isHighlighted={highlightedIndex === index}
            // 9. Add style prop
            style={getVirtualRowStyles({size, start})}
          >
            {item.name}
          </ListItem>
        )
      })}
    </ul>
  )
}

function ListItem({
  getItemProps,
  item,
  index,
  isHighlighted,
  isSelected,
  // 10. Accept style prop
  style,
  ...props
}) {
  return (
    <li
      {...getItemProps({
        index,
        item,
        style: {
          backgroundColor: isHighlighted ? 'lightgray' : 'inherit',
          fontWeight: isSelected ? 'bold' : 'normal',
          // 11. spread the incoming styles onto this inline style object
          ...style,
        },
        ...props,
      })}
    />
  )
}

function App() {
  const forceRerender = useForceRerender()
  const [inputValue, setInputValue] = React.useState('')

  const {data: items, run} = useAsync({data: [], status: 'pending'})
  React.useEffect(() => {
    run(getItems(inputValue))
  }, [inputValue, run])

  // 2. Create ref
  const listRef = React.useRef()

  // 1. Call useVirtual with options
  const rowVirtualizer = useVirtual({
    size: items.length,
    parentRef: listRef,
    estimateSize: React.useCallback(() => 20, []), // needs to be a memoized function - here we just use 20 rows
    overscan: 10,
  })

  // 🐨 create a listRef with React.useRef
  // which will be used for the parentRef option you pass to useVirtual
  // and should be applied to the <ul /> for our menu. This is how react-virtual
  // knows how to scroll our items as the user scrolls.

  // 🐨 call useVirtual with the following configuration options:
  // - size (the number of items)
  // - parentRef (the listRef you created above)
  // - estimateSize (a memoized callback function that returns the size for each item)
  //   💰 in our case, every item has the same size, so this will do: React.useCallback(() => 20, [])
  // - overscan (the number of additional rows to render outside the scrollable view)
  //   💰 You can play around with that number, but you probably don't need more than 10.
  // 🐨 you can set the return value of your useVirtual call to `rowVirtualizer`

  const {
    selectedItem,
    highlightedIndex,
    getComboboxProps,
    getInputProps,
    getItemProps,
    getLabelProps,
    getMenuProps,
    selectItem,
  } = useCombobox({
    items,
    inputValue,
    onInputValueChange: ({inputValue: newValue}) => setInputValue(newValue),
    onSelectedItemChange: ({selectedItem}) =>
      alert(
        selectedItem
          ? `You selected ${selectedItem.name}`
          : 'Selection Cleared',
      ),
    itemToString: item => (item ? item.name : ''),
    // 12. When the highlightedIndex changes, then tell react-virtual to scroll
    // to that index.
    onHighlightedIndexChange: ({highlightedIndex}) =>
      highlightedIndex !== -1 && rowVirtualizer.scrollToIndex(highlightedIndex),
    // 13. Update to be a no op (react-virtual) handles this
    scrollIntoView: () => {},
  })

  return (
    <div className="city-app">
      <button onClick={forceRerender}>force rerender</button>
      <div>
        <label {...getLabelProps()}>Find a city</label>
        <div {...getComboboxProps()}>
          <input {...getInputProps({type: 'text'})} />
          <button onClick={() => selectItem(null)} aria-label="toggle menu">
            &#10005;
          </button>
        </div>
        <Menu
          items={items}
          getMenuProps={getMenuProps}
          getItemProps={getItemProps}
          highlightedIndex={highlightedIndex}
          selectedItem={selectedItem}
          // 3. Pass in props
          listRef={listRef}
          virtualRows={rowVirtualizer.virtualItems}
          totalHeight={rowVirtualizer.totalSize}
        />
      </div>
    </div>
  )
}

export default App

```

