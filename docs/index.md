# Introduction
*Torrust* is an open source project that brings you all the tools you need to host your own (private) BitTorrent tracker and online torrent index.

## Project structure
Torrust is split up into two separate applications.

- [__Torrust Tracker__](https://github.com/torrust/torrust-tracker): A high-performance and feature-rich (private) BitTorrent tracker.
- [__Torrust Index__](https://github.com/torrust/torrust): A torrent indexing website that depends on the Torrust tracker.

## Features
- [X] Self-hosted high-performance BitTorrent tracker.
- [X] Support for public, private and whitelisted tracker modes.
- [X] API for whitelisting torrents and issuing tracker keys.
- [X] Email / Password authentication (optional email verification).
- [X] Torrent Uploading / Downloading (including magnet links).
- [X] Torrent moderation.
- [X] Customizable torrent categories.
- [X] No external services needed.
- [X] Completely written in Rust!

## Screenshots
![Web UI Sign Up page](https://raw.githubusercontent.com/torrust/torrust/main/img/signup.png)
![Web UI Popular page](https://raw.githubusercontent.com/torrust/torrust/main/img/torrents.png)
![Web UI Torrent page](https://raw.githubusercontent.com/torrust/torrust/main/img/torrent.png)
![Web UI Upload page](https://raw.githubusercontent.com/torrust/torrust/main/img/upload.png)
![Web UI Settings page](https://raw.githubusercontent.com/torrust/torrust/main/img/settings.png)

## Live Demo 
- Index: [https://torrust.dutchbits.nl](https://torrust.dutchbits.nl)
- Tracker: [http://tracker.dutchbits.nl](http://tracker.dutchbits.nl)

## Install
- Torrust Tracker: [__Install__](https://torrust.com/torrust-tracker/install)
- Torrust Index: [__Install__](https://torrust.com/torrust-index/install) (requires running a Torrust Tracker)
- Docker (Tracker + Index): [https://github.com/torrust/torrust-docker](https://github.com/torrust/torrust-docker)

## Contributing
Please report any bugs you find to our issue tracker. Ideas and feature requests are welcome as well!
Any pull request targeting existing issues would be very much appreciated.
