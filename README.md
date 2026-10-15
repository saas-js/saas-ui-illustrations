<p align="center" width="100%">

<p align="center" width="100%">
<a href="https://illustrations.saas-ui.dev"><img src="https://github.com/user-attachments/assets/0332a537-d883-4e7d-8726-851db7246d04" /></a>
</p>

<h4 align="center">
<a href="http://illustrations.saas-ui.dev">Website</a> &bull;
  <a href="https://www.figma.com/community/file/1369627302822996190/framework-illustrations">Figma</a> &bull;
  <a href="https://saas-ui.dev">Saas UI</a> &bull;
<a href="mailto:tomasz.owczarczyk@gmail.com">Contact</a>
</h4>


# What is this?
Discover a vast collection of free, high-quality illustrations designed specifically for documentation, frameworks, and design systems. Enhance your projects with our easily accessible and professionally crafted illustrations.

# Author
tomasz.owczarczyk@gmail.com

# Licence

[CC BY-SA 4.0](LICENSE.md)

# Deploying to Cloudflare

This is a Jekyll static site. The production host is moving from Vercel to a **Cloudflare Worker with static assets** (not legacy Pages). Vercel config is kept until cutover.

## Local preview

```sh
bundle install
npm install
npm run dev
```

`npm run dev` builds Jekyll into `_site/` and serves it with `wrangler dev` (default `http://localhost:8787`).

## Workers Builds (recommended)

In the Cloudflare dashboard, create a Worker named `saas-ui-illustrations` and connect this GitHub repo.

| Setting | Value |
| --- | --- |
| Worker name | `saas-ui-illustrations` |
| Production branch | `main` |
| Build command | `npm run build` |
| Deploy command | `npx wrangler deploy` |
| Root directory | `/` (repo root) |
| Account ID | leave unset in the repo; pick the account in the dashboard |

Workers Builds already has Ruby, Bundler, and Node. It will `bundle install` from the Gemfile and `npm ci` from `package.json` before the build command.

Do **not** add a custom domain yet. DNS for `saas-ui.dev` is not on Cloudflare. After it is, attach `illustrations.saas-ui.dev` in **Worker → Settings → Domains & Routes** (or uncomment `routes` in `wrangler.jsonc`).

## GitHub Actions (optional)

[`.github/workflows/deploy-cloudflare.yml`](.github/workflows/deploy-cloudflare.yml) builds on every PR and can deploy on push to `main`. Add repository secrets `CLOUDFLARE_API_TOKEN` (Edit Cloudflare Workers) and `CLOUDFLARE_ACCOUNT_ID`. Until those exist, the deploy job skips instead of failing.

## Serving behaviour

There is no `vercel.json`. Vercel was serving Jekyll `_site` with default headers. Wrangler matches that:

- Assets directory: `_site`
- Unknown paths: nearest `404.html` with HTTP 404
- HTML paths: `html_handling: auto-trailing-slash` so `/` serves `index.html` (required; `none` 404s the homepage)
- CORS `Access-Control-Allow-Origin: *` and HSTS via `_headers` (copied into `_site` at build time)