# Access token API

The Access token API allows you to inspect and revoke an API access token. This can be useful if you find a token, can't identify its owner, and you want to revoke it.

>📘
> All the endpoints expect the token to be provided using the <a href="/docs/apis/rest-api#authentication">Authorization HTTP header</a>.

{:toc}

## Get the current token

Returns details about the API access token that was used to authenticate the request.

```bash
curl "https://api.buildkite.com/v2/access-token"
```

```json
{
  "uuid": "b63254c0-3271-4a98-8270-7cfbd6c2f14e",
  "scopes": ["read_build"]
}
```

Required scope: none

Success response: `200 OK`

## Revoke the current token

Revokes the API access token that was used to authenticate the request. Once revoked, the token can no longer be used for further requests.

```bash
curl -X DELETE "https://api.buildkite.com/v2/access-token"
```

Required scope: none

Success response: `204 No Content`