## What was the bug?
`Client.request(..., api=True)` failed to refresh OAuth2 tokens when `Client.oauth2_token` was a `dict`. In that case, no `Authorization` header was added to the prepared request.

## Why did it happen?
The refresh logic only handled two situations:
- the token was missing (`None` / falsy), or
- the token was an `OAuth2Token` instance that was expired.

A `dict` token is truthy and not an `OAuth2Token`, so it skipped refresh and also skipped header-setting (which only runs for `OAuth2Token`).

## Why does your fix actually solve it?
The fix treats any non-`OAuth2Token` value (including `dict`) as invalid for API calls and forces a refresh. After refreshing, `oauth2_token` becomes an `OAuth2Token`, so the `Authorization` header is set deterministically.

## What’s one realistic case / edge case your tests still don’t cover?
If an `OAuth2Token` is close to expiring (e.g., within a small grace window) we still treat it as valid because we only check strict expiry. A production client often refreshes slightly before expiry to avoid race conditions.
