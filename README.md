# Yiddishkeit-AI — Content

Content for [yiddishkeit-ai-website](../yiddishkeit-ai-website). This repo
holds only content (Markdown + frontmatter) and references to media stored
in Cloudflare R2 — no site code, no large binary files.

This repo is wired into the website repo as a git submodule, so pushing here
and rebuilding the site are two separate, independent steps.

## Structure

```
he/
├── articles/       # frontmatter: title, description, date, locale, pdfUrl?
├── audio/          # frontmatter: title, description, date, locale, mediaUrl, duration?, downloadable
├── video/          # frontmatter: title, description, date, locale, mediaUrl, duration?, downloadable
├── presentations/  # frontmatter: title, description, date, locale, mediaUrl, downloadable
└── images/         # frontmatter: title, description, date, locale, imageUrl
```

Each folder is one language (`he/` for now). Adding a new language later
means adding a matching top-level folder (e.g. `en/`) here, plus updating
the loaders in the website repo's `src/content.config.ts` to also read from
it — see that repo's README.

`mediaUrl` / `imageUrl` / `pdfUrl` should point at a Cloudflare R2 object
(via its public `r2.dev` URL for now), not a file committed to this repo.
Audio/video/presentations/images are binary and belong in object storage,
not git history.

## Adding content

Add a new Markdown file under the right type folder, with frontmatter
matching that type's schema (see above), then push to `main`. Pushing
triggers `.github/workflows/trigger-deploy.yml`, which calls the Cloudflare
Pages Deploy Hook to rebuild the live site — no code-repo change needed.

The workflow needs a repo secret **`CF_PAGES_DEPLOY_HOOK_URL`** set to the
Cloudflare Pages project's deploy hook URL (Pages project → Settings →
Builds & deployments → Deploy hooks). Until that secret exists, the
workflow just logs a message and exits without failing.
