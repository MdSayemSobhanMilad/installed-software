# Installed Software Report
 
Small scripts that list the software installed on a computer and save it as a **PDF**, on **Windows** and **Linux (Ubuntu)**. Built so that anyone can run one command, get a PDF, and send it in.
 
- Read-only: the scripts only *read* what is installed. They don't install, change or delete anything.
- No admin rights needed on either system.
- No extra software to install: Windows uses Microsoft Edge or Google Chrome (already on most PCs) to make the PDF; Linux uses the Python 3 that ships with Ubuntu.
---
 
## Quick start for end users
 
Open **`software_report_guide.html`** in a browser, pick your operating system with the toggle, and follow the 3 steps:
 
1. Open PowerShell (Windows) or Terminal (Linux).
2. Click **Copy command**, paste it, press Enter.
3. Upload the generated `installed_software.pdf` from your **Downloads** folder to the Google Form.
The command saves the script into your Downloads folder and creates the PDF in that same folder.
 
### Windows in short
 
- Open a **normal** PowerShell window. Do **not** use "Run as administrator".
- Paste the command and press Enter. It is one long line; that is expected.
- Wait 10–20 seconds. `installed_software.pdf` opens automatically.
### Linux in short
 
- Press `Ctrl`+`Alt`+`T`, paste with `Ctrl`+`Shift`+`V`, press Enter.
- No `sudo` needed.
- If it says `python3` is missing: `sudo apt install python3`, then run the command again.
---
 
## What's in the PDF
 
| Section | Windows | Linux |
|---|---|---|
| Applications | Installed programs (same list as *Settings → Apps*), Start-menu apps incl. Microsoft Store | Apps with a menu launcher (`.desktop` files), incl. Snap and Flatpak apps |
| Packages and tools | Chocolatey and Scoop packages, if installed | Packages you installed yourself via APT, Snap and Flatpak |
 
The PDF also shows the computer name, date, and OS version. It contains **no** personal files, passwords or settings.
 
### What is left out
 
- **Windows:** system components, Windows updates and hotfixes (KB entries).
- **Linux:** everything that comes built in with Ubuntu: what the Ubuntu base packages depend on (`ubuntu-minimal`, `ubuntu-standard`, `ubuntu-desktop` and flavours), packages the installer recorded, "required"/"important" system packages, Ubuntu's default snaps (Firefox, Snap Store, runtimes such as `core22`, `gnome-42-2204`), plus libraries, `-dev` packages, kernels and language packs.
Software that was never installed (for example a portable `.exe` you double-click) is not recorded by the OS and won't appear.
 
---
 
## Files in this folder
 
| File | Purpose |
|---|---|
| `software_report_guide.html` | Step-by-step guide page with the Windows/Linux toggle, copy buttons and the upload-form link. This is the file to share. |
| `software_list_to_pdf.ps1` | Windows: creates the PDF of installed software. |
| `user_software_to_pdf.sh` | Linux: creates the PDF **excluding** Ubuntu's built-in software. |
| `software_list_to_pdf.sh` | Linux: creates the PDF including **everything** (no built-in filtering). |
| `list_software_names.sh` / `.ps1` | Names only, printed in the terminal (no PDF). |
| `list_installed_software.sh` | Linux: APT/Snap/Flatpak/pip/npm/… lists plus common tool versions. |
| `full_system_inventory.sh` | Linux: exhaustive audit that also flags executables not owned by any package. |
 
> The guide page embeds **shortened** copies of the Windows and Linux PDF scripts, with fewer options, so that one pasted command does the whole job. The standalone files above have the full option set.
 
---
 
## Running the scripts directly
 
### Linux
 
```bash
bash user_software_to_pdf.sh                 # saves ~/installed_software_user.pdf
bash user_software_to_pdf.sh -o mylist.pdf   # custom file name/location
bash user_software_to_pdf.sh -g              # applications only
bash user_software_to_pdf.sh -l              # also include libraries / -dev packages
bash user_software_to_pdf.sh -b              # include built-in software too
bash user_software_to_pdf.sh -x '^(php|ibus-table)'   # also treat matching packages as built-in
```
 
`software_list_to_pdf.sh` takes `-o`, `-g` (applications only) and `-a` (all packages, including libraries).
 
### Windows
 
```powershell
powershell -ExecutionPolicy Bypass -File .\software_list_to_pdf.ps1
powershell -ExecutionPolicy Bypass -File .\software_list_to_pdf.ps1 -AppsOnly
powershell -ExecutionPolicy Bypass -File .\software_list_to_pdf.ps1 -All -OutFile C:\Temp\apps.pdf
```
 
`-ExecutionPolicy Bypass` applies to that one run only and does not change system settings. Default output is `installed_software.pdf` on the Desktop (when run directly) or in the script's folder (when started from the guide's command).
 
---
 
## How it works
 
**Windows.** Reads installed programs from the registry uninstall keys, the Start-menu app list (`Get-StartApps`), and Chocolatey/Scoop if present. It builds an HTML page and prints it to PDF with headless Edge (or Chrome). If neither browser is found, it saves and opens the HTML instead, so the user can press `Ctrl`+`P` → *Save as PDF*.
 
**Linux.** Reads `.desktop` launchers for application names, works out which packages are built in (see above), and lists what remains from APT, Snap and Flatpak. The PDF is written by a small Python 3 routine that needs no libraries.
 
---
 
## Troubleshooting
 
| Problem | Fix |
|---|---|
| Windows: "running scripts is disabled" | Run it with `-ExecutionPolicy Bypass` as shown above (the guide's command already does). |
| Windows: only an `.html` file appears | No Edge/Chrome available. Open the `.html`, press `Ctrl`+`P`, choose **Save as PDF**. |
| Windows: parser error mentioning `"@` | Use the latest guide page and refresh it (`Ctrl`+`F5`) before copying the command again. |
| Linux: `python3 is required` | `sudo apt install python3` |
| Linux: some built-in packages still listed | Add them with `-x 'regex'`, or edit the `BUILTIN_SNAPS_RE` line in the script. |
| Linux: not Ubuntu/Debian | The APT sections will be empty; Snap, Flatpak and launcher-based apps still work. |
 
---
 
## For maintainers
 
- **Set the upload form:** in `software_report_guide.html`, change the `FORM_URL` constant near the bottom (it is a placeholder, `https://forms.gle/YOUR_FORM_LINK`, until you replace it).
- **Windows command:** the guide encodes the script as base64 and pastes it as a single line, because the PowerShell console can add spaces to pasted multi-line text and break here-strings. The Windows script therefore avoids here-strings entirely; keep it that way when editing.
- **Linux command:** the guide pastes a heredoc that writes the script to Downloads and runs it.
- The script text lives inside the two `<script type="text/plain">` blocks (`src-windows`, `src-linux`) in the guide page. Edit it there and the copy buttons pick up the change.
## Status
 
- Linux scripts were tested on Ubuntu 24.04 (PDF creation, built-in filtering, launcher detection).
- The Windows script uses standard PowerShell and Edge/Chrome features; test it on your own machines before wide distribution.
## Privacy
 
The report includes software names, the computer name, the date and the OS version. Review the PDF before sharing if any of that is sensitive.
