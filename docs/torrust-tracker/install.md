# Installing the Torrust Tracker
## Global Prerequisites
- [Git](https://git-scm.com) - Version Control.
- [cURL](https://curl.se/) - Command line tool and library for transferring data with URLs.
- [Rust/Cargo](https://www.rust-lang.org/) - Compiler toolchain & Package Manager (cargo).
- (Optional) [Tmux](https://linuxize.com/post/getting-started-with-tmux/) - Run processes in the background.

## Install Prerequisites
* OpenSSL:
    * for Arch Linux: ```sudo pacman -S pkg-config openssl```
    * for Debian/Ubuntu: ```sudo apt-get install pkg-config libssl-dev```
* SQLite3:
    * for Debian/Ubuntu: ```sudo apt-get install libsqlite3-dev```

## Install
1\. Create the torrust install directory (if you haven't already) and clone the repo:
```bash
mkdir /opt/torrust
cd /opt/torrust
git clone https://github.com/torrust/torrust-tracker.git
```

2\. Build the source code:
```bash
cd torrust-tracker
cargo build --release
```

3\. Run the torrust-tracker once to create the `config.toml` file:
```bash
./target/release/torrust-tracker
```

4\. Edit the newly created `config.toml` file (see: [Config Documentation](https://torrust.com/torrust-tracker/config/)):
```bash
nano config.toml
```

5\. Allow the port from `bind_address` (default: 6969):
> If you are using a reverse proxy like NGINX, you can skip this step.
```bash
sudo ufw allow 6969
```

### (optional) Use SSL
> If you are using a reverse proxy like NGINX, you can skip this step and use NGINX for the SSL instead.

1\. Edit your `nano.config` file and change the following settings:
```toml
...
[http_tracker]
...
ssl_enabled = true
ssl_cert_path = "YOUR_CERT_PATH"
ssl_key_path = "YOUR_CERT_KEY_PATH"
...
```

## Install with NGINX
Follow steps 1-4 from install above.

5\. Change the following settings in `config.toml`:
```toml
...
[http_tracker]
enabled = true
bind_address = "127.0.0.1:6969"
on_reverse_proxy = true
...
```

6\. Create an NGINX config for the tracker (example: tracker.torrust.com): 
> Make sure to use your own domain name instead.
```bash
sudo nano /etc/nginx/sites-available/tracker.torrust.com
```

6\.1\. Insert the example configuration:
> Don't copy the SSL comment and make sure to change the domain name to yours.
```nginx
# without SSL

server {
    listen 80;
    server_name tracker.torrust.com;
    
    location / {
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_pass http://127.0.0.1:6969;
    }
}
```
> Make sure to change the ssl_certificate paths.
```nginx
# with SSL

server {
    listen 80;
    server_name tracker.torrust.com;
    
    return 301 https://$host$request_uri;
}

server {
    listen 443;
    server_name tracker.torrust.com;
    
    ssl_certificate CERT_PATH
    ssl_certificate_key CERT_KEY_PATH; 
    
    location / {
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_pass http://127.0.0.1:6969;
    }
}
```

7\. Enable the configuration by making a symlink to the config in the `sites-enabled` directory.
> Replace tracker.torrust.com with your domain/NGINX config.
```bash
ln -s /etc/nginx/sites-available/tracker.torrust.com /etc/nginx/sites-enabled/
```

8\. After this you can test the validity of the config by executing `nginx -t`,
  if the config is valid you can safely reload Nginx to make the new configuration active:
```bash
sudo systemctl reload nginx
```

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
