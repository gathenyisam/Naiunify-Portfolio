Git installation and setup (Windows)

This document explains how to install Git on Windows, add it to your PATH, verify the installation, and initialize a repository.

1. Quick verify (check first)

- Open a NEW Command Prompt or PowerShell window and run:

```powershell
git --version
where git
```

- If `git --version` prints a version (for example `git version 2.42.0.windows.1`), Git is already installed.
- If you get `'git' is not recognized...`, follow the steps below.

2. Download and install Git for Windows

- Download the official installer from: https://git-scm.com/download/win
- Run the downloaded installer and follow the prompts.

Recommended installer choices (safe defaults):

- "Select the editor used by Git": choose your preferred editor (or keep the default).
- "Adjusting your PATH environment": choose "Git from the command line and also from 3rd-party software" (this adds Git to PATH).
- "Choosing the HTTPS transport backend": keep the default (OpenSSL).
- "Configuring the line ending conversions": choose "Checkout Windows-style, commit Unix-style" (recommended for most projects).
- "Configuring the terminal emulator to use with Git Bash": choose whichever you prefer (MinTTY or Windows default console).
- "Configuring extra options": enabling "Git Credential Manager Core" is recommended.

Finish the install. Close the installer.

3. If you did NOT add Git to PATH during install, add it manually

- Typical install paths:
  - `C:\Program Files\Git\cmd` (64-bit)
  - `C:\Program Files (x86)\Git\cmd` (32-bit)
- To add to PATH (Windows 10/11):
  1. Press Start and type "Environment variables" → open "Edit the system environment variables".
  2. Click "Environment Variables...".
  3. Under "User variables for <you>" (or under "System variables") select `Path` and click `Edit`.
  4. Click `New` and paste the folder path where `git.exe` exists (for example `C:\Program Files\Git\cmd`).
  5. Click OK to close dialogs.
- Important: Open a NEW terminal after changing PATH.

4. Verify installation

- In a new Command Prompt / PowerShell run:

```powershell
git --version
where git
```

- If both succeed and show a path/version, installation is correct.

5. Configure Git (first-time only)

- Set your name and email (these appear in commits):

```powershell
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

- Optional: check config

```powershell
git config --list
```

6. Initialize your project repository

- In the project folder (example path in this workspace):

```powershell
cd "C:\Users\Admin\MY PORTFOLIO 2.0\NAIUNIFY"
# initialize
git init
# add files
git add .
# create initial commit
git commit -m "Initial commit"
```

- Create a useful `.gitignore` file for typical projects. Example minimal `.gitignore`:

```
node_modules/
.env
dist/
build/
*.log
.DS_Store
```

7. (Optional) Create SSH key and add to GitHub (if you plan to push)

```powershell
ssh-keygen -t ed25519 -C "you@example.com"
# follow prompts (press enter to accept defaults)
# copy public key to clipboard
cat $env:USERPROFILE\.ssh\id_ed25519.pub | clip
# then paste into GitHub > Settings > SSH and GPG keys
```

8. Troubleshooting

- If `git --version` still fails after installing and adding to PATH:
  - Ensure the path you added actually contains `git.exe` (open the folder in Explorer).
  - Make sure you opened a NEW terminal after changing PATH.
  - Try restarting Windows if problems persist.
- If `where git` shows multiple entries, ensure the one you expect is listed first in `Path` order.

9. GUI alternatives

- If you prefer a GUI installer that includes Git, consider:
  - GitHub Desktop: https://desktop.github.com/
  - Sourcetree: https://www.sourcetreeapp.com/

---

This file was added into the project to help set up Git on Windows and initialize the repository.
