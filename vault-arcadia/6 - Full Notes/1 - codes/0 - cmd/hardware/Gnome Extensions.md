**SRC:**
[The Best Gnome Extensions ...](https://www.youtube.com/@MichaelNROH)

---
### Step 1: Install the Essential Tools

You need two key applications. Open your terminal and run this:

Bash

```
sudo apt update
sudo apt install gnome-tweaks gnome-shell-extension-manager
```

- **`gnome-tweaks` (The "Tweaks" app):** This lets you change settings that are hidden in the main Settings app, like themes, fonts, and window buttons.
    
- **`gnome-shell-extension-manager` (The "Extension Manager" app):** This is the best way to browse, install, and manage all your extensions.1
    

---

### Step 2: Add Functionality with Extensions

Open the new **`Extension Manager`** app. This is like a mini "app store" for your desktop's features. Here are the "must-have" extensions I recommend installing first.

Just search for them by name in the `Browse` tab of the app:

- **1. User Themes**
    
    - **What it does:** This is **essential**. It allows the `Tweaks` app to change the _shell theme_ (the look of your top bar, notifications, etc.). Install this one first.
        
- **2. Dash to Dock** (or **Dash to Panel**)
    
    - **What it does:** This is a total game-changer. `Dash to Dock` takes Ubuntu's hidden dash and turns it into a permanent, highly-configurable dock (like on macOS).2
        
    - `Dash to Panel` goes a step further and combines your top bar and dock into a single panel (like on Windows or KDE).3
        
- **3. AppIndicator and KStatusNotifierItem Support**
    
    - **What it does:** Remember system tray icons (for apps like Slack, Discord, Steam, etc.)? GNOME hides them by default. This extension brings them back.
        
- **4. ArcMenu**
    
    - **What it does:** If you miss a traditional "Start" menu from Windows or Cinnamon, this is what you want. It adds a menu button to your panel with all your apps in one place.
        
- **5. Blur my Shell**
    
    - **What it does:** This is purely for looks. It adds a modern, "frosted glass" blur effect to your top bar and the activities overview.
        

---

### Step 3: Change the Look with Themes

Now, open the **`Tweaks`** app. Go to the **`Appearance`** tab. This is your command center for theming.

You'll see you can change:

- **Legacy Applications (GTK Theme):** Changes the look of application windows.4
    
- **Icons:** Changes all the icons on the system.
    
- **Shell:** Changes the top bar, dock, and menus (this is often disabled by default, which is why you _need_ the **`User Themes`** extension from Step 2).5
    

Where to get themes:

You can find thousands of free themes and icon packs on the website gnome-look.org.

**A very popular and easy combo to start with:**

1. **Install the Papirus Icon Theme:** It's modern, clean, and has icons for almost every app.
    
    Bash
    
    ```
    sudo apt install papirus-icon-theme
    ```
    
2. **Go to `Tweaks` > `Appearance` > `Icons`** and select **"Papirus"**. Your icons will change instantly.
    
3. You can download GTK themes (like `Orchis` or `Catppuccin`) from the website, extract the folder, and move it to the `.themes` folder in your Home directory (you may need to create it).6
    

---

### Step 4: Final Fine-Tuning (with Tweaks)

The `Tweaks` app is for more than just themes.7 Here are other key things to change:

- **Fonts:** Don't like the default Ubuntu font? Change it here.
    
- **Top Bar:** Add the date, week numbers, or battery percentage to your top bar.8
    
- **Window Titlebars:** Go here to add the **Minimize** and **Maximize** buttons back to your windows (under `Window Titlebars` > `Buttons`).
    

My advice? Start by installing **Extension Manager** and **Tweaks**, then get the **User Themes** and **Dash to Dock** extensions. You'll have a completely different (and much more functional) desktop in 10 minutes.

Would you like help finding a specific theme or a list of more advanced extensions?