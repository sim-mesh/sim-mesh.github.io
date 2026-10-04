# sim-mesh.net

The site at **[sim-mesh.net](https://sim-mesh.net/)**: sim-mesh's pages, how to
build firmware for it, and the pre-built firmware. Its repository on GitHub is
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
| `using-firmware.md` | names, categories, adding and deleting, pre-built |
| `using-firmware/building.md` | building firmware for sim-mesh: sim-mesh's README section *The firmware contract*, its headings a level up and its links into the repository made absolute |
| `simulation.md` | a chapter per app tab, in the app's order: firmware, antennas, geodata, nodes, scripts, simulations (who hears whom, time) |
| `simulation/scripting.md` | the script library: every firmware's commands, then each category's |
| `examples/index.yaml` | sim-mesh's own index, `sim-mesh-examples`: the geodata packs and nodesets every sim-mesh lists, each by its sha256, the packs as release assets of sim-mesh/sim-mesh, and the text the page and `sim index list` print about them |

`_config.yml`'s `nav` is both the masthead and the sidebar; an entry's
`children` are sub-pages, listed under it in the sidebar. Pages that moved
keep their old URLs through `redirect_from`.

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
