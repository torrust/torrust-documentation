# Getting started
The easiest way is to get built binaries from [Releases](https://github.com/torrust/torrust/releases),
but building from sources is also possible:

```bash
git clone https://github.com/torrust/torrust-web-backend.git
cd torrust-web-backend
cargo build --release
```

__Notice:__ Use 1. (Binaries) if you've downloaded the binaries directly. 

1. (Source) After building __Torrust__, navigate to the folder.
```bash
cd torrust-web-backend/target
```

1. (Binaries)
```bash
cd torrust-web-backend
```

2. Create a file called `config.toml` with the following contents and change the [configuration](https://torrust.github.io/torrust-tracker/CONFIG.html) according to your liking.


3. And run __Torrust__:
```bash
./torrust
```
