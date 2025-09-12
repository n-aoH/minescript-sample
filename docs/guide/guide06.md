---
title: Guide06 – Damage Event
parent: Beginners Guide
layout: post
nav_order: 6
render_with_liquid: false
---

# Beginners Guide 06: Detecting Player Attacks on Mobs

This guide explains how to detect when **the local player attacks mobs** in Minecraft using Minescript.
We’ll build this step by step: start with a default debug, then filter skeletons, handle melee/projectiles, and finally track remaining health and kills.

---

## ❶ Default Damage Debug (All Events)

This debug version shows **all DAMAGE events** for inspection.
Good for getting a general overview before filtering or processing specific mobs.

```python
import minescript as m
from minescript import EventQueue, EventType

eq = EventQueue()
eq.register_damage_listener()

m.echo("⚡ Default DamageEvent debug mode: waiting for damage...")

while True:
    event = eq.get()
    if not event or event.type != EventType.DAMAGE:
        continue

    # Inspect all DAMAGE events
    m.echo(f"--- DAMAGE EVENT ---")
    m.echo(f"entity_uuid = {event.entity_uuid}")
    m.echo(f"cause_uuid  = {event.cause_uuid}")
    m.echo(f"source      = {event.source}")
    m.echo(f"full event  = {event.__dict__}")
```

---

## ❷ Skeleton Damage Debug

A simplified version tracking only **skeletons**:

```python
import minescript as m
from minescript import EventQueue, EventType

eq = EventQueue()
eq.register_damage_listener()

m.echo("⚡ Skeleton DamageEvent debug mode: waiting for skeleton hits...")

while True:
    event = eq.get()
    if not event or event.type != EventType.DAMAGE:
        continue

    # Get the target entity
    targets = m.entities(uuid=event.entity_uuid)
    if not targets:
        continue

    target = targets[0]
    
    # Skip if not a skeleton
    if target.type != "minecraft:skeleton":
        continue

    # Inspect skeleton-specific events
    m.echo(f"--- DAMAGE EVENT (Skeleton) ---")
    m.echo(f"entity_uuid = {event.entity_uuid}")
    m.echo(f"cause_uuid  = {event.cause_uuid}")
    m.echo(f"source      = {event.source}")
    m.echo(f"full event  = {event.__dict__}")
```

---

## ❸ Melee Attacks Only

Detect **melee attacks** from the local player:

```python
import minescript as m
from minescript import EventQueue, EventType, player, entities

local_player = player()
local_uuid = local_player.uuid
m.echo(f"🗡 Melee damage listener active for {local_player.name}")

eq = EventQueue()
eq.register_damage_listener()

while True:
    event = eq.get()
    if not event or event.type != EventType.DAMAGE:
        continue

    # Only melee attacks
    if event.source != "player":
        continue

    if event.cause_uuid != local_uuid:
        continue

    mobs = entities(uuid=event.entity_uuid)
    if not mobs:
        continue

    mob = mobs[0]
    m.echo(f"🗡 You hit {mob.type} with a melee attack")
```

---

## ❹ Add Projectiles

Handle **both melee and ranged attacks**:

```python
import minescript as m
from minescript import EventQueue, EventType, player, entities

local_player = player()
local_uuid = local_player.uuid
m.echo(f"🏹 Damage listener active for {local_player.name} (melee + projectiles)")

eq = EventQueue()
eq.register_damage_listener()

while True:
    event = eq.get()
    if not event or event.type != EventType.DAMAGE:
        continue

    # Handle melee + projectiles
    if event.source not in ["player", "arrow", "trident", "snowball", "fireball"]:
        continue

    if event.cause_uuid != local_uuid:
        continue

    mobs = entities(uuid=event.entity_uuid)
    if not mobs:
        continue

    mob = mobs[0]
    m.echo(f"🎯 You damaged {mob.type} with {event.source}")
```

---

## ❺ Remaining HP & Kill Detection

Finally, track **remaining health** and detect **kills**:

```python
import minescript as m
from minescript import EventQueue, EventType, player, entities

local_player = player()
local_uuid = local_player.uuid
m.echo(f"⚔️ Damage listener active for {local_player.name} (HP & kill tracking)")

eq = EventQueue()
eq.register_damage_listener()

while True:
    event = eq.get()
    if not event or event.type != EventType.DAMAGE:
        continue

    if event.source not in ["player", "arrow", "trident", "snowball", "fireball"]:
        continue

    if event.cause_uuid != local_uuid:
        continue

    mobs = entities(uuid=event.entity_uuid)
    if not mobs:
        continue

    mob = mobs[0]
    hp = getattr(mob, "health", None)

    if hp is not None:
        if hp > 0:
            m.echo(f"⚔️ You hit {mob.type} (HP left: {hp})")
        else:
            m.echo(f"💀 You killed {mob.type}!")
    else:
        m.echo(f"⚔️ You hit {mob.type}")
```