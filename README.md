# tobischmitt.net

Personal website, built with [Hugo](https://gohugo.io) and the [Compost](https://github.com/canstand/compost) theme (as a Hugo module, styled with Tailwind CSS). Deployed to GitHub Pages via `.github/workflows/pages.yml`.

Requirements: Hugo (>= 0.166), Go and Node.js.

```
npm install
hugo server   # local preview
hugo build    # build into public/
```

Update the theme with `hugo mod get -u && hugo mod npm pack && npm install`.
