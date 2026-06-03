# Publishing a New Blog Post

This repository is a Hexo blog site using Hexo 3.8.0 and deploys via `hexo-deployer-git`.

## 1. Install dependencies (one time)

From the repo root:

```bash
npm install
```

This installs Hexo and the plugins listed in `package.json`.

ENSURE YOU ARE WORKING IN THE master BRANCH

## 2. Create a new post

You can create a new post using Hexo or by adding a Markdown file manually.

### Option A: Use Hexo to create a new post

```bash
npx hexo new post "Your Post Title"
```

This creates a new Markdown file under `source/_posts/`.

### Option B: Create the post file manually

Create a file in `source/_posts/` with a name like:

```bash
source/_posts/2026-06-01-your-post-title.md
```

Include front matter at the top, for example:

```yaml
---
title: "Your Post Title"
date: 2026-06-01 10:00:00
categories: Uncategorized
layout: post
---
```

Then add your Markdown content below that block.

## 3. Preview locally

Run the Hexo server to preview your post in a browser:

```bash
npx hexo server
```

Open `http://localhost:4000` and verify the new content.


## 4. Commit the source changes

After adding the new post and verifying everything, commit your source changes.

```bash
git add . ; git commit -m "Working on new blog post" ; git push
```

If you are on the `gh-pages` branch, DONT BE. Editing the gh-pages branch directly is a bad idea.


## 5. Generate the static site and Deploy to GitHub Pages

Once the post looks good, generate the site output:

```bash
npx hexo clean ; npx hexo generate ; npx hexo deploy ; git pull ; git stash
```

The generated site files will be placed in the `public/` directory.

This project is configured in `_config.yml` to deploy using GitHub Pages with `gh-pages`.

That command pushes the generated site to the remote repository defined in `_config.yml`.

## Notes

- `npm install` only needs to be run when dependencies change or on a fresh checkout.
- `npx hexo` uses the local Hexo dependency from this repo.
- The deploy config in `_config.yml` currently points at `https://github.com/dan24678/dan24678.git` and deploys to `gh-pages`. Verify that this is the correct GitHub repository for your live site before running `npx hexo deploy`.
- If you want to preview drafts, use `npx hexo server --drafts`.
