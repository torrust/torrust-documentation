# Torrust Documentation

## Introduction

Torrust is an open source BitTorrent tracker developed using the Rust language. With Torrust, you can upload torrents and supply additional information (such as a description and images) for other users to view and download.

### Repositories

* <a href="https://github.com/torrust/docs">https://github.com/torrust/docs</a> - Torrust Documentation.
* <a href="https://github.com/torrust/torrust">https://github.com/torrust/torrust</a> - Torrust RESTful API.
* <a href="https://github.com/torrust/torrust-web">https://github.com/torrust/torrust-web</a> - Torrust Web.

## Installation

### Prerequisites (installed)

- [Python](https://www.python.org/)
- [pip](https://pip.pypa.io/en/stable/installing/)
- [MkDocs](https://www.mkdocs.org/#installation)
- [MkDocs Material theme](https://squidfunk.github.io/mkdocs-material/getting-started/)

### Project Files

Clone repo:

```bash
git clone https://github.com/torrust/docs.git
```

## Usage

### Run Development

Navigate to the project folder (docs) and run the following command:

```bash
mkdocs serve
```

### Test Development

Visit `http://localhost:8000` to see live changes.

### Run Production

Navigate to the project folder (docs) and build the static site:

```bash
mkdocs build
```

Publish the newly built docs/site folder using NGINX or Apache.
