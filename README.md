# flstudio-cloud-backup
A hands-on enterprise homelab showcasing Linux/Windows virtualization, Python automation, complex Layer 3 traffic routing (WireGuard split tunnels), and Dynamic DNS/port-forwarding pipeline architecture.



---

## 🎵 Step-by-Step Guide for Musicians (How to Setup)

If you are a producer or musician who wants to use this script to protect your FL Studio projects, follow these 3 steps to get everything up and running!

### Step 1: Install the Tools
1. Download **Python** from the official website and check the box that says "Add Python to PATH" during installation.
2. Download **rclone** (the cloud sync tool) from [rclone.org](https://rclone.org), extract the folder, copy the `rclone.exe` file, and paste it directly into your `C:\` drive inside a folder named `rclone` (so the path is `C:\rclone\rclone.exe`).

### Step 2: Connect Your Google Drive (No Coding Required)
Rclone has a built-in assistant that connects to Google Drive safely using a standard web login popup. 
1. Open your Windows search bar, type `cmd` (Command Prompt), and press enter.
2. Type `C:\rclone\rclone.exe config` and press enter.
3. Type `n` to create a new remote path.
4. Name it exactly: `gdrive`
5. A giant list of options will appear. Type the number corresponding to **Google Drive** (or just type `drive`) and press enter.
6. Leave the Client ID and Client Secret blank (just press enter through them).
7. When it asks for the scope, type `1` (Full access to your drive) and press enter.
8. Leave the root folder ID blank (press enter).
9. When it asks to "Use web browser to automatically authenticate," press `Y` and hit enter.
10. A standard Google Login window will automatically pop open in your internet browser. Log into your account and click **Allow**. 
11. Go back to your Command Prompt screen, press `q` to quit the setup wizard, and close the window. Your cloud configuration is completely done!

### Step 3: Configure the Script
1. Download the `backup.py` file from this repository.
2. Right-click the file and open it with Notepad.
3. Change the `MY_WINDOWS_USERNAME = "YOUR_WINDOWS_USER"` to match your actual Windows profile name.
4. Save the file. You can now double-click it anytime to instantly back up your music data!

