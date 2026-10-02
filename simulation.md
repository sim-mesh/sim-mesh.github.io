---
title: Simulation
permalink: /simulation/
redirect_from:
  - /networks/
description: The firmware, antennas, ground, nodes and scripts a simulation is made of, and how sim-mesh decides who hears whom.
---

A simulation is several things that change for different reasons, each its
own file so that changing one leaves the others alone: what the nodes run (a
**firmware**), what they transmit and hear with (an **antenna**), the ground
(**geodata**), the nodes on it (a **nodeset**), what they are told (a
**script**), and what follows from ground and nodes, the **loss table**. The
chapters below follow the app's tabs.

## Firmware

What a node runs: a Linux build of real mesh firmware with a driver beside
it, added from a zip or from the pre-built list on the **Firmware** tab. A
nodeset never says what its nodes run; a script does, so one nodeset runs on
any firmware, or on a mix of them by tag. [Firmware]({{ '/using-firmware/' | relative_url }})
has the names, the categories, and how to build firmware for sim-mesh.

## Antennas

A node carries one antenna from the catalogue — a bare quarter-wave wire, a
spring helical, a fibreglass collinear, a panel, a yagi — each a pattern of
five figures: peak gain, vertical beamwidth, tilt, horizontal beamwidth for
a directional one, and a floor. Between two nodes the direction is the line
between their antenna tips in three dimensions, the earth's curvature taken
off, so a low node under a high collinear's narrow beam hears less of it than
one on the horizon, and a yagi hears what it faces. The **Antennas** tab
draws every pattern.

## Geodata

The ground nodes stand on, one directory each under `testbed/geodata/`, of
one of two kinds.

**Synthetic ground** lies at 0°, 0°, its degrees metres by one fixed rule (a
nautical mile to the minute of arc), so a nodeset made on one synthetic
ground stands on any other. A pair's loss is log-distance,
`FSPL(1 m, f) + 10·n·log10(d)`, with `n` 2 for free space, 2.7 suburban.

**A pack** is ground compiled from public sources — terrain, clutter,
buildings, roads and places, in a UTM zone. A pair's loss on it is ITU-R
P.1812-8 over the real profile, and the map draws the pack's ground, roads
and buildings with the notices of its sources. **Build from sources…** on the
**Geodata** tab makes one over a rectangle of the map, taking the best source
for each part of it:

| | Where | From |
|---|---|---|
| terrain and clutter | Berlin | its DGM1 and bDOM (1 m) |
| | everywhere else | Copernicus GLO-30 |
| buildings | Berlin | its LoD2 models |
| | everywhere else | OpenStreetMap |
| population | Germany | the Zensus 2022 grid |
| land cover, roads, places | the world | ESA WorldCover, OpenStreetMap |

A geodata goes from one machine to another as a zip, **Export zip** and
**Import zip…**; a bare planner pack imports too. Nodes are never part of
the ground: they belong to nodesets.

## Nodes

Which nodes stand where — latitude, longitude, height above the ground — with
each one's maximum power at the antenna connector, its antenna, its tags, and
offsets: dB added to one pair's computed loss, where a measurement says the
model is wrong. A nodeset stands on any geodata whose extent holds one of its
nodes, as a layer of the **Nodes** tab, and several layers run as one.

Every node is one board, an SX1262: at 22 dBm or below a bare chip, above it
(up to 27 dBm) an SX1262 behind a GC1109 front end, as a Heltec V4 is. A node
is told its board when its station starts.

**Importing** makes a nodeset of the nodes inside the geodata's extent from
the MeshCore map, a PotatoMesh instance, planner sites or a deployed-network
CSV, each node tagged with its source, its kind and how good its position is.

## Scripts

A script is plain Python that says what each node runs and what is done to
it, run from the **Scripts** tab or with `sim run`. [Scripting]({{ '/simulation/scripting/' | relative_url }})
has the library, the commands and the scripts that come with sim-mesh.

## Simulations

The **Simulations** tab lists every simulation — running, paused or ended —
with its nodeset, geodata and script, its pace and time, and how many of its
stations are up; clicking a running one opens its live map, where the same
edits as on the Nodes tab go to the run's own copy of its nodeset.

### Who hears whom

There are no stated links. The level a frame arrives at is

```
L = P_tx + G_tx + G_rx − loss(tx → rx) − offset − 20·log10(f / f0)
```

the power it went out at, each antenna's gain toward the other end, the loss
table's figure for the pair and the nodeset's offset. A frame that arrives
below the signal-to-noise ratio its spreading factor needs — −7.5 dB at SF7,
down to −20 dB at SF12 — is not delivered at all; that is what "out of range"
means here. Every transmission overlapping a receiver's channel is
interference there, whatever its spreading factor, and each receiver rules
for itself, frame by frame, over every stretch of its air.

**The loss table** is every ordered pair's path loss for one nodeset on one
geodata in one band, derived and cached under a hash of exactly what it
depends on: the ground and the nodes' positions and heights. Antennas and
offsets are layers put on it when the medium is given it, so changing them
never recomputes it. Every pair is computed, not only those strong enough to
carry a frame, because a pair far too weak to decode still adds to a
receiver's interference.

### Time

A run goes in **real time**, on the wall clock, or in **virtual time**: the
ether owns time and moves it only when every station is idle, so an hour of
a busy network costs what the stations' work costs, and a busy host slows a
run down instead of changing what happens in it. With the same seed, two
runs of one network put the same frames on the air at the same instants.
