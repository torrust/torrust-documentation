# REST API

## Entities (v1)
The API has been split up in three main entities, each containing one or multiple underlying routes.

- [`/user`](user_api.md) - all authentication and user routes.
- [`/torrent`](torrent_api.md) - all individual torrent routes, no collections.
- [`/category`](category_api.md) - routes for getting categories and all torrents in a specific category.

## Authorization
Some routes can only be accessed by logged in users.
For these routes you have to send the token obtained in [/user/login](user_api.md#login) in the `Authorization` header as a bearer token.
<br><br>
For example:
```http
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJleGFtcGxlX3VzZXIiLCJleHAiOjE2MzIyNTQxNjZ9.kyugZXiR88q4n6Ze44HonazDp-sJdq886te9-XHthHg
```

<a id="errors"></a>
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

## Example using [curl](https://curl.se/)
Here's an example of uploading a torrent over the API.

```
curl -X POST 'http://localhost:3000/torrent/upload' \
-H 'Content-Type: multipart/form-data' \
-H 'Authorization: Bearer TOKENHERE' \
-F 'torrent=@"/path/to/your.torrent";type=application/x-bittorrent' \
-F 'title="TITLE"' \
-F 'description="DESCRIPTION"' \
-F 'category="CATEGORY"'
```


