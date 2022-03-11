# Installation
This guide walks you through how to install Torrust on a Linux system. Make sure you have the prerequisites installed before going to the next steps.
> We'll be installing Torrust in `/opt/torrust`, change the paths in the steps according to your own install location.

> Also replace `YOUR_DOMAIN` with the domain you want to host your Torrust instance on.

## Global Prerequisites
- [Git](https://git-scm.com) - Version Control
- [cURL](https://curl.se/) - command line tool and library
  for transferring data with URLs
- [Rust/Cargo](https://www.rust-lang.org/) - Compiler toolchain & Package Manager (cargo)
- [Tmux](https://linuxize.com/post/getting-started-with-tmux/) - With Tmux you can easily switch between multiple programs in one terminal, detach them and reattach them to a different terminal


### Install Global Prerequisites
* Git: ```sudo apt-get install git```
* cURL: ```sudo apt-get install curl```
* Rust/Cargo: 
    1. ```curl https://sh.rustup.rs -sSf | sh```
    2. ```1``` Proceed with installation (default)
    3. ```source $HOME/.cargo/env```
* Build-tools:
    * for Arch Linux: ```sudo pacman -S base-devel```
    * for Debian/Ubuntu: ```sudo apt install build-essential```
* Tmux:
    * for Debian/Ubuntu: ```sudo apt install tmux```


## Installing the Torrust Tracker
[//]: # (### Getting Started)
[//]: # (You can get the latest binaries from [releases]&#40;https://github.com/torrust/torrust-tracker/releases&#41; or follow the install from scratch instructions below.)

### Install Prerequisites
* OpenSSL:
    * for Arch Linux: ```sudo pacman -S pkg-config openssl```
    * for Debian/Ubuntu: ```sudo apt-get install pkg-config libssl-dev```
* SQLite3:
    * for Debian/Ubuntu: ```sudo apt-get install libsqlite3-dev```

### Install & Build
* Create the torrust install directory (if you haven't already) and clone the repo:
```bash
mkdir /opt/torrust
cd /opt/torrust
git clone https://github.com/torrust/torrust-tracker.git
cd torrust-tracker
```

* Build the source code.
```bash
cargo build --release
```

### Usage
* Run the torrust-tracker once to create the `config.toml` file:
```bash
./target/release/torrust-tracker
```

* Edit the newly created config.toml file ([config documentation](https://torrust.com/torrust-tracker/config/)) according to your liking:
```bash
nano config.toml
```

* Open a new Tmux session:
```bash
tmux new -s torrust-tracker
```

* While in Tmux session, run the torrust-tracker again:
```bash
cd /opt/torrust/torrust-tracker
./target/release/torrust-tracker
```

* Detach from Tmux session: `Ctrl+b` `d`

_Your tracker announce URL will be **udp://{tracker-ip:port}** or **https://{tracker-ip:port}/announce** depending on your tracker mode.
In private & private_listed mode, tracker keys are added after the tracker URL like: **https://{tracker-ip:port}/announce/{key}**._


## Installing the Torrust Index
**_The Torrust Index ONLY works with a Torrust Tracker. Other BitTorrent trackers will NOT work._**

### Install Prerequisites
The frontend can't run on its own and needs and external webserver like Apache or Nginx.
In this guide we will be using Nginx.

* Node (version >= 12.0.0)
    * for Debian/Ubuntu: 
```bash
curl -fsSL https://deb.nodesource.com/setup_12.x | bash -
apt-get install -y nodejs
```
* NPM
    * for Debian/Ubuntu: ```sudo apt-get install npm```
* Nginx:
    * for Debian/Ubuntu: ```sudo apt install nginx```
    * for other distros: see [Nginx installation tutorial](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)


### Install & Build
* Create the torrust install directory (if you haven't already) and clone the repo:
```bash
mkdir /opt/torrust
cd /opt/torrust
git clone https://github.com/torrust/torrust.git
```

* Change to the backend directory and create a file called: `.env`:
```bash
cd torrust/backend
echo "DATABASE_URL=sqlite://data.db?mode=rwc" > .env
```

* Then we have to create the SQLite database and run the migrations. Install the `sqlx-cli` and setup the database.
```bash
cargo install sqlx-cli
sqlx db setup
```

After this build the backend:
```bash
cargo build --release
```

### Configuration
* Run the backend once to generate the config.toml file:
```bash
cd /opt/torrust/torrust/backend
./target/release/torrust
```

* Then edit the `config.toml` and change at least the following keys:

```bash
nano config.toml
```

`[tracker]`

- `url`: Set to a connection string for the tracker. Eg: `udp://YOUR_DOMAIN:6969/announce/`. 
- `api_url`: Set to tracker api URL. Default: `http://localhost:1212`.
- `token`: Set this to an access token from the Torrust Tracker config.toml.

`[net]`

- `port`: Set to `3000` for this guide. (If you choose another port make sure to change the Nginx config as well)

`[auth]`

- `secret_key`: Set to a __SECURE__ randomly generated string.

### Running the Backend
With the changes to the configuration the backend should be completely ready to be run.
Just like the tracker it should be run in a screen / tmux session or as a systemd service.

```bash
tmux new -s torrust-index
cd /opt/torrust/torrust/backend
./target/release/torrust
```
> Press `CTRL+B D` to exit the tmux session without killing it.

### Building the Frontend
* Start with creating a file called '.env' (**Make sure to change YOUR_DOMAIN**):
```bash
cd /opt/torrust/torrust/frontend
echo "VITE_API_BASE_URL=https://YOUR_DOMAIN/api" > .env
```

* Build the frontend:
```bash
npm i
npm run build
```
After this command successfully completed, a built version of the frontend is in the `dist` folder.
These files will be served by Nginx in the next steps.

### Nginx Configuration
* Create a file in `/etc/nginx/sites-available/` called `torrust.conf` with the following contents (**Make sure to change YOUR_DOMAIN**):
```nginx
server {
    if ($host = YOUR_DOMAIN) {
        return 301 https://$host$request_uri;
    }
    listen 80;
    server_name YOUR_DOMAIN;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name YOUR_DOMAIN;
    ssl_certificate CERT_PATH;
    ssl_certificate_key CERT_KEY_PATH;

    root /opt/torrust/torrust/frontend/dist/;
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    location /api/ {
        proxy_pass http://127.0.0.1:3000/;
    }
}
```

* Enable the configuration by making a symlink to the config in the `sites-enabled` directory.
```bash
ln -s /etc/nginx/sites-available/torrust.conf /etc/nginx/sites-enabled/
```

* After this you can test the validity of the config by executing `nginx -t`,
if the config is valid you can safely reload Nginx to make the new configuration active.
```bash
sudo service nginx restart
```
### Systemd examples
You'll need to create two service files:  
`/etc/systemd/system/torrust.service`  
`/etc/systemd/system/torrust-tracker.service`

Tracker:

```
[Unit]
After=network.service
Description="torrust-tracker"

[Service]
WorkingDirectory=/opt/torrust/torrust-tracker
ExecStart=/opt/torrust/torrust-tracker/target/release/torrust-tracker
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

Index:

```
[Unit]
After=network.service
Description="torrust"

[Service]
WorkingDirectory=/opt/torrust/torrust/backend
ExecStart=/opt/torrust/torrust/backend/target/release/torrust
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

Enable both services via `systemctl enable torrust`, `systemctl enable torrust-tracker`.
Now you manage them just like other services: `service torrust start|stop|restart` etc.  
Both will log directly to syslog (`/var/log/syslog`) by default.
