# Configuring Torrust Tracker
Torrust Tracker's configuration is a simple TOML file. If no TOML file is found, it will run a default configuration.

## Configuration

### Root Level
| Root             | REQUIRED |                                                  | Values                                            | Default   |
|------------------|----------|--------------------------------------------------|---------------------------------------------------|-----------|
| log_level        | OPTIONAL | Log level.                                       | `off`, `info` or `trace`                          | `info`    |
| mode             | REQUIRED | [Tracker mode](/torrust-tracker/tracking_modes/).                    | `public`, `listed`, `private` or `private_listed` | `public`  |
| db_path          | REQUIRED | SQLite DB Path.                                  | Any path.                                         | `data.db` |
| cleanup_interval | OPTIONAL | Seconds until inactive peers/torrents are removed. | Interval in seconds.                              | 600       |
| external_ip      | OPTIONAL | Needs to be set if announcing from local network. | IP like: `100.69.420.117`                         | EMPTY     |

### UDP Tracker Section
| [udp_tracker]     | REQUIRED |                                       | Values                  | Default        |
|-------------------|----------|---------------------------------------|-------------------------|----------------|
| bind_address      | REQUIRED | Bind address + port.                  | Example: `0.0.0.0:6969` | `0.0.0.0:6969` |
| announce_interval | REQUIRED | Interval that peers will announce in. | Interval in seconds.    | 120            |

### HTTP Tracker Section
| [http_tracker]    | OPTIONAL |                                                             | Values                  | Default        |
|-------------------|----------|-------------------------------------------------------------|-------------------------|----------------|
| enabled           | REQUIRED | If false, UDP tracker will run.                             | true or false           | false          |
| bind_address      | REQUIRED | Bind address + port.                                        | Example: `0.0.0.0:7878` | `0.0.0.0:7878` |
| announce_interval | REQUIRED | Interval that peers will announce in.                       | Interval in seconds.    | 120            |
| ssl_enabled       | OPTIONAL | Enable SSL or not. HIGHLY RECOMMENDED for private trackers. | true or false           | false          |
| ssl_cert_path     | OPTIONAL | Path to SSL cert.                                           | Any path.               | EMPTY          |
| ssl_key_path      | OPTIONAL | Path to SSL cert key.                                       | Any path.               | EMPTY          |

### HTTP API Section
| [http_api]   | OPTIONAL |                       | Values                    | Default          |
|--------------|----------|-----------------------|---------------------------|------------------|
| enabled      | REQUIRED | Enables built-in API. | true or false             | true             |
| bind_address | REQUIRED | Bind address + port.  | Example: `127.0.0.1:1212` | `127.0.0.1:1212` |

| [http_api.access_tokens] | REQUIRED IF HTTP API ENABLED |                         | Values | Default                 |
|--------------------------|------------------------------|-------------------------|--------|-------------------------|
| {user}                   | OPTIONAL                     | Token for built-in API. | {key}  | admin = "MyAccessToken" |

## Sample Configuration
```toml
log_level = "trace"
mode = "private"
db_path = "data.db"
cleanup_interval = 600
external_ip = "148.420.69.117"

[udp_tracker]
bind_address = "0.0.0.0:6969"
announce_interval = 120

[http_tracker]
enabled = false
bind_address = "0.0.0.0:7878"
announce_interval = 120
ssl_enabled = false
ssl_cert_path = ""
ssl_key_path = ""

[http_api]
enabled = true
bind_address = "127.0.0.1:1212"

[http_api.access_tokens]
admin = "MyAccessToken"
```
