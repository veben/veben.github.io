---
layout: post
title: Dark theme
comments: false
---

<p style="text-align:justify;">
Add dark theme to my webpage.
</p>

The website contains a slider allowing user to switch between **light** ore **dark** theme:

```html
<label class="theme-switch" for="checkbox">
  <input type="checkbox" id="checkbox" />
  <div class="slider round"></div>
</label>
<em>Dark theme</em>
```

The following CSS code permit the style switching:
```css
:root {
    --body-bg-color: #fff;
    --body-font-color: #333;
    --h1-font-color: #333;
    --a-font-color: #4183C4;
    --footer-bg-color: #eee;
}

[theme="dark"] {
    --body-bg-color: #161625;
    --body-font-color: #e1e1ff;
    --h1-font-color: #49fb35;
    --a-font-color: #9A97FA;
    --footer-bg-color: #42408F;
}
```

For more information about the javascript code behind, look a the [code](https://github.com/veben/veben.github.io)