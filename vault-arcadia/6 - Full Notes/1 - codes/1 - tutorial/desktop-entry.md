## GNOME 
## GNOME 
- Desktop entry Postman
  http://gist.github.com/hauthorn/d1da427b16133776bd8c65db802bc6ad

## User
What is desktop entry, and how to create one ?

## Assistant
A **desktop entry** is simply a text file (with extension `.desktop`) that tells your Linux desktop environment how to launch and display an application in menus, panels or launchers. It follows the [Desktop Entry Specification][1].

---

## Key points

- **Location**  
  - **System-wide**: `/usr/share/applications/`  
  - **Per-user**: `~/.local/share/applications/`  

- **Format**  
  INI-style. Must start with:
  ```
  [Desktop Entry]
  Type=Application       # (or Link, Directory)
  Name=My App            # Visible name
  Exec=/full/path/to/app # Command to run; can include %U, %f, etc.
  Icon=/path/to/icon.png # Or just “myapp” if in your icon theme
  Terminal=false         # true if it needs a terminal
  Categories=Utility;    # Helps menu organization
  ```

```
  [Desktop Entry]
  Type=Application       
  Name=My App            
  Exec=/full/path/to/app 
  Icon=/path/to/icon.png
  Terminal=false        
  Categories=Utility;
```

  You can also add:
  - `Comment=` – tooltip text  
  - `StartupNotify=` – enable launcher feedback  
  - `StartupWMClass=` – match window for proper focus  

---

## How to create one

1. **Create the file**  
   ```bash
   mkdir -p ~/.local/share/applications
   nvim ~/.local/share/applications/myapp.desktop
   ```
2. **Populate it** with at least the required fields:
   ```
   [Desktop Entry]
   Version=1.0
   Type=Application
   Name=My Cool App
   Exec=/home/you/bin/mycoolapp %U
   Icon=/home/you/icons/mycoolapp.png
   Terminal=false
   Categories=Utility;Development;
   ```
3. **Make it executable**  
   ```bash
   chmod +x ~/.local/share/applications/myapp.desktop
   ```
4. **Refresh** (most DEs auto-detect; you can log out/in or run):  
   ```bash
   update-desktop-database ~/.local/share/applications
   ```
5. **Launch**  
   Find “My Cool App” in your application menu or via your launcher.

## User
I want to export content of this chat box to a .md, can you do it for me ?

---
# WITH StartupWMClass
```
[Desktop Entry]
Version=1.0
Name=Brave Browser
Comment=Browse the Web
Exec=/home/vessel/opt/brave-browser-nightly-1.81.7-linux-amd64/brave %U
Terminal=false
Type=Application
Icon=/home/vessel/opt/brave-browser-nightly-1.81.7-linux-amd64/product_logo_128_nightly.png
Categories=Network;WebBrowser;
StartupNotify=true
StartupWMClass=brave-browser
MimeType=text/html;text/xml;application/xhtml+xml;application/xml;application/rss+xml;application/rdf+xml;
```