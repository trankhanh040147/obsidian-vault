# 1. The 2.5-Minute Problem: `apt-daily-upgrade.service`

- **What it is:** This service checks for and installs system updates (like `apt upgrade`) _during the boot process_. This is a terrible time to do this, as it's I/O and network-heavy, blocking you from getting to your desktop.
    
- **The Fix:** You should absolutely **disable this**. Your system has _timers_ (cron jobs) that will run this in the background _after_ you're already logged in, which is the correct behavior.
    ```
    sudo systemctl disable apt-daily-upgrade.service
    ```
    
    (You might as well disable the non-upgrade one too, just in case):
    
    ```
    sudo systemctl disable apt-daily.service
    ```
    

# 2. The 35-Second Problem: `NetworkManager-wait-online.service`

- **What it is:** This service intentionally pauses the boot process until it confirms a "full" network connection is established. This is really only necessary for servers that need to mount network drives at boot. For a desktop, it's completely pointless and just adds 35 seconds of waiting.
    
- **The Fix:** **Disable it.** Your regular network connection will still start up just as fast; this just stops the _boot process from waiting for it_.
    
- **Command:**
    
    Bash
    
    ```
    sudo systemctl disable NetworkManager-wait-online.service
    ```
    

#### 3. The 14-Second Problem: McAfee Services

- **What they are:**
    
    - `mcafee.ma.service` (10.6s)
        
    - `mfeespd.service` (2.5s)
        
    - `mfetpd.service` (1.2s)
        
- **What it is:** This is your antivirus. Together, these are adding about **14 seconds** to your boot.
    
- **The Fix (My Strong Recommendation):** This is a critical point. Is this a work laptop where this is _required_?
    
    - **If this is a personal machine:** I would **strongly** recommend you uninstall McAfee. For a personal Linux desktop, it's almost universally considered unnecessary, drains resources (as you can clearly see!), and doesn't offer much practical protection that good browsing habits and the built-in `ufw` firewall don't already cover. Removing it will be a _massive_ performance win, not just at boot.
        
    - **If you are forced to keep it:** You can't do much. But if you can remove it, do it.
        

#### 4. The 8-Second Problem: `docker.service`

- **What it is:** The Docker daemon.
    
- **The Fix:** This is actually a _symptom_. It's very likely waiting for `NetworkManager-wait-online.service` to finish. **Disabling the NetworkManager service will probably make this one start much faster.**
    
- **Bonus Tip:** Do you need Docker _every single time_ you boot? If not, you can also disable it and just start it manually when you need it.
    
- **Optional Command:**
    
    Bash
    
    ```
    sudo systemctl disable docker.service
    ```
    
    (You can still run Docker; you'd just have to run `sudo systemctl start docker.service` when you want to use it).

---
# Swapiness
That's a great follow-up. This one setting can make a big difference.

It's a setting that tells your system **how aggressively to use your swap file.**

---

### What "Swappiness = 60" Means

Think of it as a "panic" setting on a scale of 0 to 100.

- **Fast RAM:** Your physical RAM sticks. It's super-fast.
    
- **Slow "Swap":** A file on your hard drive/SSD that Linux uses as "backup RAM" when you run out of fast RAM. It's _thousands_ of times slower.
    

Your value of **`60`** tells the operating system:

> "Be pretty aggressive. When my fast RAM starts getting (roughly) 40% full, I want you to start 'swapping' data I'm not _actively_ using out of fast RAM and moving it to the slow swap file. I want to keep lots of fast RAM free."

---

### How It Affects Your System

This "aggressive" setting (60) is the default, but it's bad for most modern desktops.

**The main effect is system "stutter" or "lag."**

Here's the scenario that happens all the time with `swappiness=60`:

1. You are using Chrome, Spotify, and a code editor.
    
2. You don't touch Spotify for 20 minutes.
    
3. Your system sees Spotify is "inactive" and, because of the `60` setting, "swaps" it from fast RAM to your slow disk. This frees up RAM, which the system _thinks_ is a good thing.
    
4. You then click the Spotify icon to change the song.
    
5. Your _entire computer_ freezes for a second or two. This is "lag."
    
6. **Why?** Your system had to frantically pull all of Spotify's data _back_ from the slow disk and into fast RAM before it could respond.
    

For a modern desktop with 8GB or more of RAM, you don't want this. You want to use your fast RAM as much as possible!

---

### The Recommended Fix

As I mentioned in the last message, you almost certainly want to change this.

A value of **`10`** is much better for a desktop. This tells the system:

> "Be very lazy. Do NOT use the slow swap file unless you absolutely _have_ to. Only use it as a last resort when my fast RAM is almost 90% full."

This will make your system prioritize keeping applications in fast RAM, which makes your whole desktop feel snappier and eliminates those random "stutters."

To change it permanently, you'd run:

Bash

```
# Creates a new config file for this one rule
echo "vm.swappiness=10" | sudo tee /etc/sysctl.d/99-swappiness.conf
```

After you do that, you just need to **reboot** for it to take effect.