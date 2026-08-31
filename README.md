# relay

Machine-to-machine relay for Claude workflows. One folder per project. Nothing secret ever enters
this repo — it is public by design.

## How it is used

A machine (today: the Jahjah VPS work engine) writes reports here and pushes. A reader — a person, or
a Claude session in another chat — fetches them over plain HTTPS, with no token and no clone:

- raw file: `https://raw.githubusercontent.com/obidex/relay/main/<path>`
- directory listing: `https://api.github.com/repos/obidex/relay/contents/<dir>`

## Layout

```
<project>/reports/     machine reports, newest last, named <UTC-date>-<subject>.md
```

## The one rule

**Nothing secret ever enters this repo.** No key, token, password, connection string, private IP that
matters, customer data, or a real person's name. Anything written here is world-readable forever, and
deleting it later does not un-publish it. If a value's usefulness depends on it staying private, it
does not belong in a file under this repo.
