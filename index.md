---
title: RPi Audio Player
layout: minimal
nav_exclude: true
---

{: .highlight }
## **Project Description**

---
This project uses a **Raspberry Pi 4 Model B** (RPi for short), with the help of Python and circuits and **two handsome guys**, we created an **audio (.mp3) player**, embedded into our RPi.

{: .highlight }
## **Project History**

---
* We first got a Raspberry Pi named **Michael** from Mr. Andrade, our teacher. Unfortunately, it was not up to our standard, so we got another one (owned by Dylan, my partner), the same model but **younger and more handsome**, named **Michael 2.0**
* We then tried to install **Volumio OS** (An operating system specifically made for audio and music playing) into Michael 2.0, but that means we need **more monitors and Micro SD Cards**, which also means that it is going to be **super tedious and makes the project more expensive**, we do not want that
* We decided to embed a **Python script** into Michael 2.0 instead, which with the help of **buttons and wires**, we successfully created a Walkman, though in this case, **Sitman**, because no one wanted to walk around with the setup, We tried, learned it the hard way

{: .highlight }
## **Project Setup**

---
A list of what you would need to recreate this project:
1. **A handsome, functional Raspberry Pi**
2. **Headphone/Speaker with jack cable**
3. **Monitor**
4. **32 GB micro SD card**
5. **Keyboard and mouse**
6. **Breadboard, power supply, buttons, and jumper wires (♂ to ♀)**

{: .highlight }
## **Python Script**

---
```python
# This is the Python Script used for this project.
# We believe it could use some improvement, feel free to use it as a reference for your work. :)

import RPi.GPIO as GPIO
import subprocess
import time
import os

# Pin configuration
PLAY_PAUSE_BUTTON_PIN = 18
NEXT_TRACK_BUTTON_PIN = 23
music_folder = "<folder_path>"  # Path to folder with .mp3 files

# Playback state
is_playing = False
current_track_index = 0
tracks = []
vlc_process = None

# GPIO setup
GPIO.setmode(GPIO.BCM)
GPIO.setup(PLAY_PAUSE_BUTTON_PIN, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)
GPIO.setup(NEXT_TRACK_BUTTON_PIN, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)

def load_tracks():
    """Load all .mp3 files from the music folder."""
    global tracks
    tracks = sorted(
        [os.path.join(music_folder, f) for f in os.listdir(music_folder) if f.endswith(".mp3")]
    )
    if not tracks:
        print("No .mp3 files found in the music folder.")
        exit(1)

def start_vlc():
    """Start VLC in RC mode."""
    global vlc_process
    vlc_process = subprocess.Popen(
        ["cvlc", "--intf", "rc"],
        stdin=subprocess.PIPE,
        stdout=subprocess.DEVNULL,
        stderr=subprocess.DEVNULL,
        text=True
    )

def play_track(index):
    """Send command to VLC to play a specific track."""
    global is_playing
    track_path = tracks[index]
    print(f"Playing: {os.path.basename(track_path)}")
    vlc_process.stdin.write(f"add {track_path}\n")
    vlc_process.stdin.flush()
    is_playing = True

def toggle_play_pause(channel):
    """Toggle between play and pause."""
    global is_playing
    if is_playing:
        print("Pausing playback...")
        vlc_process.stdin.write("pause\n")
        vlc_process.stdin.flush()
        is_playing = False
    else:
        print("Resuming playback...")
        vlc_process.stdin.write("pause\n")  
        vlc_process.stdin.flush()
        is_playing = True

def next_track(channel):
    """Skip to the next track."""
    global current_track_index
    current_track_index = (current_track_index + 1) % len(tracks)
    print("Skipping to next track...")
    play_track(current_track_index)


load_tracks()

start_vlc()

# Start playing the first track
play_track(current_track_index)

# Detects button presses with debounce
GPIO.add_event_detect(PLAY_PAUSE_BUTTON_PIN, GPIO.RISING, callback= toggle_play_pause, bouncetime=100)
GPIO.add_event_detect(NEXT_TRACK_BUTTON_PIN, GPIO.RISING, callback= next_track, bouncetime=100)

try:
    print("Press the play/pause button to toggle playback.")
    print("Press the next track button to skip to the next track.")
    while True:
        time.sleep(0.1)  # Keeps the script running
except KeyboardInterrupt:
    print("Exiting...")
finally:
    if vlc_process:
        vlc_process.terminate()
    GPIO.cleanup()

```

[My Github](https://github.com/RonPhan22?tab=repositories){: .btn }
[Dylan Github](https://github.com/DylanT168){: .btn }
