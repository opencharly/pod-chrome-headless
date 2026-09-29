# pod-chrome-headless

The `chrome-headless` candy of the OpenCharly candy library, as a standalone repo
(the candy de-submodule cutover, kind-prefixed naming). It runs the real
`google-chrome` binary in `--headless=new` mode as a cross-deployment CDP driver.

## What it provides

The real `/usr/bin/google-chrome-stable` in `--headless=new` mode on
`127.0.0.1:9223` — no Wayland compositor, no desktop — via the always-restart
`chrome-headless` supervisord service launched from
`~/.local/bin/chrome-headless-launch`.

| Property | Value |
|---|---|
| Service | `chrome-headless` (`restart: always`) |
| Requires | `layer-chrome`, `layer-supervisord` |
| Composes | `pod-chrome-cdp` (republishes CDP on published `9222`) |
| Launcher | `~/.local/bin/chrome-headless-launch` |
| CDP | internal `127.0.0.1:9223`, published `9222` through the proxy |

It is designed as a **sibling-member driver deployment** that CDP-probes a
separate web-server subject over the shared `charly` network, without baking
Chrome into the subject image. The launcher sets `--remote-allow-origins='*'`
because Chrome 146+ rejects the CDP WebSocket upgrade otherwise. Container-only.

## How to use it

Compose the candy as a sibling member alongside a web-server subject:

```yaml
my-check-bed:
  candy:
    base: cachyos.cachyos
    candy:
      - '@github.com/opencharly/pod-chrome-headless:<tag>'
```

Then drive it via the `cdp:` check verb against `http://127.0.0.1:9222`.

## Layout

- `charly.yml` — the `chrome-headless` candy entity (description, `require`,
  `candy`, service, plan; the launcher is authored inline in the plan).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning procedure: `/charly-check:cdp` — the declarative `cdp:` check verb.
- `/charly-selkies:chrome-cdp` — the CDP proxy layer this candy composes.
- `/charly-selkies:chrome` — the parent Chrome layer.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
