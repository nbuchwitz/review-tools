# review-tools

Small helpers for kernel patch review.

## sashiko-bugs

A command line client for the [sashiko.dev](https://sashiko.dev) bug
database. It lists, shows and triages the bugs that sashiko reviews find.
It needs Python 3 and nothing outside the standard library.

### Getting a token

The API wants a bearer token, which you mint from a logged-in browser
session.

1. Log in on sashiko.dev with the email magic link.
2. Open the browser console (F12) on any sashiko.dev page and run:

   ```js
   fetch('/api/auth/token', {method: 'POST',
     headers: {'Content-Type': 'application/json'},
     body: JSON.stringify({max_bug_access: 'manage', expires_in_days: 30})})
     .then(r => r.json()).then(j => console.log(j.token))
   ```

   `max_bug_access` caps what the token may do. `read` allows list and show,
   `comment` adds comments, and `manage` adds close, dismiss, dup and assign.
   The cap never grants more than your account has through MAINTAINERS. The
   server clamps `expires_in_days` to 1..90.
3. Save it with `sashiko-bugs token-save`, which reads the token from stdin,
   or export `SASHIKO_API_TOKEN`.

The script looks for the token in `--token`, then `$SASHIKO_API_TOKEN`, then
`~/.config/sashiko/token`. It talks to `https://sashiko.dev` unless you set
`--server` or `$SASHIKO_SERVER`.

### Usage

```sh
sashiko-bugs list genet --status open --file bcmgenet
sashiko-bugs list --subsystem "BROADCOM GENET ETHERNET DRIVER" --json
sashiko-bugs show 460
sashiko-bugs comment 460 "text"
sashiko-bugs close 460 -r "fixed by <sha>"
sashiko-bugs dismiss 460 -r "false positive: ..."
sashiko-bugs dup 4011 460 -r "same root cause"
sashiko-bugs assign 460 user@example.com    # '' to unassign
sashiko-bugs token-info                     # decode the token locally
```

A bug can be given by its numeric id or its bugid (`linux-<uuid>`).

## License

BSD-3-Clause, see [LICENSE](LICENSE).
