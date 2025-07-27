**Obsidian**
sh -c "wmctrl -x -a Obsidian || /usr/bin/flatpak run --branch=stable --arch=x86_64 --command=obsidian.sh --file-forwarding md.obsidian.Obsidian @@u %U @@"

**Terminal**
sh -c "wmctrl -x -a Terminal || gnome-terminal"

**Brave**
sh -c "wmctrl -x -a Brave || brave-browser"

Goland
sh -c "wmctrl -x -a Goland || "/opt/GoLand-2024.2/bin/goland" %f"

Timer
sh -c "wmctrl -x -a Timer || "/usr/bin/flatpak run --branch=stable --arch=x86_64 --command=timer com.github.vikdevelop.timer" %f"