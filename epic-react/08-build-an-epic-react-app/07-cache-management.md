# Cache Management (Using react-query 2)

One of the hardest parts of development. What can the cache be invalidated? When is it stale? Does it have invalid data?

Many developers store cache and global state all in the same area, which makes things very complex.

It's better to separate them into two buckets:

1. UI state: Modal is open, item is highlighted, etc.

2. Server cache: User data, tweets, contacts, etc.

Frontend data is just a copy of the backend data. One good solution for keeping the frontend up to data is by using `react-query`.

\- `useQuery`: https://react-query.tanstack.com/docs/guides/queries

\- `useMutation`: https://react-query.tanstack.com/docs/guides/mutations

One thing to keep in mind is that if you have two resources that are the same used in two different components, you want to make sure that both the `queryKey` is the same otherwise you'll have two entries for that resource in the cache. Also, make sure their `queryFn` do the same thing or you'll have some pretty odd behavior!



**For changes in Version 3:**

https://react-query.tanstack.com/guides/migrating-to-react-query-3



## Exercises

### Create listItems with React Query useMutation

`react-query` `useMutation` takes a function (your mutation function) and returns a function that can be used to trigger the mutation.

In this case, our mutation function returns a promise that performs the mutation.

```jsx

  const [create] = useMutation(({bookId}) =>
    client('list-items', {data: {bookId}, token: user.token}),
  )

```

```jsx
        <TooltipButton
          label="Add to list"
          highlight={colors.indigo}
          onClick={() => create({bookId: book.id})}
          icon={<FaPlusCircle />}
        />
```

### View listItems with React Query useQuery

```jsx
function StatusButtons({user, book}) {
  // Use useQuery to get books added to list
  const {data: listItems} = useQuery({
    queryKey: 'list-items',
    queryFn: () =>
      client('list-items', {token: user.token}).then(data => data.listItems),
  })

  // Once listItems exists, see if the current book is in the list
  const listItem = listItems?.find(li => li.bookId === book.id) ?? null

  // Add an option with onSettled to invalidate the cache for 'list-items'
  // when we add a new book, to get a refreshed list
  const [create] = useMutation(
    ({bookId}) => client('list-items', {data: {bookId}, token: user.token}),
    {onSettled: () => queryCache.invalidateQueries('list-items')},
  )
```



### Remove listItems with useMutation

Same as the create function, but change the url and method.

```jsx
  const [remove] = useMutation(
    ({id}) => client(`list-items/${id}`, {method: 'DELETE', token: user.token}),
    {onSettled: () => queryCache.invalidateQueries('list-items')},
  )
```

```jsx
        <TooltipButton
          label="Remove from list"
          highlight={colors.danger}
          onClick={() => remove({id: listItem.id})}
          icon={<FaMinusCircle />}
        />
```



### Update listItems with useMutations

```jsx
  const [update] = useMutation(
    updates =>
      client(`list-items/${updates.id}`, {
        method: 'PUT',
        data: updates,
        token: user.token,
      }),
    {onSettled: () => queryCache.invalidateQueries('list-items')},
  )
```



```jsx
{listItem ? (
        Boolean(listItem.finishDate) ? (
          <TooltipButton
            label="Unmark as read"
            highlight={colors.yellow}
            onClick={() => update({id: listItem.id, finishDate: null})}
            icon={<FaBook />}
          />
        ) : (
          <TooltipButton
            label="Mark as read"
            highlight={colors.green}
            onClick={() => update({id: listItem.id, finishDate: Date.now()})}
            icon={<FaCheckCircle />}
          />
        )
      ) : null}
```



### View listItem Data in BookRow with useQuery

Same `useQuery` call as before. Note use of same query key.

```jsx
  const {data: listItems} = useQuery({
    queryKey: 'list-items',
    queryFn: () =>
      client('list-items', {token: user.token}).then(data => data.listItems),
  })

  const listItem = listItems?.find(li => li.bookId === book.id) ?? null
```

```jsx
              {listItem?.finishDate ? (
                <Rating user={user} listItem={listItem} />
              ) : null}
```



### Update a Book Rating with useMutation

Same as update function from before:

```jsx
  const [update] = useMutation(
    updates =>
      client(`list-items/${updates.id}`, {
        method: 'PUT',
        data: updates,
        token: user.token,
      }),
    {onSettled: () => queryCache.invalidateQueries('list-items')},
  )
```



### Refactor useAsync to useQuery

Old code:

```jsx
const {data, error, run, isLoading, isError, isSuccess} = useAsync()

React.useEffect(() => {
  if (!queried) {
    return
  }
  run(
    client(`books?query=${encodeURIComponent(query)}`, {
      token: user.token,
    }).then(data => data.books),
  )
}, [query, queried, run, user.token])
```



Refactored code:

```jsx
const {data, error, isLoading, isError, isSuccess} = useQuery({
    queryKey: ['bookSearch', {query}],
    queryFn: () =>
      client(`books?query=${encodeURIComponent(query)}`, {
        token: user.token,
      }).then(data => data.books),
  })
```



### Load and Persist Book Data with useQuery

Get book:

```jsx
function BookScreen({user}) {
  const {bookId} = useParams()

  const {data: book = loadingBook} = useQuery({
    queryKey: ['book', {bookId}],
    queryFn: () =>
      client(`books/${bookId}`, {token: user.token}).then(data => data.book),
  })

  const {data: listItems} = useQuery({
    queryKey: 'list-items',
    queryFn: () =>
      client('list-items', {token: user.token}).then(data => data.listItems),
  })

  const listItem = listItems?.find(li => li.bookId === book.id) ?? null

  const {title, author, coverImageUrl, publisher, synopsis} = book

  return (
```



Persist notes:

```jsx
function NotesTextarea({listItem, user}) {
  const [mutate] = useMutation(
    updates =>
      client(`list-items/${updates.id}`, {
        method: 'PUT',
        data: updates,
        token: user.token,
      }),
    {onSettled: () => queryCache.invalidateQueries('list-items')},
  )

  const debouncedMutate = React.useMemo(() => debounceFn(mutate, {wait: 300}), [
    mutate,
  ])

  function handleNotesChange(e) {
    debouncedMutate({id: listItem.id, notes: e.target.value})
  }

  return (
```

### Query with useQuery for listItems in ListItemList

Same query as before:

```jsx
/** @jsx jsx */
import {jsx} from '@emotion/core'

import {useQuery} from 'react-query'
import {client} from 'utils/api-client'
import {BookListUL} from './lib'
import {BookRow} from './book-row'

function ListItemList({
  user,
  filterListItems,
  noListItems,
  noFilteredListItems,
}) {
  const {data: listItems} = useQuery({
    queryKey: 'list-items',
    queryFn: () =>
      client('list-items', {token: user.token}).then(data => data.listItems),
  })

  const filteredListItems = listItems?.filter(filterListItems)

  if (!listItems?.length) {
    return <div css={{marginTop: '1em', fontSize: '1.2em'}}>{noListItems}</div>
  }
  if (!filteredListItems.length) {
    return (
      <div css={{marginTop: '1em', fontSize: '1.2em'}}>
        {noFilteredListItems}
      </div>
    )
  }

  return (
    <BookListUL>
      {filteredListItems.map(listItem => (
        <li key={listItem.id}>
          <BookRow user={user} book={listItem.book} />
        </li>
      ))}
    </BookListUL>
  )
}

export {ListItemList}

```



### Clear queryCache on User Logout

User `queryCache.clear()` in any log out functions (when the user is forced out, or if a user logs out by choice)

### Create useBookSearch Custom Hook

Refactor code to new hook:

```jsx
// utils/books.js

import {useQuery} from 'react-query'
import {client} from './api-client'
import bookPlaceholderSvg from 'assets/book-placeholder.svg'

const loadingBook = {
  title: 'Loading...',
  author: 'loading...',
  coverImageUrl: bookPlaceholderSvg,
  publisher: 'Loading Publishing',
  synopsis: 'Loading...',
  loadingBook: true,
}

const loadingBooks = Array.from({length: 10}, (v, index) => ({
  id: `loading-book-${index}`,
  ...loadingBook,
}))

function useBookSearch(query, user) {
  const result = useQuery({
    queryKey: ['bookSearch', {query}],
    queryFn: () =>
      client(`books?query=${encodeURIComponent(query)}`, {
        token: user.token,
      }).then(data => data.books),
  })

  return {...result, books: result.data ?? loadingBooks}
}

export {useBookSearch}

```



```jsx
  const {books, error, isLoading, isError, isSuccess} = useBookSearch(
    query,
    user,
  )
```



### Create a useBook Custom Hook

```jsx
function useBook(bookId, user) {
  const {data} = useQuery({
    queryKey: ['book', {bookId}],
    queryFn: () =>
      client(`books/${bookId}`, {token: user.token}).then(data => data.book),
  })

  return data ?? loadingBook
}

```

```jsx
const book = useBook(bookId, user)
```



### Create useListItem(s) Custom Hook

```jsx
import {useQuery} from 'react-query'
import {client} from './api-client'

function useListItems(user) {
  const {data: listItems} = useQuery({
    queryKey: 'list-items',
    queryFn: () =>
      client('list-items', {token: user.token}).then(data => data.listItems),
  })
  return listItems ?? []
}

function useListItem(user, bookId) {
  const listItems = useListItems(user)
  return listItems.find(li => li.bookId === bookId) ?? null
}

export {useListItem, useListItems}

```



```jsx
function BookRow({user, book}) {
  const {title, author, coverImageUrl} = book

  const listItem = useListItem(user, book.id)

  const id = `book-row-book-${book.id}`

  return (
```



```jsx
function ListItemList({
  user,
  filterListItems,
  noListItems,
  noFilteredListItems,
}) {
  const listItems = useListItems(user)

  const filteredListItems = listItems?.filter(filterListItems)

  if (!listItems?.length) {
    return <div css={{marginTop: '1em', fontSize: '1.2em'}}>{noListItems}</div>
  }
  if (!filteredListItems.length) {
    return (
      <div css={{marginTop: '1em', fontSize: '1.2em'}}>
        {noFilteredListItems}
      </div>
    )
  }

  return (
```



### Reuse Mutation Logic in a Custom Hook

```jsx
function useUpdateListItem(user) {
  return useMutation(
    updates =>
      client(`list-items/${updates.id}`, {
        method: 'PUT',
        data: updates,
        token: user.token,
      }),
    {onSettled: () => queryCache.invalidateQueries('list-items')},
  )
}
```



```jsx
const [mutate] = useUpdateListItem(user)
```



### Reuse Custom Hooks to Reduce Code

Just using our new hooks in other files.

### Create and Remove Custom Hook

```jsx
const defaultMutationOptions = {
  onSettled: () => queryCache.invalidateQueries('list-items'),
}

function useUpdateListItem(user) {
  return useMutation(
    updates =>
      client(`list-items/${updates.id}`, {
        method: 'PUT',
        data: updates,
        token: user.token,
      }),
    defaultMutationOptions,
  )
}

function useCreateListItem(user) {
  return useMutation(
    ({bookId}) => client('list-items', {data: {bookId}, token: user.token}),
    defaultMutationOptions,
  )
}

function useRemoveListItem(user) {
  return useMutation(
    ({id}) => client(`list-items/${id}`, {method: 'DELETE', token: user.token}),
    defaultMutationOptions,
  )
}
```

```jsx
function StatusButtons({user, book}) {
  const listItem = useListItem(user, book.id)

  const [update] = useUpdateListItem(user)

  const [remove] = useRemoveListItem(user)

  const [create] = useCreateListItem(user)

  return (
```



### Wrap the `<App/>` in a `<ReactQueryConfigProvider>`

`react-query` will make regular tries to an API on an interval if it gets a 404. You can let `react-query` know how to handle errors instead. Use a `<ReactQueryConfigProvider` with a config:

```jsx
import {loadDevTools} from './dev-tools/load'
import './bootstrap'
import * as React from 'react'
import ReactDOM from 'react-dom'
import {ReactQueryConfigProvider} from 'react-query'
import {App} from './app'

const queryConfig = {
  queries: {
    useErrorBoundary: true,
    // react-query refetches on window focus by default
    refetchOnWindowFocus: false,
    retry(failureCount, error) {
      if (error.status === 404) {
        return false
      } else if (failureCount < 2) {
        return true
      } else {
        return false
      }
    },
  },
}

loadDevTools(() => {
  ReactDOM.render(
    <ReactQueryConfigProvider config={queryConfig}>
      <App />
    </ReactQueryConfigProvider>,
    document.getElementById('root'),
  )
})

```

### Show Error When Request Fails

```jsx
function NotesTextarea({listItem, user}) {
  // Our hooks provide an error and isError values
  const [mutate, {error, isError}] = useUpdateListItem(user)

  const debouncedMutate = React.useMemo(() => debounceFn(mutate, {wait: 300}), [
    mutate,
  ])

  function handleNotesChange(e) {
    debouncedMutate({id: listItem.id, notes: e.target.value})
  }

  return (
    <React.Fragment>
      <div>
        <label
          htmlFor="notes"
          css={{
            display: 'inline-block',
            marginRight: 10,
            marginTop: '0',
            marginBottom: '0.5rem',
            fontWeight: 'bold',
          }}
        >
          Notes
        </label>
        {/* Use the ErrorMessage component to display your error */}
        {isError ? (
          <ErrorMessage
            error={error}
            variant="inline"
            css={{marginLeft: 6, fontSize: '0.7em'}}
          />
        ) : null}
      </div>
      <Textarea
        id="notes"
        defaultValue={listItem.notes}
        onChange={handleNotesChange}
        css={{width: '100%', minHeight: 300}}
      />
    </React.Fragment>
  )
}

export {BookScreen}
```



### React Query Custom Error Handling

`react-query` swallows all errors, even those from other non-react-query API calls.

Do the following to not swallow those errors and to forward those along.

```jsx
// Accept options
function useUpdateListItem(user, options) {
  return useMutation(
    updates =>
      client(`list-items/${updates.id}`, {
        method: 'PUT',
        data: updates,
        token: user.token,
      }),
    // Use options
    {...defaultMutationOptions, ...options},
  )
}
```

Pass along an option to throw errors:

```jsx
 const [update] = useUpdateListItem(user, {throwOnError: true})
```



### Add a Loading Spinner for Notes

```jsx
  const [mutate, {error, isError, isLoading}] = useUpdateListItem(user)
```



```jsx
{isLoading ? <Spinner /> : null}
```



### Prefetch the Book Search Query

To prevent flashing of all data, prefetch your Discover results as the component unmounts:

```jsx
function DiscoverBooksScreen({user}) {
  const [query, setQuery] = React.useState('')
  const [queried, setQueried] = React.useState(false)

  const {books, error, isLoading, isError, isSuccess} = useBookSearch(
    query,
    user,
  )

  React.useEffect(() => {
    return () => {
      refetchBookSearchQuery(user)
    }
  }, [user])
```

```jsx
const getBookSearchConfig = (query, user) => ({
  queryKey: ['bookSearch', {query}],
  queryFn: () =>
    client(`books?query=${encodeURIComponent(query)}`, {
      token: user.token,
    }).then(data => data.books),
})

function refetchBookSearchQuery(user) {
  // remove all book search queries, regardless of what they are
  queryCache.removeQueries('bookSearch')
  queryCache.prefetchQuery(getBookSearchConfig('', user))
}
```



### Add Books to the Query Cache

Would be nice to prevent double searching the same data that is stored in two different query keys.

Add an `onSuccess` config where you set query data:

```jsx
const getBookSearchConfig = (query, user) => ({
  queryKey: ['bookSearch', {query}],
  queryFn: () =>
    client(`books?query=${encodeURIComponent(query)}`, {
      token: user.token,
    }).then(data => data.books),
  config: {
    onSuccess(books) {
      for (const book of books) {
        setQueryDataForBook(book)
      }
    },
  },
})

function setQueryDataForBook(book) {
  queryCache.setQueryData(['book', {bookId: book.id}], book)
}

```



### Add Optimistic Updates and Recovery

Optimistically updated you UI after marking a book as read. If the request fails, the UI will revert:

```jsx
const defaultMutationOptions = {
  // If there is an error, and the recover param is a function, run it
  // In this case, it sets the cache to the previous items
  onError(err, variables, recover) {
    if (typeof recover === 'function') {
      recover()
    }
  },
  onSettled: () => queryCache.invalidateQueries('list-items'),
}

function useUpdateListItem(user, options) {
  return useMutation(
    updates =>
      client(`list-items/${updates.id}`, {
        method: 'PUT',
        data: updates,
        token: user.token,
      }),
    {
      // The onMutate config gives you access to the new item
      onMutate(newItem) {
        const previousItems = queryCache.getQueryData('list-items')
        
        // Set the query data for 'list-items', the data being
        // what the new values should be.
        // The server is doing this, but we're just beating it to
        // the punch.
        queryCache.setQueryData('list-items', old => {
          return old.map(item => {
            return item.id === newItem.id ? {...item, ...newItem} : item
          })
        })
        // Returns to onError -- a function that resets the UI to what
        // it was before optimistic updates
        return () => queryCache.setQueryData('list-items', previousItems)
      },
      ...defaultMutationOptions,
      ...options,
    },
  )
}
```





