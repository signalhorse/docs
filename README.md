# Signal Horse Docs

This repository contains the standalone Signal Horse user manual built with MkDocs and Material for MkDocs.

## Local preview

```powershell
pip install -r requirements.txt
mkdocs serve
```

Open the local preview URL printed by MkDocs, usually `http://127.0.0.1:8000`.

## Strict build check

```powershell
mkdocs build --strict
```

## GitHub Pages

The workflow at `.github/workflows/deploy-pages.yml` builds this repository and deploys it to GitHub Pages.

GitHub Pages only hosts the static manual. The Signal Horse backend, installers, and release artifacts still need to be hosted elsewhere, such as `signal.horse` or GitHub Releases.