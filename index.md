---
title: Home
hero: Real mesh firmware, many nodes, one simulated sky.
description: >-
  sim-mesh runs real LoRa mesh firmware, built as Linux processes, over a
  model of the radio chip and one medium that decides who hears what from the
  ground they stand on, in real time or in virtual time.
---

**sim-mesh** is a LoRa testbed. Its stations are real firmware, built for
Linux and run as ordinary processes, each on its own loopback address; below
their radio driver sits a model of the radio chip on a virtual SPI bus, and
between the models sits one medium, the **ether**, which decides who hears
what from a table of every pair's path loss over real or synthetic ground. A
map in the browser places the nodes and shows every frame on the air. It is
the firmware under test, built for a different target — not an emulator.

A run goes in **real time**, for working with stations by hand, or in
**virtual time**, as fast as the stations can compute: an hour of a busy
network in seconds, the same frames at the same instants in every run of one
seed.

<ul class="cards">
<li><a href="{{ '/getting-started/' | relative_url }}"><b>Get started</b><span>Clone, start, add firmware, run a script. Docker or Podman is all it needs.</span></a></li>
<li><a href="{{ '/firmware/' | relative_url }}"><b>Pre-built firmware</b><span>Station builds ready to add, for aarch64 and x86_64.</span></a></li>
<li><a href="{{ '/contract/' | relative_url }}"><b>Bring your firmware</b><span>The zip, its driver, the ether's protocol and the virtual radio, specified.</span></a></li>
</ul>

## What it is made of

```
browser ── localhost:8800 ──► front ─┬─► simd (one simulation) ─┬─ ether      who hears what
                                     │                          ├─ stations   firmware processes, one pty each
                                     │                          └─ proxy      each station's own web UI
                                     ├─► a script's runner, driving one simulation
                                     └─► the planner: real ground, built from public sources
```

One simulation is one `simd`; the **front** runs several behind one port,
computes their loss tables, runs scripts against them and builds real ground
from public data. Around them are the things sim-mesh keeps:

- **firmware** — station builds, each a zip with its own driver, installed
  from a file or from [the pre-built ones]({{ '/firmware/' | relative_url }});
- **geodata** — the ground, a pack built from public sources or synthetic;
- **nodesets** — which nodes stand where, with what antenna and power;
- **scripts** — plain Python that says what each node runs and what is done;
- **runs** and **snapshots** — what a simulation did, and moments of it to
  start again from.

sim-mesh holds nothing of any one firmware project. A firmware tells sim-mesh
how to talk to it through its **driver**, a Python module in its own zip, and
a script asks for the same things — an announce, a message, a path — of every
firmware of a category. Reticulum firmware is the first category; MeshCore
and Meshtastic are next.

Source: [github.com/sim-mesh/sim-mesh](https://github.com/sim-mesh/sim-mesh),
Apache 2.0.
