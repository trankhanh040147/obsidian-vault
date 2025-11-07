shortcuts GNOME
	**list shortcuts**
		- **all**
		- **switch-to-application:**
```
gsettings list-recursively | grep switch-to-application | sort
```
**set shortcut**
```
gsettings set org.gnome.shell.keybindings switch-to-application-8 "['<Super><shift>q']"
```

```
	org.gnome.shell.keybindings switch-to-application-1 ['<Super>1']
	org.gnome.shell.keybindings switch-to-application-2 ['<Super>2']
	org.gnome.shell.keybindings switch-to-application-3 ['<Super>3']
	org.gnome.shell.keybindings switch-to-application-4 ['<Super>4']
	org.gnome.shell.keybindings switch-to-application-5 ['<Super><shift>w']
	org.gnome.shell.keybindings switch-to-application-6 ['<Super><shift>s']
	org.gnome.shell.keybindings switch-to-application-7 ['<Super><shift>r']
	org.gnome.shell.keybindings switch-to-application-8 ['<Super><shift>q']
	org.gnome.shell.keybindings switch-to-application-9 ['<Super><shift>d']
```
	

