---
title: Firmware
permalink: /using-firmware/
description: How sim-mesh names, installs and deletes the firmware its stations run.
---

A station runs a **firmware**: an executable built for Linux, whatever it
needs beside it, and a Python module, its **driver**, that tells sim-mesh how
to talk to it. A firmware arrives as a zip and is installed under
`firmware/`, unpacked into a directory of the zip's own name. sim-mesh holds
nothing of any one firmware project: what it knows of one is its driver.
[Building firmware for sim-mesh]({{ '/using-firmware/building/' | relative_url }})
says how to make one.

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

A firmware's **category** says what kind of mesh its stations make, and so
which commands its driver answers: `reticulum` or `meshcore`;
`meshtastic` comes next. A script means the same thing on every
firmware of a category — an announce, a message, a path — and each driver
does it its own way. The categories' commands are not alike: MeshCore's
carry meshcore-cli's names and meanings, and comparing across protocols
is a layer above both. [Scripting]({{ '/simulation/scripting/' | relative_url }})
lists the commands, every firmware's and each category's.

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

## Pre-built firmware

[The pre-built firmware]({{ '/firmware/' | relative_url }}) is the `firmware`
release of sim-mesh's repository, which this site serves; its listing carries
each zip's facts, so the Firmware tab lists them without fetching any. A
project publishes its zips there with `sim firmware publish`.
