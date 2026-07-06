# SETUP — Publishing to GitHub

This repository is staged locally with a clean initial commit and **no git remote**. Publishing is left to you so you control when it goes public.

## One-time: create the GitHub repo

Create a **public** repository named `project-scaffolder` under your account (via the GitHub UI, or the CLI below).

```bash
# Optional: create the repo with the GitHub CLI
gh repo create s-kellywag/project-scaffolder --public --description "An open-source Claude Code skill that scaffolds execution-ready project packages: intake → ingest → synthesize → generate."
```

## Add the remote and push

From inside this repo (`/Users/sally/Projects/project-scaffolder`):

```bash
git remote add origin git@github.com:s-kellywag/project-scaffolder.git
git branch -M main
git push -u origin main
```

If you use HTTPS instead of SSH:

```bash
git remote add origin https://github.com/s-kellywag/project-scaffolder.git
git branch -M main
git push -u origin main
```

## Before you push — public-repo checklist

- [ ] Confirm the repo is intended to be **public**.
- [ ] Re-read `skill/SKILL.md` and `examples/` once more for anything tool- or org-specific you'd rather not share.
- [ ] Optionally link it from your portfolio site once it's live.
