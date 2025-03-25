# Linux Basic Commands
## Index

- [Basic Linux Commands](#basic-linux-commands)
- [Directory Commands](#directory-commands)
- [File Commands](#file-commands)
- [Users](#-users)
    - [Switch User (Local Machine)](#-switch-user-local-machine)
    - [Secure Remote Login](#-secure-remote-login)
    - [User Accounts: Root, Regular Users & Sudo](#-user-accounts-root-regular-users--sudo)
- [Downloading Files in Linux](#-downloading-files-in-linux)
    - [Using `curl`](#-using-curl)
    - [Using `wget`](#-using-wget)
- [Packages Management](#packages-management)
    - [RPM (Red Hat Package Manager)](#-rpm-red-hat-package-manager)
    - [YUM (Yellowdog Updater, Modified)](#-yum-yellowdog-updater-modified)
- [Services](#services)
    - [systemctl / service (Service Management Tool)](#-systemctl--service-service-management-tool)
    - [Custom systemd Service (my_app)](#-custom-systemd-service-my_app)
    - [Creating a systemd Service That Runs on Boot](#-creating-a-systemd-service-that-runs-on-boot)
- [VI Editor](#vi-editor)
    - [Move Around](#-move-around)
    - [Delete](#-delete)
    - [Copy & Paste](#-copy--paste)
    - [Scroll Up/Down](#-scroll-updown)
- [Save & Quit Commands](#save--quit-commands)
    - [Enter Command Mode](#-enter-command-mode)
    - [Save](#-save)
    - [Quit](#-quit)
    - [Save & Quit](#-save--quit)
- [Find (Search) in Command Mode](#find-search-in-command-mode)
    - [Find Text](#-find-text)
    - [Repeat Search](#-repeat-search)

---

## Basic Linux Commands

- `echo Hi`  
  👉 Prints text to the screen.

- `ls`  
  👉 Lists files and folders in the current directory.

- `cd my_dir1`  
  👉 Changes to the directory `my_dir1`.

- `pwd`  
  👉 Shows the Present Working Directory.

- `mkdir new_directory`  
  👉 Creates a new directory named `new_directory`.

- `cd new_directory; mkdir www; pwd`  
  👉 Runs multiple commands:
  - Changes to `new_directory`
  - Creates a folder `www`
  - Prints the current directory path

## Directory Commands

- `mkdir /tmp/asia`  
  👉 Creates a single directory.

- `mkdir /tmp/asia/india`  
  👉 Creates a subdirectory (requires parent to exist).

- `mkdir /tmp/asia/india/bangalore`  
  👉 Creates another level (requires full path to exist).

- `mkdir -p /tmp/asia/india/bangalore`  
  👉 Creates the full directory hierarchy in one command.

- `rm -r /tmp/my_dir1`  
  👉 Removes the directory `my_dir1` and its contents recursively.

- `cp -r my_dir1 /tmp/my_dir1`  
  👉 Copies the directory `my_dir1` and its contents to `/tmp/my_dir1`.


## File Commands

- `touch new_file.txt`  
  👉 Creates a new empty file.

- `cat > new_file.txt`  
  👉 Adds content to the file (press `CTRL+D` to save and exit).

- `cat new_file.txt`  
  👉 Displays the contents of the file.

- `cp new_file.txt copy_file.txt`  
  👉 Copies the file to a new file named `copy_file.txt`.

- `mv new_file.txt sample_file.txt`  
  👉 Moves or renames `new_file.txt` to `sample_file.txt`.


---

## 👤 Users

### 🔄 Switch User (Local Machine)
- `su <username>`  
👉 Used to **switch to another user** on the same machine. You’ll need that user's password.  
✅ Useful when you need to perform actions as another user (e.g., root or admin tasks).

### 🌐 Secure Remote Login
- `ssh <username>@<ip-address>`  
👉 Connects to a **remote machine securely** via SSH (Secure Shell). You'll be prompted for the remote user’s password.

## 👤 User Accounts: Root, Regular Users & Sudo

### 👑 What is the Root User?
- The **root** user is the **superuser** in Unix/Linux systems.
- Has **unrestricted access** to all files, directories, and commands.
- Can install software, manage system-wide settings, add/remove users, etc.

### 👤 What is a Regular User?
- A **regular user** (e.g., `matthew`) has **limited permissions**.
- Cannot access protected system areas (e.g., `/root`) or install packages without permission.

### 🔐 What is `sudo`?
- `sudo` stands for **"superuser do"**.
- It allows a **regular user** to run specific commands **with elevated privileges**.
- Root users can grant `sudo` access to trusted users via the **`/etc/sudoers`** file.

### ✅ Why use `sudo` instead of logging in as root?
- It’s **safer** — only specific commands are elevated.
- It provides **auditing** — sudo usage is logged.
- Reduces risk of accidental system-wide changes.

```bash
ls /root
# ❌ Permission denied for regular users

sudo ls
```

---

## 🌐 Downloading Files in Linux

You can use `curl` or `wget` to download files from the internet via the command line.

### 📥 Using `curl`
```bash
curl http://www.some-site.com/some-file.txt -O
```
- Downloads the file from the given URL.
- `-O` saves the file with its original name.
- Ideal for API interactions and multi-protocol support.

### 📥 Using `wget`
```bash
wget http://www.some-site.com/some-file.txt -O some-file.txt
```
- Downloads the file from the given URL.
- `-O` allows renaming the output file.
- Great for automation and background downloads.

----

## 📦 Packages Management

### 📦 RPM (Red Hat Package Manager)
- `rpm -i package.rpm`  
    👉 Installs the specified RPM package.  
    ❗ Note: This command does not resolve or install dependencies.

- `rpm -e package_name`  
    👉 Uninstalls the specified RPM package by name.  
    ❗ Note: This command does not remove dependencies that were installed with the package.

- `rpm -U package.rpm`  
    👉 Upgrades or installs the specified RPM package.  
    ❗ Note: This command does not resolve or install dependencies.

- `rpm -qa`  
    👉 Lists all installed RPM packages.

- `rpm -q package_name`  
    👉 Checks if the specified RPM package is installed.  
    *Example:* `rpm -q httpd` checks if the `httpd` package is installed.

### 📦 YUM (Yellowdog Updater, Modified)
- `yum install package_name`  
    👉 Installs the specified package along with its dependencies.

- `yum remove package_name`  
    👉 Uninstalls the specified package and its dependencies.

- `yum update package_name`  
    👉 Updates the specified package to the latest version.

- `yum list installed`  
    👉 Lists all installed packages.

- `yum repolist`  
    👉 Displays the list of enabled repositories.

- `yum install package_name-version`  
    👉 Installs a specific version of the package.  
    *Example:* `yum install httpd-2.4.6` installs version 2.4.6 of the `httpd` package.
---

## 🛠️ Services

### 🛠️ systemctl / service (Service Management Tool)
- `service httpd start` or `systemctl start httpd`  
    👉 Starts the `httpd` (Apache) service.

- `systemctl stop httpd`  
    👉 Stops the `httpd` service.

- `systemctl status httpd`  
    👉 Checks the current status of the `httpd` service (running, stopped, etc).

- `systemctl enable httpd`  
    👉 Configures the `httpd` service to start automatically at system boot.

- `systemctl disable httpd`  
    👉 Prevents the `httpd` service from starting at boot time.

### ⚙️ Custom systemd Service (my_app)

You can create and manage your own custom services using `systemd` by defining a ***systemd unit file***.

Example file: `/etc/systemd/system/my_app.service`

```ini
[Service]
ExecStart=/usr/bin/python3 /opt/code/my_app.py
```

This tells systemd to run `my_app.py` using Python 3.

THen, reload systemd to register the new service:

```bash
systemctl daemon-reload
```

👉 Reloads all unit files. Must be run after adding or modifying `.service` files.

Now, start the Custom Service:

```bash
systemctl start my_app
```

👉 Starts the `my_app` service.

We can also check the Service Status:

```bash
systemctl status my_app
```

👉 Shows detailed status info, including logs, PID, and run state (e.g., `active (running)`).

📝 Tip: Place your custom `.service` file in `/etc/systemd/system/` and make sure it is owned by root for production use.

### ⚙️ Creating a systemd Service That Runs on Boot
To make a custom service (e.g., `my_app`) start when the system boots:

---

1. Create the Service File:

Create a file named `/etc/systemd/system/my_app.service` with the following content:

```bash
[Unit]
Description=My python web application

[Service]
ExecStart=/usr/bin/python3 /opt/code/my_app.py
ExecStartPre=/opt/code/configure_db.sh  # Runs a script to configure the database before starting the service
ExecStartPost=/opt/code/email_status.sh  # Runs a script to send an email notification after the service starts
Restart=always  # Ensures the service restarts automatically if it crashes

[Install]
WantedBy=multi-user.target
```
Here, each section stands for:
- `Description`: A short summary of the service.
- `ExecStart`: The command to run your app.
- `WantedBy=multi-user.target`: Ensures the service runs at system boot.

2. Reload systemd to recognize the new service

```bash
sudo systemctl daemon-reload
```

3. Start the Service
```bash
sudo systemctl start my_app
```

4. Enable the Service at Boot (This ensures `my_app` will start automatically every time the system boots.)
```bash
sudo systemctl enable my_app
```

---

## VI Editor
- `vi new_file.txt`  
    👉 Opens `new_file.txt` in the VI editor.

- `i`  
    👉 Switches to insert mode to start editing the file.

### 📦 Move Around
- `h` → Move left  
- `l` → Move right  
- `j` → Move down  
- `k` → Move up  

### 🗑️ Delete
- `x` → Delete character under cursor  
- `dd` → Delete entire line  

### 📋 Copy & Paste
- `yy` → Copy (yank) the current line  
- `p` → Paste after the current line  VI Editor - 

### 🔃 Scroll Up/Down
- `CTRL + u` → Scroll up  
- `CTRL + d` → Scroll down  

## Save & Quit Commands

### 💻 Enter Command Mode
- `:` → Enters command mode (used before all save/quit commands)

### 💾 Save
- `:w` → Save the current file
- `:w filename` → Save with a new filename

### ❌ Quit
- `:q` → Quit (if no changes)
- `:q!` → Quit without saving (force discard changes)

### ✅ Save & Quit
- `:wq` → Save and quit the editor

## Find (Search) in Command Mode

### 🔍 Find Text
- `/pattern` → Search for `pattern` forward in the file  
  *Example:* `/of` will highlight all instances of “of”

### 🔁 Repeat Search
- `n` → Jump to the next match
- `N` → Jump to the previous match

---