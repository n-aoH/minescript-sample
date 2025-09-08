---
title: Guide04 – Effects etc
parent: Beginners Guide
layout: post
nav_order: 4
render_with_liquid: false
---

# Beginners Guide 04: Titles, Effects, Sounds, and Particles in Minescript

This guide shows how to use **titles, effects, sounds, and particles** in Minecraft using Minescript.
We provide **utility functions** and **example usage** for each.

---

## ❶ Title & Subtitle

**Goal**

* Display a title and optional subtitle to all players
* Customize colors and delay
* Easy to reuse via function

**Function**

```python
import minescript as m
import time

def title_subtitle(title_text, subtitle_text=None, title_color="gold", subtitle_color="aqua", delay=1):
    """Display a title and optional subtitle to all players"""
    m.execute("title @a clear")
    m.execute(f'title @a title {{"text":"{title_text}","color":"{title_color}","bold":true}}')
    if subtitle_text and subtitle_text.strip():
        m.execute(f'title @a subtitle {{"text":"{subtitle_text}","color":"{subtitle_color}","bold":true}}')
    time.sleep(delay)
```

**Examples**

```python
title_subtitle("Welcome!", "Have fun!", "gold", "aqua")
time.sleep(2)
title_subtitle("Level Up!", "You reached level 5", "green", "yellow")
time.sleep(2)
title_subtitle("Warning!", "Danger ahead!", "red", "dark_red")
time.sleep(2)
title_subtitle("Quest Complete!", "Reward obtained", "gold", "white")
time.sleep(2)
title_subtitle("Game Over", "Try again?", "dark_purple", "gray")
time.sleep(2)
```

---

## ❷ Effects

**Goal**

* Apply status effects to players
* Control duration, level, and particle visibility

**Function**

```python
def give_effect(target="@s", effect="health_boost", duration=1000000, level=1, hide_particles=False):
    """Apply a status effect to the specified player"""
    hide = "true" if hide_particles else "false"
    m.execute(f"/effect give {target} {effect} {duration} {level} {hide}")
```

**Examples**

```python
give_effect(effect="health_boost", level=4, hide_particles=False)
time.sleep(2)
give_effect(effect="speed", level=3, hide_particles=True)
time.sleep(2)
give_effect(effect="strength", level=2, hide_particles=False)
time.sleep(2)
give_effect(effect="regeneration", level=2, hide_particles=True)
time.sleep(2)
give_effect(effect="resistance", level=1, hide_particles=False)
time.sleep(2)
```

---

## ❸ Sounds

**Goal**

* Play sounds at the player's location
* Control volume and pitch

**Function**

```python
def play_sound(sound="minecraft:entity.player.levelup", source="master", target="@s", volume=1, pitch=1):
    """Play a sound at the specified player's location"""
    m.execute(f"playsound {sound} {source} {target} ~ ~ ~ {volume} {pitch}")
```

**Examples**

```python
play_sound("minecraft:entity.player.levelup")
time.sleep(2)
play_sound("minecraft:block.note_block.bell")
time.sleep(2)
play_sound("minecraft:entity.experience_orb.pickup")
time.sleep(2)
play_sound("minecraft:entity.firework_rocket.launch")
time.sleep(2)
play_sound("minecraft:entity.arrow.hit_player")
time.sleep(2)
```

---

## ❹ Particles

**Goal**

* Spawn particles at a location
* Control position offsets, speed, and count

**Function**

```python
def spawn_particle(particle="minecraft:happy_villager", x="~", y="~", z="~", dx=0, dy=0, dz=0, speed=0, count=1):
    """Spawn particles at the specified coordinates"""
    m.execute(f"/particle {particle} {x} {y} {z} {dx} {dy} {dz} {speed} {count}")
```

**Examples**

```python
spawn_particle("minecraft:heart", dx=0.5, dy=1, dz=0.5, count=5)
time.sleep(2)
spawn_particle("minecraft:happy_villager", dx=0, dy=1, dz=0, speed=0.1, count=10)
time.sleep(2)
spawn_particle("minecraft:flame", dx=0, dy=1, dz=0, speed=0.1, count=8)
time.sleep(2)
spawn_particle("minecraft:crit", dx=0, dy=1, dz=0, speed=0.2, count=6)
time.sleep(2)
spawn_particle("minecraft:dragon_breath", dx=0, dy=1, dz=0, speed=0.05, count=4)
time.sleep(2)
```
