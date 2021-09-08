# Building Torrust Tracker

## Required tools
- [Git](https://git-scm.com) - Version Control
- [Rust](https://www.rust-lang.org/) - Compiler toolchain & Package Manager (cargo)

### Getting the sources
If you prefer to just download the compiled binaries, you can get the [latest release here](https://github.com/torrust/torrust-tracker/releases). Else:

```bash
git clone https://github.com/torrust/torrust-tracker.git
```

### Building (Skip if you downloaded the binaries)
Build the application by running the following. The binary can be found in `target/release/` after completion.
```bash
cd /opt/torrust/torrust-tracker
cargo build --release
```

Once cargo is done building, `torrust-tracker` will be built at `target/release/torrust-tracker`.
