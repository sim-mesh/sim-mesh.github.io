---
title: Firmware
description: How sim-mesh names, installs, chooses and deletes the firmware its stations run.
---

A station runs a **firmware**: an executable built for Linux, whatever it
needs beside it, and a Python module, its **driver**, that tells sim-mesh how
to talk to it. A firmware arrives as a zip and is installed under
`firmware/`, unpacked into a directory of the zip's own name. sim-mesh holds
nothing of any one firmware project: what it knows of one is its driver.
[The firmware contract]({{ '/contract/' | relative_url }}) specifies all of it.

## Names

A firmware is called `<base>_<arch>_<version>`, and so is its zip, `.zip`
after it:

- **base** — lower-case letters, digits, `-` and `.`;
- **arch** — the architecture it runs on, as `uname -m` spells it
  (`aarch64`, `x86_64`);
- **version** — a build stamp, `YYYYMMDDhhmmss` in UTC, or a semantic version
  `1.2.3`; a base keeps to one of the two.

Two builds that differ in anything sim-mesh does not read — the radio they
drive among it — are two bases, and by custom the base names the radio:
`relay-sx1262`. **`<base>_latest`** is the newest installed firmware of
exactly that base for this machine.

## Categories

A firmware's **category** says what kind of mesh its stations make, and which
verbs its driver answers: `reticulum` (the one there is); `meshcore` and
`meshtastic` come next. A script means the same thing on every firmware of a
category — an announce, a message, a path — and each driver does it its own
way. The `reticulum` verbs:

| Verb | Means | Returns |
|---|---|---|
| `name(name)` | the node's name | |
| `role(role)` | `transport` or `client` | |
| `radio(…)` | slot 0's LoRa settings | |
| `radio_up()` | the radio started | |
| `tx_power(dbm)` | transmit power at the connector | |
| `path(dest, iface)` | | its path table, `[{dest, next_hop, iface, hops}]`, what of it is asked for |
| `peer_tcp(addr, port)` | a TCP link to another station | |
| `current_role()` | | what it does now |
| `diagnostics()` | | what a traffic run keeps of it |
| `lxmf.create(name)` | one more LXMF identity, unless the node has one by that name | its address |
| `lxmf.identities()` | | `[(name, address)]`, the one it sends from first |
| `lxmf.announce(name)` | an announce of that identity's destination | |
| `lxmf.send(dest, text, mid, sender)` | an LXMF message, `mid` sim-mesh's id for it | |

What became of each message the driver reports as `lxmf.message.status`
under sim-mesh's id: `pending`, `sent`, `delivered` or `failed`, with why.
A script reaches a category's verbs through a selection,
`nodes(…).reticulum.…`, and names a message's recipient by node or by LXMF
identity: `node("a20").reticulum.lxmf.send("bob", "hi")`.

## Adding, listing, deleting

On the **Firmware** tab: **Add from zip…**, **Add from pre-built…**, and a
trash can per firmware. From a shell:

```sh
sim firmware add relay-sx1262_aarch64_20260930163128.zip
sim firmware add https://sim-mesh.net/firmware/<name>.zip
sim firmware prebuilt
sim firmware list [SUBSTRING]
sim firmware delete [-f] SUBSTRING        # lists what it would delete, then asks
```

A zip is refused when it is built for another architecture, when its base
already holds firmware versioned the other way, when a firmware of its name
is installed already, and when its `node.yaml` or anything it names is
missing. **Deleting is refused for a firmware a paused run or a snapshot
holds**: their state can only be resumed on the firmware that wrote it, so
the run or snapshot goes first.

## Choosing it in a script

A script asks for its firmware as an input, and the Scripts tab shows a
dropdown for it above the script, offering the firmware of the category the
script needs:

```python
firmware = script_input("firmware", type=Firmware, category="reticulum",
                        label="Firmware for nodes not otherwise configured")
nodes().firmware(firmware)
```

From a shell, `sim run lxmf-traffic … --set firmware=relay-sx1262_latest`.
A run keeps what each name resolved to.

## Pre-built firmware

[The pre-built firmware]({{ '/firmware/' | relative_url }}) is the `firmware`
release of sim-mesh's repository, which this site serves; its listing carries
each zip's facts, so the Firmware tab lists them without fetching any. A
project publishes its zips there with sim-mesh's `tools/deploy-firmware`.
