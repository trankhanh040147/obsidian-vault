1. create a **Custom Shortcut** in **Settings** > **Keyboards** > **Custom Shortcuts**
2. set command 
```
wmctrl -lx | grep mongodb.NoSQLBooster && wmctrl -xa mongodb.NoSQLBooster || /home/vessel/opt/nosqlbooster
```

```
wmctrl -lx | grep Signet.Signet && wmctrl -xa Signet.Signet || /home/vessel/Signet/Signet
```

```
wmctrl -lx | grep jetbrains-goland.jetbrains-goland && wmctrl -xa jetbrains-goland.jetbrains-goland || /home/vessel/opt/GoLand-2025.1.2/bin/goland
```