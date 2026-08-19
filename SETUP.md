# Vivideo Status Page - Setup Guide

This folder is a ready-to-push [Upptime](https://upptime.js.org) instance that publishes a
free status page at **https://status.vivideo.ai**. It runs entirely on GitHub Actions +
GitHub Pages (no servers, no subscription).

It is a standalone repo and is completely unrelated to `vivideo-backend`, `vivideo-webapp`,
or `vivideo-landing-page`. Pushing it has zero effect on their CI/CD or deployments.

Monitored endpoints (configured in `.upptimerc.yml`):

- Vivideo Landing - https://vivideo.ai
- Vivideo Web App - https://app.vivideo.ai
- Vivideo Developers - https://developers.vivideo.ai
- Vivideo Partners - https://partners.vivideo.ai

---

## Step 1 - Create a public GitHub repo

1. Go to https://github.com/new
2. Owner: `hancerkiranmevlut`
3. Repository name: `vivideo-status`
4. Visibility: **Public** (required so GitHub Actions minutes and GitHub Pages are free)
5. Do NOT add a README, .gitignore, or license (this folder already has them)
6. Click **Create repository**

## Step 2 - Create a Personal Access Token (GH_PAT)

Upptime commits its results back to the repo, so it needs a token.

1. Go to https://github.com/settings/tokens/new (classic token)
2. Note: `vivideo-status upptime`
3. Expiration: `No expiration` (or set a reminder to rotate it)
4. Scopes: check **`repo`** and **`workflow`**
5. Click **Generate token** and copy it

Then add it as a repo secret:

1. Go to `https://github.com/hancerkiranmevlut/vivideo-status/settings/secrets/actions`
2. Click **New repository secret**
3. Name: `GH_PAT`
4. Value: paste the token
5. Click **Add secret**

## Step 3 - Push this folder

From inside this `vivideo-status/` directory:

```bash
git init
git branch -M master
git add .
git commit -m "Initial Upptime status page for Vivideo"
git remote add origin https://github.com/hancerkiranmevlut/vivideo-status.git
git push -u origin master
```

> Upptime's default branch is `master`. If you prefer `main`, rename it on GitHub afterward
> and update the Pages branch in Step 4.

## Step 4 - Enable GitHub Pages and Issues

1. Issues: `https://github.com/hancerkiranmevlut/vivideo-status/settings` -> Features -> ensure
   **Issues** is checked (used for incident tracking).
2. Pages: `https://github.com/hancerkiranmevlut/vivideo-status/settings/pages`
   - Source: **Deploy from a branch**
   - Branch: **`gh-pages`** / `/ (root)` -> Save
   - The `gh-pages` branch is created automatically the first time the "Static Site CI" or
     "Uptime CI" workflow runs. If you don't see it yet, run the workflow once (Step 5) and
     come back.

## Step 5 - Trigger the first run

1. Go to `https://github.com/hancerkiranmevlut/vivideo-status/actions`
2. If prompted, click **I understand my workflows, go ahead and enable them**
3. Open **Setup CI** -> **Run workflow** (this generates everything and deploys the site).
   You can also run **Uptime CI** manually to get the first data point immediately.

## Step 6 - Point your DNS at GitHub Pages

Add this record in your `vivideo.ai` DNS provider (Cloudflare / Route 53 / etc.):

| Type  | Name     | Value                        |
| ----- | -------- | ---------------------------- |
| CNAME | `status` | `hancerkiranmevlut.github.io` |

- If using Cloudflare, set the record to **DNS only** (grey cloud) at first so GitHub can
  issue the TLS certificate; you can enable the proxy later.
- The `CNAME` file in this repo already tells GitHub Pages to serve `status.vivideo.ai`.
- Back in **Settings -> Pages**, confirm the custom domain shows `status.vivideo.ai` and wait
  for "Enforce HTTPS" to become available (can take a few minutes to an hour).

## Verification

- Actions tab: **Uptime CI** run is green and `history/*.yml` files start appearing.
- https://status.vivideo.ai loads with 4 components and rolling uptime percentages.
- To test incident automation, temporarily change a URL in `.upptimerc.yml` to a broken one,
  push, run **Uptime CI**, and confirm an issue is auto-opened. Then revert.

## Changing what's monitored

Edit `.upptimerc.yml` (the `sites:` list), commit, and push. The **Setup CI** workflow
regenerates the workflows and status page automatically.
