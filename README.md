# 🎵 FL Studio Automated Cloud Backup Utility

Have you ever lost hours of creative work because your laptop crashed, your hard drive died, or you accidentally saved over an old arrangement? 

Most musicians don't know that **you can turn your standard Google Drive account into an automatic, silent backup vault for your music.** You don't have to remember to drag and drop files every day. This utility sets up a private cloud pipeline that automatically packages, compresses, and sends your active FL Studio project folders to Google Drive completely in the background.

### 🌟 What this means for your workflow:
*   **Set it and forget it:** The script runs silently underneath your system.
*   **Saves hard drive space:** It automatically zips your heavy audio sessions and cleans up old local copies on your computer after 3 days.
*   **Zero monthly fees:** Bypasses expensive third-party cloud tools by utilizing your existing, free Google storage account.

---

## ⚙️ How the Automation Process Works

This utility runs an un-attended backend pipeline divided into four distinct phases:
1. **Directory Targeting & Check:** The script validates that your configured local Windows path exists.
2. **Lossless Compression:** It packs all internal `.flp` files and assets into a space-saving `.zip` file stamped with the current date and time.
3. **Programmatic Cloud Routing:** It opens an invisible background window to pass the file directly to your `rclone` cloud configuration without requiring user interaction.
4. **Storage Cleanup:** It checks your local backup folder and securely deletes any local zip files older than 3 days to protect your hard drive storage from filling up.

---

## 🎛️ Automation, Scripting & Cloud Orchestration (Technical Breakdown)
*   **Cloud API Integration:** Engineered an automated, scheduled Python pipeline utilizing the Google Drive API to enforce data preservation and business continuity for media assets.
*   **Data Pipeline & Compression:** Script handles automated directory scanning of active project paths, packages raw source assets into compressed archive formats (.zip), and handles secure programmatic uploads to cloud infrastructure endpoints.
*   **Data Retention Lifecycle Management:** Automates local system housecleaning by cross-referencing file creation timestamps against a prescriptive strict 3-day retention policy threshold, preventing disk saturation.
*   **Rate-Limitation Engine:** Parses historical system logs sequentially at runtime to determine if a successful upload footprint exists for the active 24-hour cycle. If flagged, execution yields immediately to preserve storage bandwidth and avoid rate-limiting ceilings at the Google Cloud gateway.
*   **Defensive Logging Architecture:** Configures structural system logging handlers to concurrently output trace flags to standard runtime output and persistent off-site logging configurations (`backup_status_log.txt`).

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
1. [Click Here to Download backup_script.py](https://github.com) directly to your computer. *(Note: Verify the URL structure matches your repository name)*
2. Right-click the file and open it with Notepad.
3. Change the `MY_WINDOWS_USERNAME = "YOUR_WINDOWS_USER"` to match your actual Windows profile name.
4. Save the file. You can now double-click it anytime to instantly back up your music data!

---

### 🔑 Optional: Advanced Developer API Configuration (Custom Credentials)
For enterprise or production environments where you prefer not to utilize rclone's shared global API endpoint, you can explicitly provision private access scopes:
1. Navigate to the **Google Cloud Console**, create a new project, and enable the **Google Drive API**.
2. Configure your **OAuth Consent Screen** and create desktop application credentials to generate a private `Client ID` and `Client Secret`.
3. Download your secret variables and supply them directly to the `client_id` and `client_secret` prompts during the `rclone config` execution phase.
