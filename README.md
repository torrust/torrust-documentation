# Torrust Documentation

This is the documentation site for the Torrust [Tracker](https://github.com/torrust/torrust-tracker) and [Index](https://github.com/torrust/torrust-index).

Live: <https://torrust.com/>

## Installation

__Prerequisites:__

- [Python](https://www.python.org/)
- [pip](https://pip.pypa.io/en/stable/installing/)
- [MkDocs](https://www.mkdocs.org/#installation)
- [MkDocs Material theme](https://squidfunk.github.io/mkdocs-material/getting-started/)

You can install `mkdocs` and `mkdocs-material` with:

```s
pip3 install mkdocs
pip3 install mkdocs-material
```

__Install:__

```bash
git clone https://github.com/torrust/torrust-documentation.git
```

## Usage

__Run for development:__

Navigate to the project folder (docs) and run the following command:

```bash
mkdocs serve
```

Visit `http://localhost:8000` to see live changes.

__Run for production:__

Navigate to the project folder (docs) and build the static site:

```bash
mkdocs build
```

Publish the newly built docs/site folder using NGINX or Apache.

## Contributing

Please report any bugs you find to our issue tracker. Ideas and feature requests are welcome as well! Any pull request targeting existing issues would be very much appreciated.
