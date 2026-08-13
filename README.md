# distribution.version.battle.net archive

An exact mirror of Blizzard's `distribution.version.battle.net` version service.
Files are added, never modified — each commit is one observed change.

## Layout

```
summaries/<md5>          one file per distinct /summary body served
blue/<h0:2>/<h2:4>/<h>   content-addressed JSON store, mirroring the URL paths
```

`/summary` names the current channel hash and the store prefix:

```json
{"public":{"channel":"a2d61f64456da5bd146b12752a48ac30"},"path":"blue/"}
```

That channel hash is the root manifest: every product with its
`builds[].definition`, `regions[].cdns` and optional `preloads[].definition`
hashes. A `definition` blob is a build (`versionString`, `buildKey`, `cdnKey`,
`productConfig`); a `cdns` blob is a CDN host list.

`buildKey`, `cdnKey` and `productConfig.hash` point at the TACT CDN
(`tpr/...` on `*.cdn.blizzard.com`) — a different service, not mirrored here.

## Integrity

Every file is named by the MD5 of its own contents, so the archive verifies itself:

```bash
find . -type f -not -path './.git/*' ! -name README.md | while read -r f; do
  [ "$(md5sum <"$f" | cut -d' ' -f1)" = "$(basename "$f")" ] || echo "BAD $f"
done
```

The newest summary is the one added by the most recent commit:

```bash
git show --name-only --format= HEAD | grep '^summaries/'
```
