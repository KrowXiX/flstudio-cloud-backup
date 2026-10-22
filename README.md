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















import os
import sys
import zipfile
import datetime
import logging
import subprocess

# =====================================================================
#                 🎵 MUSICIAN CONFIGURATION PANEL 🎵
# =====================================================================
# CHANGE THE TEXT INSIDE THE QUOTES BELOW TO MATCH YOUR COMPUTER SETUP.
# DO NOT TOUCH ANY OF THE CODE OUTSIDE THIS TOP PANEL!

# 1. Type your Windows computer username here:
MY_WINDOWS_USERNAME = "YOUR_WINDOWS_USER"

# 2. Type your unique Google Drive remote folder path name here:
MY_GOOGLE_DRIVE_FOLDER = "projectbackup/FLStudiosProjects"

# 3. Choose how many days you want to keep local backups on your hard drive:
RETENTION_DAYS = 3

# =====================================================================
#             ⚙️ AUTOMATED BACKEND ENGINE (DO NOT TOUCH)
# =====================================================================
# The paths below calculate automatically based on the username entered above.

SOURCE_FOLDER = rf"C:\Users\{MY_WINDOWS_USERNAME}\Documents\Image-Line\FL Studio\Projects\FL Project Files"
LOCAL_STAGING_FOLDER = rf"C:\Users\{MY_WINDOWS_USERNAME}\Backups"      
LOG_FILE_PATH = rf"C:\Users\{MY_WINDOWS_USERNAME}\Backups\backup_status_log.txt"
RCLONE_EXE_PATH = r"C:\rclone\rclone.exe"            
RCLONE_CONFIG_PATH = rf"C:\Users\{MY_WINDOWS_USERNAME}\AppData\Roaming\rclone\rclone.conf"
REMOTE_NAME = "gdrive"                               
# =====================================================================

# Initialize the automated system loggers
os.makedirs(os.path.dirname(LOG_FILE_PATH), exist_ok=True)
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s [%(levelname)s] %(message)s',
    handlers=[
        logging.FileHandler(LOG_FILE_PATH),
        logging.StreamHandler(sys.stdout)
    ]
)

def create_zip_archive(source, destination, timestamp):
    """Recursively packs the target FL Studio folder into a compressed ZIP file."""
    zip_filename = f"fl_projects_backup_{timestamp}.zip"
    full_zip_path = os.path.join(destination, zip_filename)
    
    logging.info(f"Starting compression of FL Projects: {source}")
    
    if not os.path.exists(source):
        logging.error(f"Source folder does not exist: {source}. Please verify your Windows username configuration.")
        return None
        
    try:
        with zipfile.ZipFile(full_zip_path, 'w', zipfile.ZIP_DEFLATED) as zipf:
            for root, dirs, files in os.walk(source):
                for file in files:
                    file_path = os.path.join(root, file)
                    relative_path = os.path.relpath(file_path, source)
                    zipf.write(file_path, relative_path)
        logging.info(f"Compression complete. Archive created: {full_zip_path}")
        return full_zip_path
    except Exception as e:
        logging.error(f"Compression failed: {str(e)}")
        return None

def upload_to_gdrive(local_file_path):
    """Invokes rclone CLI subprocess to push the local archive to the cloud."""
    logging.info(f"Initializing cloud upload via rclone for: {local_file_path}")
    
    rclone_cmd = [
        RCLONE_EXE_PATH,
        "--config", RCLONE_CONFIG_PATH,
        "copy",
        local_file_path,
        f"{REMOTE_NAME}:{MY_GOOGLE_DRIVE_FOLDER}"
    ]
    
    try:
        subprocess.run(rclone_cmd, capture_output=True, text=True, check=True)
        logging.info("Cloud synchronization completed successfully.")
        return True
    except subprocess.CalledProcessError as e:
        logging.error(f"Rclone execution failed. Error output:\n{e.stderr}")
        return False
    except Exception as e:
        logging.error(f"Failed to execute rclone process: {str(e)}")
        return False

def clean_old_local_backups(staging_folder, days_threshold):
    """Data Retention: Scans local staging folder and purges archives older than threshold."""
    logging.info(f"Running local retention check (Purging files older than {days_threshold} days)...")
    now = datetime.datetime.now()
    cutoff_date = now - datetime.timedelta(days=days_threshold)
    
    try:
        for file in os.listdir(staging_folder):
            if file.startswith("fl_projects_backup_") and file.endswith(".zip"):
                file_path = os.path.join(staging_folder, file)
                file_modification_time = datetime.datetime.fromtimestamp(os.path.getmtime(file_path))
                
                if file_modification_time < cutoff_date:
                    os.remove(file_path)
                    logging.info(f"Deleted old local backup: {file}")
        logging.info("Local retention cleanup process completed.")
    except Exception as e:
        logging.error(f"Error during retention cleanup: {str(e)}")

if __name__ == "__main__":
    now_dt = datetime.datetime.now()
    current_time = now_dt.strftime("%Y%m%d_%H%M%S")
    today_string = now_dt.strftime("%Y%m%d")
    
    if os.path.exists(LOG_FILE_PATH):
        try:
            with open(LOG_FILE_PATH, "r") as log_file:
                log_contents = log_file.read()
                if today_string in log_contents and "Backup cycle finalized seamlessly" in log_contents:
                    logging.info("A backup has already been completed today. Skipping run to prevent duplicate spam.")
                    sys.exit(0)
        except Exception as e:
            pass

    logging.info("--- Automated FL Studio Backup Cycle Started ---")
    archive_path = create_zip_archive(SOURCE_FOLDER, LOCAL_STAGING_FOLDER, current_time)
    
    if archive_path:
        upload_success = upload_to_gdrive(archive_path)
        if upload_success:
            logging.info("Backup cycle finalized seamlessly.")
            clean_old_local_backups(LOCAL_STAGING_FOLDER, RETENTION_DAYS)
        else:
            logging.critical("Backup cycle failed during cloud transmission. Retention skip triggered.")
    else:
        logging.critical("Backup cycle aborted due to staging failures.")
