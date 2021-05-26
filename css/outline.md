# Outline

Outline, unline border, doesn't affect layout. It's more like a box-shadow.

It can be used as a "second border"



**Properties**

- `outline-width`
- `outline-color`
- `outline-style`
- `outline-offset`: adds a gap between the element and its outline



**Notes**

- There is no `outline-radius`

- **Never** set `outline` to `none`. The outline helps people navigate with the keyboard. Only set it to none if you provide a suitable alternative:

  ```css
  button {
    outline: none;
  }
  button:focus {
    background: navy;
    color: white;
  }
  ```

  Note that it needs to be high-contract/noticeable

  



