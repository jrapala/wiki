# The Spread Operator

Can be used to collect function arguments as an array:

```javascript
function directions(...args) {
  let [start, ...remaining] = args;
  let [finish, ...stops] = remaining.reverse();

  console.log(`drive through ${args.length} towns`);
  console.log(`start in ${start}`);
  console.log(`the destination is ${finish}`);
  console.log(`stopping ${stops.length} times in between`);
}

directions("Truckee", "Tahoe City", "Sunnyside", "Homewood", "Tahoma");
```



```javascript
console.log({...obj1, ...obj2})
// is the same as
console.log(Object.assign({}, obj1, obj2))
```

