# cimd

OAuth Client ID Metadata Documents (CIMD), served with GitHub Pages.

A client's `client_id` is the URL of its document, for example
`https://zinscky.github.io/cimd/clients/my-mcp.json`.

## Adding a client

1. Create `clients/<name>.json`.
2. Set `client_id` to that file's Pages URL, exactly. cidaas compares the two byte for byte.
3. Commit and push to `main`. Pages may take up to 10 minutes to serve the change.

## Rules cidaas applies when it fetches a document

- **Fetch**:
  - The document must be served from an `https` URL, with a 200 response and `Content-Type: application/json`. Pages sets that type for `.json` files, so always use the extension.
  - Redirects are not followed. Link the exact file URL, never a folder.
  - The document must be at most 16 KiB, and the fetch must finish within 3 seconds.
- **URL**: no query, fragment, `.` / `..` segments or IP host, and at most 512 characters.
- **Auth**: `token_endpoint_auth_method` is `none` or `private_key_jwt`. No `client_secret`. `private_key_jwt` needs exactly one of `jwks` / `jwks_uri`.
- **Redirect URIs**: never `localhost`. Use `127.0.0.1` or `[::1]` instead. Plain `http` is allowed only for loopback IPs.
- **Ceilings**: grant types, response types, scopes and auth methods must fit the tenant's CIMD policy ceilings.

## Tenant setup

In the tenant's CIMD policy, add a host rule: host `zinscky.github.io`, path prefix
`/cimd/`, effect `allow`. Do not use `*.github.io`, which would admit any GitHub user's
documents.

`.nojekyll` makes Pages serve the files exactly as committed.
