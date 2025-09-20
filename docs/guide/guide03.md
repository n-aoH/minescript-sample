---
title: Guide03 – Countdown
parent: Beginners Guide
layout: post
nav_order: 3
---

# Beginners Guide 03: Countdown with Bossbar, Actionbar, and Scoreboard in Minescript

This guide shows how to create a **countdown timer** in Minecraft using Minescript.
We will cover three display methods: **Bossbar**, **Actionbar**, and **Scoreboard**.
Each section explains how to **create**, **update**, and **remove** the display.

---

## 🎥 YouTube

<iframe width="560" height="315" src="https://www.youtube.com/embed/IFV92ahrXWQ?si=gJK5q6kvVhJ8ZXe4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

---

## ❶ Actionbar Countdown

**Goal**

* Display a countdown message at the bottom center of the screen
* Update the timer every second
* Clear the message when finished

**Notes**

* `title @a actionbar ["text"]` displays to all players
* Clear the actionbar using empty quotes

**Example**

```python
import minescript as m
import time

m.echo("countdown start")

num = 10

# Countdown loop
for t in range(num, 0, -1):
    m.execute(f'title @a actionbar ["Countdown: {t}"]')
    time.sleep(1)

# Clear the actionbar message
m.execute('title @a actionbar [""]')
m.echo("countdown stop")
```

### MM\:SS Format

* Format: f"{minutes:02d}:{seconds:02d}"

```python
for t in range(num, 0, -1):
    minutes = t // 60
    seconds = t % 60
    time_text = f"{minutes:02d}:{seconds:02d}"
    
    m.execute(f'title @a actionbar ["{time_text}"]')
    time.sleep(1)
```

---

## ❷ Scoreboard Countdown

**Goal**

* Display a countdown timer in a scoreboard
* Update the timer every second
* Remove the scoreboard when finished

**Notes**

* Use `scoreboard objectives add <id> dummy "<display_name>"` to create
* Update using `scoreboard players set`
* Remove the scoreboard using `scoreboard objectives remove <id>`

**Simple Countdown Example**

```python
import minescript as m
import time

m.echo("Countdown start")

num = 10

# Create a scoreboard with display name "Timer" (shown at top of sidebar)
m.execute('scoreboard objectives add timer dummy "Timer"')

# Use a dummy player name for display numbers, e.g., "Countdown"
m.execute(f'scoreboard players set Countdown timer {num}')

# Display the scoreboard on the sidebar
m.execute('scoreboard objectives setdisplay sidebar timer')

# Countdown loop
for t in range(num, 0, -1):
    m.execute(f'scoreboard players set Countdown timer {t}')
    time.sleep(1)

# Hide and remove the scoreboard
m.execute('scoreboard objectives setdisplay sidebar')  # Clear display
m.execute('scoreboard objectives remove timer')

m.echo("Countdown stop")
```

### MM\:SS Format Example

```python
import minescript as m
import time

m.echo("Countdown start")

total_seconds = 10
objective_name = "timer"
display_title = "Countdown"
dummy_line = "Time"  # Dummy line for updating

# Create scoreboard with title "Countdown"
m.execute(f'scoreboard objectives add {objective_name} dummy "{display_title}"')

# Display on sidebar
m.execute(f'scoreboard objectives setdisplay sidebar {objective_name}')

prev_time_str = None

# Countdown loop
for t in range(total_seconds, 0, -1):
    minutes = t // 60
    seconds = t % 60
    time_str = f"{minutes:02d}:{seconds:02d}"  # 00:10, 00:09 ...

    # Set the current time line
    m.execute(f'scoreboard players set "{time_str}" {objective_name} 15')

    # Remove the previous line so only one line is visible
    if prev_time_str:
        m.execute(f'scoreboard players reset "{prev_time_str}" {objective_name}')

    prev_time_str = time_str
    time.sleep(1)

# Remove scoreboard after countdown
m.execute(f'scoreboard objectives setdisplay sidebar')
m.execute(f'scoreboard objectives remove {objective_name}')

m.echo("Countdown stop")
```

---

## ❸ Bossbar Countdown

**Goal**

* Display a countdown timer in the bossbar
* Update the timer every second
* Remove the bossbar when finished

**Notes**

* Use `bossbar set <id> value <num>` to update progress
* Always remove the bossbar when finished using `bossbar remove <id>`

**Simple Countdown Example**

```python
import minescript as m
import time

m.echo("Countdown start")

num = 10

# Create a bossbar
m.execute('bossbar add timer "Countdown"')
m.execute('bossbar set timer color blue')
m.execute(f'bossbar set timer max {num}')   # Maximum value (seconds)
m.execute(f'bossbar set timer value {num}') # Current value
m.execute('bossbar set timer players @a')  # Display to all players

# Countdown loop
for t in range(num, 0, -1):
    # Update value
    m.execute(f'bossbar set timer value {t}')
    
    # Update title to include countdown number
    m.execute(f'bossbar set timer name "Countdown {t}"')
    
    time.sleep(1)

# Remove bossbar
m.execute('bossbar remove timer')
m.echo("Countdown stop")
```

### MM\:SS Format Example

```python
for t in range(num, 0, -1):
    minutes = t // 60
    seconds = t % 60
    time_text = f"{minutes:02d}:{seconds:02d}"

    # Update bossbar title with text
    m.execute(f'bossbar set timer name "{time_text}"')
    # Update progress value
    m.execute(f'bossbar set timer value {t}')
    time.sleep(1)
```
