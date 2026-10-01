# sim-mesh.net

The site at **[sim-mesh.net](https://sim-mesh.net/)**: sim-mesh's pages, the
firmware contract, and the pre-built firmware. Its repository on GitHub is
sim-mesh/sim-mesh.github.io, as GitHub Pages names an organisation's site.

```
this repo (Jekyll pages) ──┐
                           ├──► .github/workflows/pages.yml ──► GitHub Pages ──► sim-mesh.net
sim-mesh/sim-mesh, release `firmware` ──► /firmware/
```

| Page | What |
|---|---|
| `index.md` | what sim-mesh is |
| `getting-started.md` | clone, start, add firmware, a first simulation |
| `using-firmware.md` | names, categories, adding and deleting, choosing in a script |
| `networks.md` | geodata, nodesets, antennas, who hears whom, time |
| `scripts.md` | the script library |
| `contract.html` | the firmware contract: the zip, node.yaml, the environment, time, the driver, the `reticulum` verbs, the ether's protocol, the virtual radio |

`/firmware/` is not in this repo: the deploy copies the `firmware` release of
sim-mesh/sim-mesh there — every pre-built zip, `firmware.yaml` and the
`index.html` made from it — since a browser cannot fetch a release asset
across origins. sim-mesh's `tools/deploy-firmware` uploads to that release and
fires this repo's `firmware-published` dispatch, so new firmware is on the
site without a commit here.

## Previewing

```sh
bundle install
bundle exec jekyll serve      # http://localhost:4000/
```

## The domain

The site is `sim-mesh.net`: the domain's A and AAAA records are GitHub's
published Pages addresses, and **Settings → Pages → Custom domain** on this
repo holds `sim-mesh.net`, with **Enforce HTTPS** once GitHub has issued the
certificate. A site published by a workflow names its domain there, not in a
`CNAME` file, so this repo has none.
