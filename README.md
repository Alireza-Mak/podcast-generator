# 🎙️ podcast-generator

A GitHub Action (Docker) that reads a podcast episode YAML file and publishes a valid Apple Podcasts RSS feed back to your repo — automatically, on every push.

This is the action powering [podcast-test](https://github.com/Alireza-Mak/podcast-test), my learning project for GitHub Actions/CI-CD.

---

## 👋 About This Action

Instead of manually hand-writing podcast RSS XML, this action lets you maintain a simple YAML file describing your podcast and episodes. On push, it runs `feed.py` inside a container to regenerate `podcast.xml`, then commits and pushes the updated feed back to your repository.

**Status:** 🚧 Work in progress — still learning and building.

---

## 🚀 What It Does

1. Builds a Docker container (Ubuntu 22.04 + Python 3.10 + `PyYAML` + `git`).
2. `entrypoint.sh` runs on container start:
   - Configures the git identity from the `name` / `email` inputs.
   - Marks `/github/workspace` as a safe git directory.
   - Runs `feed.py`, which reads your source YAML file and builds an Apple Podcasts–compliant `<rss>`/`<channel>`/`<item>` structure.
   - Commits the regenerated `podcast.xml` and pushes it back to the triggering branch.

---

## 📁 Project Structure

```
podcast-generator/
│
├── action.yaml       # Action metadata & inputs
├── Dockerfile         # Container definition (Ubuntu 22.04, Python 3.10, PyYAML, git)
├── entrypoint.sh       # Configures git, runs feed.py, commits & pushes
├── feed.py           # Reads feed.yaml, generates podcast.xml
├── LICENSE
└── README.md
```

---

## ⚙️ Inputs

| Input   | Description                  | Required | Default                       |
|---------|-------------------------------|----------|--------------------------------|
| `email` | The committer's email address | `true`   | `${{ github.actor }}@localhost` |
| `name`  | The committer's name          | `true`   | `${{ github.actor }}`           |

---

## ▶️ Usage

In the **consuming repo's** workflow file:

```yaml
name: Generate Podcast Feeds
on: [push]

permissions:
  contents: write   # required so the action can push podcast.xml back

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repo
        uses: actions/checkout@v3
      - name: Run Feed Generator
        uses: Alireza-Mak/podcast-generator@main
```

`permissions: contents: write` is required — without it, the default `GITHUB_TOKEN` is read-only and the final `git push` step will fail.

---

## 📝 `feed.yaml`

> ⚠️ Field list inferred from the channel-level fields `feed.py` builds (title, subtitle, description, language, link, category) — confirm against your actual `feed.py` if a field doesn't match.

```yaml
title: <Podcast Title>
subtitle: <Podcast Subtitle>
author: <Author Name>
description: <Podcast Description>
link: <Podcast/GitHub Pages URL>
image: <Artwork URL>
language: <e.g. en-us>
category: <e.g. Technology>
format: <e.g. audio/mpeg>
item:
  - title: <Episode Title>
    description: <Episode Description>
    published: <Date Published>
```

---

## 🛠 Built With

- Docker (Ubuntu 22.04)
- Python 3.10, `PyYAML`
- Bash
- GitHub Actions (Docker container actions)
- RSS / Apple Podcasts feed spec

---

## 👨‍💻 About Me

I'm **Alireza Mak**, a full-stack developer with 5 years of experience, passionate about game development and creative coding. This project is part of my broader push to round out my CI/CD and automation skills.

🌐 Portfolio: [alirezamak.com](https://alirezamak.com)

---

## 🔗 Links

[![Portfolio](https://img.shields.io/badge/My_Portfolio-000?style=for-the-badge&logo=ko-fi&logoColor=white)](https://alirezamak.com/)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/alireza-mak/)
[![Email](https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:info@alirezamak.com)

---

## 📜 License

This project is open-source and available under the [MIT License](./LICENSE).

## Useful Links

- [RSS Feed Sample](https://help.apple.com/itc/podcasts_connect/en.lproj/itcbaf351599.html)
- [GitHub Actions Docs](https://docs.github.com/en/actions)
