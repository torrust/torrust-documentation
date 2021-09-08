# Installation
This guide walks you through how to install Torrust on a Linux system. Make sure you have the prerequisites installed, before going to the next steps.
> We'll be installing Torrust in `/opt/torrust`, change the paths in the steps according to your own install location.

> Also replace `torrust.dutchbits.nl` with the domain you want to host your Torrust instance on.

## Prerequisites
- [Git](https://git-scm.com) - Version Control
- [Rust](https://www.rust-lang.org/) - Compiler toolchain & Package Manager (cargo)
- [Node.js](https://nodejs.org/en/) - A JavaScript runtime
- [NPM](https://www.npmjs.com/) - Package manager for Node/JavaScript

## Setting up Nginx
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
    listen 80;
    server_name torrust.dutchbits.nl; # set your own domain name here!!

    root /opt/torrust/torrust-web-frontend/dist/;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://localhost:8080/;
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

## Setting up the Torrust Backend
### Getting the sources
If you prefer to just download the compiled binaries, you can get the [latest release here](https://github.com/torrust/torrust-web-backend/releases). Else:

While in `/opt/torrust`:
```bash
git clone https://github.com/torrust/torrust-web-backend.git
```

### Building (Skip if you downloaded the binaries)
Before building we have to create the SQLite database and run the migrations. Install the `sqlx-cli` and setup the database.
```bash
cd /opt/torrust/torrust-web-backend
cargo install sqlx-cli
sqlx db setup
```

After this build the backend just like you built the tracker:
```bash
cargo build --release
```

### Configuration
Edit the `config.toml` and change at least the following keys.

`[tracker]`

- `url`: Set to a connection string for the tracker.
- `token`: Set this to the randomly generated key for the `torrust_api` when setting up the Tracker.

`[net]`

- `port`: Set to `8080` for this guide. (If you choose another port make sure to change the Nginx config aswell)
- `base_url`: The URL the backend is accesible to from the outside, should be your domain appended by `/api`, ex: `https://torrust.dutchbits.nl/api`.

`[mail]`

- Change all fields to match the details of the SMTP provider of your choice.

Not sure what to use? Both [SendGrid](https://sendgrid.com/) and [Sendinblue](https://www.sendinblue.com/) have a generous free tier.

`[auth]`

- `secret_key`: Set to a __LONG__ randomly generated string.

### Running the Backend
With the changes to the configuration the backend should be completely ready to be run.
Just like the Tracker it should be run in a screen / tmux session or as a systemd service.

```bash
tmux # open a new tmux session
./target/release/torrust-web-backend
```
> Press `CTRL+B D` to exit the tmux session without killing it.

## Setting up the Torrust Web Frontend
## Getting the sources
```bash
git clone https://github.com/torrust/torrust-web-frontend
```

### Building the Frontend
Next up is building the frontend, we'll first install all of the dependecies.

While in `/opt/torrust`:
```bash
cd /opt/torrust-web-frontend
npm i
```

Edit the `.env.production` file to look like this, and after that we can start the build.
```env
VUE_APP_API_BASE_URL=/api
```
```bash
npm run build
```
After this command succesfully completed a built version of the frontend is in the `dist` folder.
These files are served by Nginx as specified by the `root` directive in the created config.

