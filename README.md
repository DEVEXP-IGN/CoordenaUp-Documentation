# CoordenaUp-Documentation

Repository containing the documentation for the CoordenaUp project.

## Local development

Run the site locally with:

```bash
bundle install
bundle exec jekyll serve
```

## GitHub Pages

This repository is configured to deploy automatically to GitHub Pages through
GitHub Actions on every push to `main`.

Expected site URL:

`https://devexp-ign.github.io/CoordenaUp-Documentation/`

To enable publishing in GitHub:

1. Open the repository settings.
2. Go to `Settings` -> `Pages`.
3. In `Source`, select `GitHub Actions`.
4. Push to `main` or run the `Deploy Jekyll site to Pages` workflow manually.
