# This folder is clasp's deploy staging area

`appsscript.json` here must be a copy of your **real** Apps Script project
manifest (timezone, web app access settings, etc.) — never hand-written,
since guessing it wrong could silently change how your deployment runs
(e.g. who's allowed to call it).

## One-time setup (do this once, on your own computer — not in Claude's sandbox)

```
npm install -g @google/clasp
clasp login
cd /path/to/this/repo
clasp pull
```

`clasp pull` fetches your project's actual files, including the real
`appsscript.json`, into the folder `.clasp.json` points at (`./appsscript`).
Commit that fetched `appsscript.json` to the repo — that's the only file
that needs to live here permanently. `recharge_backend.gs` itself is NOT
duplicated here; the GitHub Action copies the root copy in at deploy time
so there's one source of truth.

## One honest caveat

I (Claude) have no network access to script.google.com from this
sandbox, so I've never actually run clasp against your real project --
everything above is written from clasp's documented behavior, not a
tested run. The one thing worth double-checking on your **first** real
deploy: whether `clasp pull` names the fetched backend file
`recharge_backend.js` or `recharge_backend.gs` (clasp's convention has
changed across versions). The GitHub Action currently copies the root
`recharge_backend.gs` in under that same `.gs` name -- if `clasp pull`
gives you a `.js` file instead, tell me and I'll adjust the Action's
"Stage the backend file for clasp" step to match (and probably rename
your pulled file to `.gs` to keep one consistent extension, rather than
have two conventions floating around).

## Setting up the GitHub Secret

1. In the GitHub repo: Settings → Secrets and variables → Actions → New
   repository secret.
2. Name: `CLASPRC_JSON`
3. Value: the entire contents of `~/.clasprc.json` from your computer,
   right after running `clasp login` above. Open that file in a text
   editor, copy everything, paste it as the secret's value.
4. This file contains an OAuth token with real access to your Apps
   Script account -- treat it like a password. Never commit it to the
   repo; it only ever lives in this one encrypted GitHub Secret and your
   own computer's `~/.clasprc.json`.
