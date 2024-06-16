---
title: "Dark mode"
date: 2020-02-03
tags:
  - jekyll
  - css
---

I've just added dark mode support to my blog! Now you can easily switch between light and dark themes for a more comfortable reading experience.

## The HTML code
Here's the HTML code that adds a slider, allowing users to toggle between **light** and **dark** themes:

```html
<label class="theme-switch" for="checkbox">
  <input type="checkbox" id="checkbox" />
  <div class="slider round"></div>
</label>
<em>Dark theme</em>
```

## The CSS code
The following CSS code handles the style changes for both themes:
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

These variables define the colors for the body background, font, headings, links, and footer for both light and dark themes. When the dark theme is activated, the styles are switched accordingly.