---
title: Guide05 – Chat Event
parent: Beginners Guide
layout: post
nav_order: 5
render_with_liquid: false
---

# Beginners Guide 05: Chat Commands and Countdown Timer in Minescript

This guide explains how to use **chat commands** in Minecraft with Minescript.
We will first build a simple **chat command demo** (fun commands like spawning sheep or setting time),
then expand into a **countdown timer with bossbar**, controlled fully through chat.

---

## ❶ Goal

* Learn how to **listen for chat messages** with `EventQueue`.
* Create custom commands (`--help`, `--noon`, etc.).
* Build a simple **utility commands script** (time set, summon sheep, give items).
* Create a **countdown timer** using bossbar, controlled by chat commands.

---

## ❷ Example: Listen to Chat

```python
import minescript as m
from minescript import EventQueue, EventType

eq = EventQueue()
eq.register_chat_listener()
m.echo("Chat listener ready!")

while True:
    event = eq.get()
    if not event or event.type != EventType.CHAT:
        continue

    msg = event.message
    # Remove player name prefix
    if msg.startswith("<") and ">" in msg:
        msg = msg.split(">", 1)[1].strip()

    # Skip if condition not met
    if not msg.startswith("--"):
        continue

    # Process only messages starting with --
    m.echo(f"Processing command-style chat: {msg}")
```

---

## ❸ Example: Simple Chat Commands

Here's a **fun demo** with custom commands:

```python
import minescript as m
from minescript import EventQueue, EventType

def main():
    eq = EventQueue()
    eq.register_chat_listener()

    m.echo("Chat command demo ready! Type --help for commands.")

    while True:
        event = eq.get()
        if not event or event.type != EventType.CHAT:
            continue

        msg = event.message.strip()

        if msg.startswith("<") and ">" in msg:
            msg = msg.split(">", 1)[1].strip()

        # Remove <Player> prefix
        if not msg.startswith("--"):
            continue

        if msg == "--help":
            m.echo("""
Available commands (type in chat):
--noon       : Set time to noon
--pinksheep  : Summon a pink sheep
--cheat      : Give OP items
""")

        elif msg == "--noon":
            m.execute("time set noon")
            m.echo("☀️ Time set to noon!")

        elif msg == "--pinksheep":
            m.execute("summon minecraft:sheep ^3 ^ ^ {Color:6}")
            m.echo("🐑 A pink sheep appeared!")

        elif msg == "--cheat":
            items = [
                "minecraft:netherite_sword",
                "minecraft:netherite_helmet",
                "minecraft:netherite_chestplate",
                "minecraft:netherite_leggings",
                "minecraft:netherite_boots",
                "minecraft:enchanted_golden_apple"
            ]
            for item in items:
                m.execute(f"give @p {item} 1")
            m.echo("🎁 OP items granted!")

if __name__ == "__main__":
    main()
```

---

## ❹ Countdown Timer with Chat Control

Next, let’s build a **countdown timer** displayed with a bossbar.
Players can control it via chat commands.

### Features

* `--settime <seconds>` : configure timer length
* `--start` : start the countdown
* `--stop` : stop and remove the bossbar
* `--help` : show available commands

### Script

```python
import minescript as m
import threading
import time
from minescript import EventQueue, EventType

# ----------------------
# Timer state
# ----------------------
timer_length = 30
timer_value = timer_length
timer_running = False
timer_thread = None  # Countdown thread

# ----------------------
# Bossbar / timer functions
# ----------------------
def create_bossbar():
    m.execute('bossbar add timer "Countdown"')
    m.execute('bossbar set timer color blue')
    m.execute(f'bossbar set timer max {timer_length}')
    m.execute(f'bossbar set timer value {timer_value}')
    m.execute('bossbar set timer players @a')

def remove_bossbar():
    m.execute('bossbar remove timer')

def update_bossbar():
    minutes = timer_value // 60
    seconds = timer_value % 60
    time_text = f"{minutes:02d}:{seconds:02d}"
    m.execute(f'bossbar set timer name "{time_text}"')
    m.execute(f'bossbar set timer value {timer_value}')

def countdown():
    global timer_value, timer_running
    while timer_running and timer_value > 0:
        update_bossbar()
        time.sleep(1)
        timer_value -= 1
    if timer_running:
        m.echo("⏰ Timer finished!")
        m.execute('title @a title {"text":"⏰ Time\'s up!","color":"red"}')
    remove_bossbar()
    timer_running = False

# ----------------------
# Chat commands
# ----------------------
def show_help():
    m.echo("""
Timer Commands:
--settime <seconds> : Set countdown time
--start             : Start countdown
--stop              : Stop countdown
--help              : Show this message
""")

# ----------------------
# Main loop
# ----------------------
def main():
    global timer_length, timer_value, timer_running, timer_thread

    eq = EventQueue()
    eq.register_chat_listener()
    m.echo("Countdown timer ready! Type --help for commands.")

    while True:
        event = eq.get()
        if not event or event.type != EventType.CHAT:
            continue

        msg = event.message.strip()

        # Remove <Player> prefix
        if msg.startswith("<") and ">" in msg:
            msg = msg.split(">", 1)[1].strip()

        if not msg.startswith("--"):
            continue

        if msg.startswith("--settime"):
            try:
                seconds = int(msg.split()[1])
                timer_length = max(1, seconds)
                timer_value = timer_length
                m.echo(f"Timer set to {timer_length} seconds.")
            except:
                m.echo("Invalid usage. Example: --settime 60")

        elif msg == "--start":
            if timer_running:
                m.echo("Timer is already running.")
            else:
                timer_running = True
                timer_value = timer_length
                create_bossbar()
                timer_thread = threading.Thread(target=countdown, daemon=True)
                timer_thread.start()
                m.echo("▶️ Timer started!")

        elif msg == "--stop":
            if timer_running:
                timer_running = False
                m.echo("⏹ Timer stopped manually.")
            remove_bossbar()

        elif msg == "--help":
            show_help()

if __name__ == "__main__":
    main()
```

