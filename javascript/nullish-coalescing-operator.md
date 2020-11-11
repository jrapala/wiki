# Nullish Coalescing Operator

```javascript
// here's what we had to do before:
function add(a, b) {
  a = a == null ? 0 : a
  b = b == null ? 0 : b
  return a + b
}

// here's what we can do now
function add(a, b) {
  a = a ?? 0
  b = b ?? 0
  return a + b
}

// in React:
function DisplayContactName({contact}) {
  return <div>{contact.name ?? 'Unknown'}</div>
}
```

