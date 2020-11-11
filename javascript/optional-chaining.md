# Optional Chaining

```javascript
// what we did before optional chaining:
const streetName = user && user.address && user.address.street.name

// what we can do now:
const streetName = user?.address?.street?.name

// this will run even if options is undefined (in which case, onSuccess would be undefined as well)
// however, it will still fail if options was never declared, 
// since optional chaining cannot be used on a non-existent root object.
// optional chaining does not replace checks like if (typeof options == "undefined")
const onSuccess = options?.onSuccess
```

