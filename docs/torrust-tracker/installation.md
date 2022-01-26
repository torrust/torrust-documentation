## Setting up the Torrust Tracker
### Getting the sources
If you prefer to just download the compiled binaries, you can get the [latest release here](https://github.com/torrust/torrust-tracker/releases). Else:

While in `/opt/torrust`:
```bash
git clone https://github.com/torrust/torrust-tracker.git
```

### Building (Skip if you downloaded the binaries)
Build the application by running the following. The binary can be found in `target/release/` after completion.
```bash
cd /opt/torrust/torrust-tracker
cargo build --release
```

### Configuration
Edit the `configuration.toml` and change at least the following keys.

- `external_ip`: Set this to the external IP of the server.
- `[http.access_tokens]`: Add an entry for the `torrust_backend` with a long randomly generated key.
```toml
[http.access_tokens]
torrust_api = "<your randomly generated string>"
```

### Running the Tracker
After building and configuring the Tracker it's ready to be run.
It's recommended to either run it in a screen / tmux session or to create a systemd service for it.

```bash
tmux # open a new tmux session
./target/release/torrust-tracker -c configuration.toml
```
> Press `CTRL+B D` to exit the tmux session without killing it.
