# Yiddishkeit-AI — Content

Content for [yiddishkeit-ai-website](https://github.com/ai-web-projects/yiddishkeit-ai-website).
This repo holds content (Markdown + frontmatter) and, for now, the media
files themselves — no site code.

This repo is wired into the website repo as a git submodule, so pushing here
and rebuilding the site are two separate, independent steps.

## Structure

```
he/
├── articles/            # frontmatter: title, description, date, locale, pdfUrl?
├── audio/                # frontmatter: title, description, date, locale, mediaUrl, duration?, downloadable
│   └── media/             # the actual audio files
├── video/                # frontmatter: title, description, date, locale, mediaUrl, duration?, downloadable
│   └── media/             # the actual video files
├── presentations/        # frontmatter: title, description, date, locale, mediaUrl, downloadable
│   └── media/             # the actual PDF/slide files
└── images/               # frontmatter: title, description, date, locale, imageUrl
    └── media/             # the actual image files
```

Each top-level folder is one language (`he/` for now). Adding a new
language later means adding a matching top-level folder (e.g. `en/`) here,
plus updating the loaders in the website repo's `src/content.config.ts` to
also read from it — see that repo's README.

## Media hosting

`mediaUrl` / `imageUrl` / `pdfUrl` point at files committed into this repo's
`media/` subfolders, referenced via their `raw.githubusercontent.com` URL,
e.g.:

```
https://raw.githubusercontent.com/ai-web-projects/yiddishkeit-ai-content/master/he/audio/media/<file>.mp3
```

This is free and requires no extra infrastructure, but it's not a real CDN —
GitHub caps individual files at 100MB and isn't built for high-traffic media
serving. It's a deliberate placeholder: because these fields are just plain
URLs, moving to Cloudflare R2 (or anything else) later is a matter of
uploading the files there and updating the URLs — no site-code changes.

## Adding content

Add a new Markdown file under the right type folder (and, if it has media,
the actual file under that type's `media/` subfolder), with frontmatter
matching that type's schema (see above), then push to `master`. Pushing
triggers `.github/workflows/trigger-deploy.yml`, which calls the Cloudflare
Pages Deploy Hook to rebuild the live site — no code-repo change needed.

The workflow needs a repo secret **`CF_PAGES_DEPLOY_HOOK_URL`** set to the
Cloudflare Pages project's deploy hook URL (Pages project → Settings →
Builds & deployments → Deploy hooks). Until that secret exists, the
workflow just logs a message and exits without failing.
