# Bypass Blocks

### Skip Link to Main Content

```html
<head>
  <style>
    .skip a {
      position: absolute;
      left: 10000px;
      top: auto;
      width: 1px;
      height: 1px;
      overflow: hidden;
      font-size: 2em;
      background: white;
      font-weight: bold;
      display: block;
      padding: 10px;
    }

  .skip a:focus {
    position: static;
    width: auto;
    height: auto;
  }
</style>
</head>

<body>
  <header class="main">
    <div class="skip">
      <a href="#content">Skip to main content</a>
    </div>
    <div class="container">
      <a href="/" class="logo-header">
	      <img class="logo" alt="Homepage" src="/assets/template/images/logo/logo.svg"/>
      </a>
    </div>
  </header>
  <main id="content"></main>
</body>

```

