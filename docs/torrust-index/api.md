# Index REST API

> This page is heavy WIP and missing a lot of endpoints from the newest torrust-index.

## Authorization

Some routes can only be accessed by logged in users.
For these routes you have to send the token obtained in [/user/login](api.md#login) in the `Authorization` header as a bearer token.

For example:

```http
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJleGFtcGxlX3VzZXIiLCJleHAiOjE2MzIyNTQxNjZ9.kyugZXiR88q4n6Ze44HonazDp-sJdq886te9-XHthHg
```

## Errors

Every route always returns a non `2xx` status code for an error.
If the error is caused by user input the status code will be in the `4xx` range.
Unrecoverable server errors return a `500` status code.

Every error contains a error message that is safe to display to an user.
The body of an error _should_ always look like this:

```json
{
  "error": "<error message>"
}
```

## Torrent

### `GET /torrents`

Get all torrent information of the listing with `id`, used for loading the data of the TorrentDetails page.

__Optional query params:__

| Param      | Description                 | Value                                                                                                                                             | Example                        |
|------------|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| categories | Included categories.        | Comma seperated list.                                                                                                                             | ?categories=music,other,movies |
| search     | Search term.                | String.                                                                                                                                           | &search=bunny                  |
| sort       | Sorting and sort direction. | `uploaded_ASC`, `uploaded_DESC`, `seeders_ASC`, `seeders_DESC`, `leechers_ASC`, `leechers_DESC`, `name_ASC`, `name_DESC`, `size_ASC`, `size_DESC` |&sort=size_DESC|

__Response:__

- On success: Status `200`.

```json
{
  "data": {
    "total": 1,
    "results": [
      {
        "torrent_id": 1,
        "uploader": "test",
        "info_hash": "bc03e1a08565f8f09bed7c10aad3e6e7771a88fc",
        "title": "Lucky.The.Labrador.IMG.PNG",
        "description": "",
        "category_id": 5,
        "upload_date": 1646783943,
        "file_size": 1710641,
        "seeders": 0,
        "leechers": 0
      }
    ]
  }
}
```

---

### `POST /torrent/upload`

Upload a torrent file and create a torrent listing for it in the index.

Consumes a __multipart__ form with the fields:

- `title`: Title of the torrent listing.
- `description`: A Markdown description.
- `category`: Category this torrent fits in.
- `torrent`: The torrent file itself.

__Response:__

- On success: Status `200`, returns id of the newly created torrent listing.

```json
{
  "data": {
    "torrent_id": 1
  }
}
```

- On error: Standard error, see [Errors](api.md#errors)

---

### `GET /torrent/download/<id>`

Generate and download torrent file with a personal announce URL for the authenticated user.

__Response:__

- On success: Status `200`, Personalized .torrent file stream.
- On error: Standard error, see [Errors](api.md#errors)

---

### `GET /torrent/<id>`

Get all torrent information of the listing with `id`, used for loading the data of the TorrentDetails page.

__Response__:

- On success: Status `200`.

```json
{
  "data": {
    "torrent_id": 1,
    "uploader": "example_user",
    "info_hash": "5499b9f42b44fb61c937be5943a194adb7aa6278",
    "title": "Example torrent",
    "description": "## Some torrent title\n\nSome torrent text.\n\n---",
    "category_id": 1,
    "upload_date": 1631046870,
    "file_size": 15653809,
    "seeders": 0,
    "leechers": 0,
    "files": null
  }
}
```

- On error: Standard error, see [Errors](api.md#errors)

---

## Category

### `GET /category`

Get a list of existing categories.

__Response__:

- On success: Status `200`.

```json
{
  "data": [
    {
      "name": "app",
      "num_torrents": 0
    },
    {
      "name": "documentary",
      "num_torrents": 0
    },
    {
      "name": "game",
      "num_torrents": 0
    },
    {
      "name": "movie",
      "num_torrents": 1
    },
    {
      "name": "music",
      "num_torrents": 0
    },
    {
      "name": "other",
      "num_torrents": 0
    },
    {
      "name": "tv show",
      "num_torrents": 0
    }
  ]
}
```

- On error: Standard error, see [Errors](api.md#errors)

---

## User

### `POST /user/register`

Register (sign-up) a new user account.

`Example: POST /user/register`

__Body__:

```json
{
  "email": "email@example.com",
  "username": "example_user",
  "password": "password",
  "confirm_password": "password"
}
```

__Response__:

- On success: Status `200`, with an empty body.
- On error: Standard error, see [Errors](api.md#errors)

---

### `POST /user/login`

Login into an existing account.

__Fields__:

- `login`: Either the email or username of the account
- `password`: password of the account

`Example: POST /user/login`

__Body__:

```json
{
  "login": "email@example.com",
  "password": "password"
}
```

__Response__:

- On success: Status `200`.

```json
{
  "data": {
    "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJleGFtcGxlX3VzZXIiLCJleHAiOjE2MzIyNTQxNjZ9.kyugZXiR88q4n6Ze44HonazDp-sJdq886te9-XHthHg",
    "username": "example_user"
  }
}
```

- On error: Standard error, see [Errors](api.md#errors)

---

### `GET /user/verify/<token>`

Email verification handler.

On register an email is sent to the email address of the registered account with a link to this route to verify their email address.

`Example: GET /user/verify/<token>`

__Response__:

- On success: Status `200`.

```html
Email verified, you can close this page.
```

- On error: Error message as a string.

```html
Token invalid.
```

---

### `DELETE /user/ban/<username>`

Ban users.

`Example: DELETE /user/verify/<username>`
__Response__:

- On success: Status `200`.

```json
{
  "message": "User successfully banned!"
}
```

- On error: Error message as a string.

```json
{
  "error": "Could not find user."
}
```

---
