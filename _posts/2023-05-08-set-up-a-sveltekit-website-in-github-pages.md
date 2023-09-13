---
layout: post
title: Set up a SvelteKit website in GitHub Pages
date: 2023-05-08 15:13 +0000
image:
    path: "/assets/img/sk-gh-pages/sk-gh-pagesBanner.png"
categories: [Guide, Svelte]
tags: [sveltekit, github pages, github actions]
author: carlosgdcj
---

In this article, I'll show you how to use GitHub Pages to host your SvelteKit website. 

First of all, there are some things to consider when using GitHub Pages:

* It can only host static websites
* It's only available for public repositories

If none of these are a deal breaker, you're good to go.
There are some other limitations but those are mostly related to the website's content and usage, and in this guide, we'll just create a blank page.

## 1. Create a public GitHub repository
In your GitHub account, click on the + sign at the top right corner, and select **New repository**.
In the creation screen, be sure to make the repository **public**.
Name it whatever you like.
In the following examples, the repository will be called `sk-gh-pages`.

![Creating a repository](/assets/img/sk-gh-pages/repository_create_light.png){: .light}
![Creating a repository](/assets/img/sk-gh-pages/repository_create_dark.png){: .dark}

I added a README just so that the repository isn't empty.
You don't need to do that.
The important thing is that the repository is public.

## 2. Creating a SvelteKit project
Copy the link to clone the repository from GitHub by clicking on the **green button** (<> Code).

![Cloning the repository](/assets/img/sk-gh-pages/repository_clone_light.png){: .light}
![Cloning the repository](/assets/img/sk-gh-pages/repository_clone_dark.png){: .dark}

Clone the repository.

```shell
git clone <link you copied>
```
Inside the repository folder, create a SvelteKit project.
I'll use `pnpm`, but feel free to use `npm` instead.

```shell
cd sk-gh-pages
pnpm create svelte
```

Specify the project to be created on the current directory, even if it isn't empty, and choose the Skeleton project as the template.
The other options, like TypeScript support, linting, formatting, and testing are up to your preference.

To check if everything is working, run:

```shell
pnpm i
pnpm run dev --open
```

A browser window should pop up with the "Welcome to SvelteKit" screen.

## 3. Making our SvelteKit website static
Inside the `routes` folder, create a `+layout.js` file with the following content:

```javascript
export const prerender = true;
```
{: file="routes/+layout.js"}

Next, install the static adapter.
This will allow SvelteKit to generate static websites:

```shell
pnpm i -D @sveltejs/adapter-static
```

In order for it to work, change the first line of the `svelte.config.js` file to use the newly installed static adapter:

```javascript
import adapter from '@sveltejs/adapter-static';
```
{: file="svelte.config.js"}

Now commit all your changes and push them to the GitHub repository.

```shell
git add -A
git commit -m "Static website config"
git push
```

## 4. Setting up GitHub Pages
In the **Settings** tab of your repository, click on the **Pages** option on the left side.

![Settings tab](/assets/img/sk-gh-pages/repository_settings_light.png){: .light}
![Pages option](/assets/img/sk-gh-pages/repository_pages_light.png){: .light .left}
![Transparent placeholder](/assets/img/sk-gh-pages/transparent_square.png){: .light .normal}

![Settings tab](/assets/img/sk-gh-pages/repository_settings_dark.png){: .dark}
![Pages option](/assets/img/sk-gh-pages/repository_pages_dark.png){: .dark .left}
![Transparent placeholder](/assets/img/sk-gh-pages/transparent_square.png){: .dark .normal}

Find the **Source** drop-down menu, and select **GitHub Actions**.
We'll create a workflow that will build and deploy the website to GitHub Pages. 

![Repository source](/assets/img/sk-gh-pages/repository_source_light.png){: .light}
![Repository source](/assets/img/sk-gh-pages/repository_source_dark.png){: .dark}

Now go to the **Actions** tab, and click on **set up a workflow yourself**

![Actions tab](/assets/img/sk-gh-pages/repository_actions_light.png){: .light}
![Set up a workflow](/assets/img/sk-gh-pages/repository_setup_workflow_light.png){: .light}

![Actions tab](/assets/img/sk-gh-pages/repository_actions_dark.png){: .dark}
![Set up a workflow](/assets/img/sk-gh-pages/repository_setup_workflow_dark.png){: .dark}

An editor will open.
Paste the following configuration:

```yaml
# Workflow to build and deploy a SvelteKit website to GitHub Pages
name: Deploy static SvelteKit content to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["main"]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build-site:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        
      - name: Install pnpm
        uses: pnpm/action-setup@v2
        with:
          version: 8
      
      - name: Install Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18
          cache: pnpm
      
      - name: Install dependencies
        run: pnpm i
        
      - name: Setup Pages
        uses: actions/configure-pages@v3
        with:
          static_site_generator: "sveltekit"
      
      - name: Build site
        run: pnpm run build
          
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
        with:
          path: 'build/'
          
  deploy:
    needs: build-site
    runs-on: ubuntu-latest
    
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```
{: file=".github/workflows/deploy_pages.yml"}

This configuration file sets up two jobs: `build-site` and `deploy` and schedule them to run every time a push happens on the `main` branch.
On the build-site job, if you are not using `pnpm`, you can delete the step called "Install pnpm" and then replace all occurrences of `pnpm` with `npm`.

After pasting the configuration, click **Commit changes** in the top right corner.
The name of the file doesn't really matter, but I went with `deploy_pages.yml`.
A new pop-up will appear.
Check the option **Commit directly to the main branch** and again, click on **Commit changes**.

![Commit changes 1](/assets/img/sk-gh-pages/repository_commit_changes_light.png){: .light}
![Commit changes 2](/assets/img/sk-gh-pages/repository_commit_changes2_light.png){: .light}

![Commit changes 1](/assets/img/sk-gh-pages/repository_commit_changes_dark.png){: .dark}
![Commit changes 2](/assets/img/sk-gh-pages/repository_commit_changes2_dark.png){: .dark}

This new commit will trigger the workflow to run.
You can see the progress in the **Actions** tab.
It shouldn't take too long to complete.

![Workflows](/assets/img/sk-gh-pages/repository_workflows_light.png){: .light}
![Workflows](/assets/img/sk-gh-pages/repository_workflows_dark.png){: .dark}

You can click on the workflow to see more details.
If it was successful, the GitHub pages link should appear below the `deploy` job.

![Deploy pages workflow](/assets/img/sk-gh-pages/repository_deploy_light.png){: .light}
![Deploy pages workflow](/assets/img/sk-gh-pages/repository_deploy_dark.png){: .dark}

If the workflow wasn't successful you can click on the jobs to see logs and details on what went wrong.

All that's left is syncing the GitHub repository with our local one.
Run:

```shell
git pull
```

And that's it.
Now every time you make a push to the `main` branch, the workflow will run, building a new version of the website.
If you want to change this behavior, alter the `.yml` file in `.github/workflows` to your liking.
[Here](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions#understanding-the-workflow-file) you can learn more about the syntax and GitHub Actions in general.