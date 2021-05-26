# Color

- **Named Colors**: `red`, `deeppink`, `royalblue`
- **Hex Codes**: `#123ABC`, `#FFF`. Another way to represent RGB, but even less intuitive. Hexadecimal goes from `0` to `f`. Only advantage is that they're quick to type.
- **HSL** (recommended)
  - **Hue**: The pigment. The color. Measured in degrees (0-360 degrees). It's a circular unit (360 takes you back to 0).
  - **Saturation**: The horizontal grid on a color picker. Low saturation color looks washed out. Lower saturation to make colors more subtle.
  - **Lightness**: The vertical grid on a color picker. Low brightness looks black. Lower brightness to make colors darker. High lightness is white. (In HSB, B means "Brightness". The higher the brightness, the more of a completely pure color you have.)
- **RGB**: Not intuitive. 



### Using HSL

```css
.colorful-thing {
  color: hsl(200deg 100% 50%);
  border-bottom: 3px solid hsl(100deg 75% 50%);
}

/* Fourth value is the alpha channel */
.first.box {
	background-color: hsl(340deg 100% 50% / 1);
}
```

**What's the deal with the slash?** A rising trend in the CSS language is to separate groups of values with a forward-slash (`/`). It isn't related to division, it's meant to segment values into logical groups. In this case, the first 3 values are related to the color, and the fourth value is related to the opacity, so they're separated with a slash.