## What was the bug?
`Client.request(api=True)` did not refresh tokens when `oauth2_token` was a `dict`, so no `Authorization` header was added.

## Why did it happen?
The refresh logic only handled `None` or expired `OAuth2Token` instances. Dictionary (`dict`) tokens were ignored.

## Why does your fix solve it?
Any non-`OAuth2Token` (including `dict`) now triggers a refresh. After refresh, `oauth2_token` is an `OAuth2Token`, so the header is set correctly.

## Edge case not covered
Tokens close to expiry are still treated as valid; a real client might refresh slightly early to avoid race conditions.