# Using the torrust-tracker
## Usage
1\. While in the `torrust-tracker` folder, you can run the torrust-tracker like this:
```bash
./target/release/torrust-tracker
```

## Usage with Tmux
1\. Open a new Tmux session:
```bash
tmux new -s torrust-tracker
```

2\. While in Tmux session, run the torrust-tracker:
```bash
cd /opt/torrust/torrust-tracker
./target/release/torrust-tracker
```

3\. Detach from Tmux session:
```
CTRL+B
D
```

## Announce URL
|             | Default                          | NGINX                                |
|-------------|----------------------------------|--------------------------------------|
| UDP         | udp://TRACKER_IP:PORT/announce   | X                                    |
| HTTP        | http://TRACKER_IP:PORT/announce  | http://tracker.YOUR_DOMAIN/announce  |
| HTTPS (SSL) | https://TRACKER_IP:PORT/announce | https://tracker.YOUR_DOMAIN/announce |
