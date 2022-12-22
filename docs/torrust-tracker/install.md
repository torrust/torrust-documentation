# Installing the Tracker

## Global Prerequisites

- [Git](https://git-scm.com) - Version Control.
- [cURL](https://curl.se/) - Command line tool and library for transferring data with URLs.
- [Rust/Cargo version => v1.60.0](https://www.rust-lang.org/) - Compiler toolchain & Package Manager (cargo).
- (Optional) [Tmux](https://linuxize.com/post/getting-started-with-tmux/) - Run processes in the background.

## Install Prerequisites

* OpenSSL:
  - for Arch Linux: ```sudo pacman -S pkg-config openssl```
  - for Debian/Ubuntu: ```sudo apt-get install pkg-config libssl-dev```
- SQLite3:
  - for Debian/Ubuntu: ```sudo apt-get install libsqlite3-dev```

## Installation

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

> If you run into errors here, try running : `rustup update stable` before building.

3\. Run the torrust-tracker once to create the `config.toml` file:

```bash
./target/release/torrust-tracker
```

4\. Edit the newly created `config.toml` file (See: [Configuration](https://torrust.com/torrust-tracker/config/)):

```bash
nano config.toml
```

Example `config.toml`:

```toml
log_level = "info"
mode = "private"
db_driver = "Sqlite3"
db_path = "data.db"
announce_interval = 120
min_announce_interval = 120
max_peer_timeout = 900
on_reverse_proxy = false
external_ip = "0.0.0.0"
tracker_usage_statistics = true
persistent_torrent_completed_stat = false
inactive_peer_cleanup_interval = 600
remove_peerless_torrents = true

[[udp_trackers]]
enabled = false
bind_address = "0.0.0.0:6969"

[[http_trackers]]
enabled = true
bind_address = "0.0.0.0:6969"
ssl_enabled = false
ssl_cert_path = ""
ssl_key_path = ""

[http_api]
enabled = true
bind_address = "127.0.0.1:1212"

[http_api.access_tokens]
admin = "MyAccessToken"
```

5\. Allow the port from `bind_address` (default: 6969):
> If you are using a reverse proxy like NGINX, you can skip this step.

```bash
sudo ufw allow 6969
```

### Setup SSL (optional)

> If you are using a reverse proxy like NGINX, you can skip this step and use NGINX for the SSL instead.

1\. Edit your `nano.config` file and change the following settings:

```toml
...
[[http_trackers]]
...
ssl_enabled = true
ssl_cert_path = "YOUR_CERT_PATH"
ssl_key_path = "YOUR_CERT_KEY_PATH"
...
```

## Installation behind NGINX or Apache reverse proxy

Follow steps 1-4 from install above.

5\. Change the following settings in `config.toml`:

```toml
...
on_reverse_proxy = true
...
[[http_trackers]]
bind_address = "127.0.0.1:6969"
ssl_enabled = false
...
```

### NGINX

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

### Apache

6\. Create an Apache config for the tracker (example: tracker.torrust.com):
> Make sure to use your own domain name instead.

```bash
sudo nano /etc/apache2/sites-available/tracker.torrust.com.conf
```

6\.1\. Insert the example configuration:
> Don't copy the SSL comment and make sure to change the domain name to yours.

```apache
# HTTP only (without SSL)

<VirtualHost *:80>
    ServerAdmin webmaster@tracker.torrust.com
    ServerName tracker.torrust.com

    <Proxy *>
        Order allow,deny
        Allow from all
    </Proxy>

    ProxyPreserveHost On
    ProxyRequests Off
    AllowEncodedSlashes NoDecode

    ProxyPass / http://localhost:6969/
    ProxyPassReverse / http://localhost:6969/
    ProxyPassReverse / http://tracker.torrust.com/

    RequestHeader set X-Forwarded-Proto "http"
    RequestHeader set X-Forwarded-Port "80"

    ErrorLog ${APACHE_LOG_DIR}/tracker.torrust.com-error.log
    CustomLog ${APACHE_LOG_DIR}/tracker.torrust.com-access.log combined
</VirtualHost>
```

> Make sure to change the SSLCertificateFile and SSLCertificateKeyFile paths.

```apache
# HTTPS only (with SSL - force redirect to HTTPS)

<VirtualHost *:80>
    ServerAdmin webmaster@tracker.torrust.com
    ServerName tracker.torrust.com

    <IfModule mod_rewrite.c>
        RewriteEngine on
        RewriteCond %{HTTPS} off
        RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
    </IfModule>
</VirtualHost>

<IfModule mod_ssl.c>
    <VirtualHost *:443>
        ServerAdmin webmaster@tracker.torrust.com
        ServerName tracker.torrust.com

        <Proxy *>
            Order allow,deny
            Allow from all
        </Proxy>

        ProxyPreserveHost On
        ProxyRequests Off
        AllowEncodedSlashes NoDecode

        ProxyPass / http://localhost:3000/
        ProxyPassReverse / http://localhost:3000/
        ProxyPassReverse / http://tracker.torrust.com/

        RequestHeader set X-Forwarded-Proto "https"
        RequestHeader set X-Forwarded-Port "443"

        ErrorLog ${APACHE_LOG_DIR}/tracker.torrust.com-error.log
        CustomLog ${APACHE_LOG_DIR}/tracker.torrust.com-access.log combined

        SSLCertificateFile CERT_PATH
        SSLCertificateKeyFile CERT_KEY_PATH
    </VirtualHost>
</IfModule>
```

7\. Enable the configuration by making a symlink to the config in the `sites-enabled` directory.
> Replace tracker.torrust.com with your domain/Apache config.

```bash
sudo ln -s /etc/apache2/sites-available/tracker.torrust.com.conf /etc/apache2/sites-enabled/
# or 
sudo a2enssite tracker.torrust.com
```

8\. After this you can test the validity of the config by executing `sudo apache2ctl -t`,
  if the config is valid you can safely reload Apache to make the new configuration active:

```bash
sudo systemctl reload apache2
```

<br>

__[How to run torrust-tracker](https://torrust.com/torrust-tracker/usage/)__
