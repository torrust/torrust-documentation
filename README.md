# Torrust Documentation

## Introduction
Torrust is an open source project that brings you all the tools you need to host your own (private) BitTorrent tracker and online torrent index.

### Project structure
Torrust is split up into two separate applications.

- [__Torrust Tracker__](https://github.com/torrust/torrust-tracker): A lightweight but incredibly powerful and feature-rich (private) BitTorrent Tracker.
- [__Torrust Index__](https://github.com/torrust/torrust): A torrent indexing website that depends on the Torrust tracker.

# Installation

## Torrust index + tracker
Please follow our [guide](https://torrust.github.io/torrust-documentation/installation/).

## Torrust Documentation Site
### Prerequisites (installed)

- [Python](https://www.python.org/)
- [pip](https://pip.pypa.io/en/stable/installing/)
- [MkDocs](https://www.mkdocs.org/#installation)
- [MkDocs Material theme](https://squidfunk.github.io/mkdocs-material/getting-started/)

### Project Files

Clone repo:

```bash
git clone https://github.com/torrust-documentation.git
```

# Usage

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

## Contributing
Please report any bugs you find to our issue tracker. Ideas and feature requests are welcome as well!
Any pull request targeting existing issues would be very much appreciated.
