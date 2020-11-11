# Custom Hooks

[TOC]

## useForm()

```javascript
export default function useForm(defaults) {
  const [values, setValues] = useState(defaults);

  function updateValue(e) {
    let { value } = e.target;

    if (e.target.type === 'number') {
      value = parseInt(e.target.value);
    }

    setValues({
      ...values,
      [e.target.name]: value,
    });
  }

  return { values, updateValue };
}

```

```javascript
  const { values, updateValue } = useForm({
    name: '',
    email: '',
    coupon: '',
  });
```

