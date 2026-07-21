# Deploy Tree of Life Vue to GitHub Pages

Repository: `https://github.com/binuriii/tree-of-life-vue`

Expected website URL:

`https://binuriii.github.io/tree-of-life-vue/`

## 1. Requirements

- Git
- Node.js 22.12 or newer
- npm

## 2. Test the project locally

```bash
npm ci
npm run dev
```

To test the production build:

```bash
npm run build
npm run preview
```

## 3. GitHub Pages configuration included in this project

The following items have already been added or corrected:

- `vite.config.js` has `base: "/tree-of-life-vue/"`
- `.github/workflows/deploy.yml` builds and deploys the `dist` folder
- `@vitejs/plugin-vue` is compatible with Vite 8
- public image paths use `import.meta.env.BASE_URL`

## 4. Push the project

If your local folder is already connected to the GitHub repository:

```bash
git status
git add .
git commit -m "Configure GitHub Pages deployment"
git push origin main
```

If you need a fresh local copy, clone the repository first, copy the updated files into it, and then push:

```bash
git clone https://github.com/binuriii/tree-of-life-vue.git
cd tree-of-life-vue
npm ci
npm run build
git add .
git commit -m "Configure GitHub Pages deployment"
git push origin main
```

## 5. Enable GitHub Pages

1. Open the GitHub repository.
2. Select **Settings**.
3. Select **Pages** under **Code and automation**.
4. Under **Build and deployment**, set **Source** to **GitHub Actions**.
5. Open the **Actions** tab.
6. Open the workflow named **Deploy Vue app to GitHub Pages**.
7. Confirm that the build and deploy jobs are green.
8. Open `https://binuriii.github.io/tree-of-life-vue/`.

## Future updates

After this setup, every push to the `main` branch automatically rebuilds and republishes the website:

```bash
git add .
git commit -m "Update website"
git push origin main
```

## Important

If the repository is renamed, update the Vite base setting to match the new repository name:

```js
base: "/NEW-REPOSITORY-NAME/",
```
