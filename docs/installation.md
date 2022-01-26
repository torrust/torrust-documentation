# Installation
This guide walks you through how to install Torrust on a Linux system. Make sure you have the prerequisites installed, before going to the next steps.
> We'll be installing Torrust in `/opt/torrust`, change the paths in the steps according to your own install location.

> Also replace `torrust.dutchbits.nl` with the domain you want to host your Torrust instance on.

## Prerequisites
- [Git](https://git-scm.com) - Version Control
- [Rust](https://www.rust-lang.org/) - Compiler toolchain & Package Manager (cargo)
- [Node.js](https://nodejs.org/en/) - A JavaScript runtime
- [NPM](https://www.npmjs.com/) - Package manager for Node/JavaScript

## Setting up the Torrust Index
### Installing Nginx
The frontend can't run on it's own and needs and external webserver like apache or nginx.
In this guide we will be using nginx, so lets install that first.
<br><br>
On debian and ubuntu based distros you can simply execute:
```bash
sudo apt install nginx
```
_See [Nginx installation tutorial](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/) for installation instructions on other distros_

### Nginx configuration
Create a file in `/etc/nginx/sites-available/` called `torrust.conf` with the following contents:
```nginx
server {
    if ($host = torrust.dutchbits.nl) {
        return 301 https://$host$request_uri;
    }

    listen 80;
    server_name torrust.dutchbits.nl;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name torrust.dutchbits.nl;
    ssl_certificate /etc/letsencrypt/live/torrust.dutchbits.nl/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/torrust.dutchbits.nl/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

    root /opt/torrust/torrust/frontend/dist/;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://localhost:3000/;
    }
}
```
Enable the configuration by making a symlink to the config in the `sites-enabled` directory.
```bash
ln -s /etc/nginx/sites-available/torrust.conf /etc/nginx/sites-enabled/
```

After this you can test the validity of the config by executing `nginx -t`,
if the config is valid you can safely reload Nginx to make the new configuration active.
```bash
systemctl reload nginx
```

### Getting the Torrust index sources
Create the torrust install directory and clone the repo:
```bash
mkdir /opt/torrust
cd /opt/torrust
git clone https://github.com/torrust/torrust.git
```

### Building the Backend
First change to the backend directory and create a file called: '.env':
```bash
cd /opt/torrust/torrust/backend
echo "DATABASE_URL=sqlite://data.db?mode=rwc" > .env
```

Then we have to create the SQLite database and run the migrations. Install the `sqlx-cli` and setup the database.
```bash
cargo install sqlx-cli
sqlx db setup
```

After this build the backend:
```bash
cargo build --release
```

### Configuration
Run the backend once to generate the config.toml file:
```bash
cd /opt/torrust/torrust/backend
./target/release/torrust
```

Then edit the `config.toml` and change at least the following keys:

```bash
nano config.toml
```

`[tracker]`

- `url`: Set to a connection string for the tracker. Eg: `udp://torrust.com:6969/announce/`. 
- `api_url`: Set to tracker api URL. Default: `http://localhost:1212`.
- `token`: Set this to the randomly generated key for the `torrust_api` when setting up the tracker.

`[net]`

- `port`: Set to `3000` for this guide. (If you choose another port make sure to change the Nginx config as well)

`[auth]`

- `secret_key`: Set to a __LONG__ randomly generated string.

### Running the Backend
With the changes to the configuration the backend should be completely ready to be run.
Just like the tracker it should be run in a screen / tmux session or as a systemd service.

```bash
tmux # open a new tmux session
cd /opt/torrust/torrust/backend
./target/release/torrust
```
> Press `CTRL+B D` to exit the tmux session without killing it.

### Building the Frontend
Next up is building the frontend. Start with creating a file called '.env':
```bash
cd /opt/torrust/torrust/frontend
echo "VITE_API_BASE_URL=https://torrust.dutchbits.nl/api" > .env
```

Build the frontend:
```bash
npm i
npm run build
```
After this command successfully completed a built version of the frontend is in the `dist` folder.
These files are served by Nginx as specified by the `root` directive in the created config.

