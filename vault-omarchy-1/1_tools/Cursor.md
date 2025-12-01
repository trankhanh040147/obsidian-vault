# Using https://github.com/yeongpin/cursor-free-vip

**Run --appimage-extract**
```
./Cursor___.AppImage --appimage-extract
mv squash-root /opt/Cursor

```

**Create symlink `resources`**
```bash
sudo ln -s /opt/Cursor/usr/share/cursor/resources /opt/Cursor/resources
```