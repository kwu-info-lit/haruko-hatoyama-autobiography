# Haruko Hatoyama's Autobiography - E-Book Project

This repository contains the source files and content for generating the EPUB version of the **Autobiography of Haruko Hatoyama** (1929).

The project is built using the [Re:VIEW](https://github.com/kmuto/review) digital publishing framework (version 5.0).

---

## About the Book

**Haruko Hatoyama** (1861–1938) was a prominent Japanese educator, co-founder of Kyoritsu Women's University, and the matriarch of the influential Hatoyama political family.

This autobiography was originally serialized as a series of articles in the magazine *Shin-Katei* between 1916 and 1918. In 1929, it was compiled and published as an appendix to *The Life of Hatoyama*. Rather than focusing on her individual achievements, the narrative centers heavily on her family life, her devotion to her husband Kazuo Hatoyama, and the education of her children. As she writes in the preface, her life was entirely dedicated to her husband and children, making their stories inseparable from her own autobiography.

---

## Prerequisites

Before building the book, make sure you have the following installed:

- **Ruby** (version 2.7 or higher recommended)
- **Bundler** (for managing Ruby dependencies)

---

## Installation

1. Clone this repository to your local machine.
2. Install the required Ruby gems:
   ```sh
   bundle install
   ```

---

## Generating the EPUB

You can generate the EPUB file using either `rake` or the Re:VIEW CLI command directly.

### Using Rake (Recommended)

A `Rakefile` is provided with pre-configured tasks for compiling the book:

* **Generate EPUB:**
  ```sh
  bundle exec rake epub
  ```
  This creates `book.epub` in the root directory.

* **Clean Build Artifacts:**
  ```sh
  bundle exec rake clean
  ```
  This removes all temporary directories and compiled EPUB files.

### Using Re:VIEW Command Directly

Alternatively, you can run the Re:VIEW compiler directly:

```sh
bundle exec review-epubmaker config.yml
```

---

## Publishing (GitHub Pages)

The EPUB and the web reader in `public/` are published by GitHub Actions (`.github/workflows/pages.yml`):

* **Push to `main`**: builds `book.epub` with Re:VIEW and deploys it together with `public/` to GitHub Pages.
* **Pull requests**: build only; the generated EPUB is attached to the workflow run as the `book-epub` artifact.
* **Manual run**: available from the Actions tab (`workflow_dispatch`).

`book.epub` is a build output and is not committed. Repository **Settings → Pages → Source** must be set to **GitHub Actions**.

---

## Running a Coding Agent in Docker Sandboxes

The repository ships a [Docker Sandboxes](https://docs.docker.com/ai/sandboxes/) environment (`sbxenv.yaml`) that runs a coding agent in an isolated sandbox with Ruby and Re:VIEW preinstalled, so the agent can build the EPUB itself. Claude Code, OpenAI Codex and OpenCode (for Grok) are supported; every agent follows the rules in `AGENTS.md`.

Requires the `sbx` CLI (`sbx login` first). From the repository root:

```sh
sbx env plan   # preview what will be created
sbx env run    # create the sandbox (first time) and attach to Claude Code
sbx env rm     # remove the sandbox
```

### Choosing the agent

Claude Code is the default. Select another agent with `--env-arg agent=...`, and pass the same value to `plan`, `run` and `rm`. Each agent gets its own sandbox named `<agent>-haruko-hatoyama`.

| Agent | Command | API key (store once on the host) |
|---|---|---|
| Claude Code | `sbx env run` | `sbx secret set anthropic` |
| OpenAI Codex | `sbx env run --env-arg agent=codex` | `sbx secret set openai` (or `sbx secret set openai --oauth` for a ChatGPT account) |
| Grok (via OpenCode) | `sbx env run --env-arg agent=opencode` | `sbx secret set xai` |

Grok has no built-in sbx agent, so it runs through [OpenCode](https://opencode.ai/) using the xAI provider. After attaching, pick a Grok model with `/models` in OpenCode.

The secrets are held by the sandbox proxy and never enter the sandbox itself.

### Building inside the sandbox

Inside the sandbox, `bundle exec rake epub` works as usual. The Re:VIEW toolchain is defined as a mixin kit in `sbx/review/spec.yaml`; when `Gemfile.lock` changes, recreate the sandbox (`sbx env rm` then `sbx env run`, with the same `--env-arg`) so the gems are reinstalled.

---

## Project Structure

* **`contents/`**: Contains the book's text source files.
  * **`contents/predef/`**: Prefaces, introductions, and front matter.
  * **`contents/chaps/`**: The main chapters of the autobiography (written in Re:VIEW markup format `.re`).
* **`public/`**: Browser-based EPUB reader published on GitHub Pages (see `public/README.md`).
* **`images/`**: Image assets used in the book (covers, illustrations).
* **`sty/`**: LaTeX stylesheets and macro files (used for PDF generation).
* **`lib/tasks/`**: Custom Rake tasks (`review.rake`).
* **`catalog.yml`**: Defines the book's table of contents and chapter ordering.
* **`config.yml`**: Main configuration file for book compilation (metadata, styles, layouts).
* **`style.css`**: CSS stylesheet for EPUB formatting.
