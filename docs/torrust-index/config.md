# Configuring Torrust
Torrust's configuration is a simple TOML file. If no TOML file is found, it will fail on startup.

## Must change
> These are all the configuration options that can affect the security of your instance. Please make sure to change these to your own values.
- `tracker.token`
- `auth.secret_key`

## Configuration

### Tracker
- `REQUIRED` `url`: public UDP url of the Torrust Tracker instance.
- `REQUIRED` `api_url`: URL of the Torrust Tracker API, usually `http://localhost:1212`.
- `REQUIRED` `token`: token configured in the Torrust Tracker configuration.
- `REQUIRED` `token_valid_seconds`: Lifetime of a tracker key.

### NET
- `REQUIRED` `port`: The port the API will listen on. It's not advised to use ports under 1024 because root access is required for these ports.
- `OPTIONAL` `base_url`: The URL this application is accesible from. Used to build the email verification URL. If not set it uses the hostname the endpoint was called from.


### Database
- `REQUIRED` `connect_url`: The connection URL of the database. Should always start with `sqlite:`, no other databases are supported as of now.Including `mode=rwc` allows the database to be `Read / Writed / Created`. Example: `sqlite://data.db?mode=rwc`
- `REQUIRED` `torrent_info_update_interval`: Interval in seconds for updating torrent seeder and leecher information. This can be a heavy operation depending on the amount of torrents that are tracked, and thus is not recommended to be lower than `1800` seconds.

### Mail
- `REQUIRED` `server`: Hostname or IP address of a SMTP server.
- `REQUIRED` `port`: Port of the SMTP server.
- `REQUIRED` `username`: Username for authenticating with the specified SMTP server.
- `REQUIRED` `password`: Password for authenticating with the specified SMTP server.
- `REQUIRED` `from`: Email address where emails are sent from.
- `REQUIRED` `reply_to`: Email address to which replies on the emails should be sent. Can also be a non reply address, or the same as the from address.

### Auth
- `REQUIRED` `min_password_length`: Minimum length of a password when registering a new user.
- `REQUIRED` `max_password_length`: Maximum length of a password when registering a new user.
- `REQUIRED` `secret_key`: Signing key of the JWT authentication tokens. Keeping these default will severly impact the security of your instance, and allows attackers to login as any user.

### Storage
- `REQUIRED` `upload_path`: Path where uploads should be stored. Directories will be automatically created on startup if they don't exist.

## Default
```toml
[website]
name = "Torrust"

[tracker]
url = "udp://torrust.com:6969/announce"
api_url = "http://localhost:1212"
token = "MyAccessToken"
token_valid_seconds = 7257600

[net]
port = 3000

[auth]
min_password_length = 6
max_password_length = 64
secret_key = "MaxVerstappenWC2021"

[database]
connect_url = "sqlite://data.db?mode=rwc"
torrent_info_update_interval = 3600

[storage]
upload_path = "./uploads"

[mail]
email_verification_enabled = false
from = "example@email.com"
reply_to = "noreply@email.com"
username = ""
password = ""
server = ""
port = 25
```
