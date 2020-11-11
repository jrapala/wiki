# Useful Tips

## Filter Truthy Values

If an array may contain undefined values, use this to filter only truthy values:

```javascript
const tops = Object.values(toppings).filter(Boolean);
```

