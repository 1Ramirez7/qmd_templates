# qmd_templates
This files should help use qmd files in any new device. This repo will include all the necessary programs, files and packages needed to run this qmd files. 


1.  making venv:
python -m venv .venv

To force a specific python version, use the following command: py -3.12 -m venv .venv
Note that this python version must be installed on your system already, as it should have been installed in the same process when first installing VS code. 


1. activate environment:
Windows powershell: .venv\Scripts\Activate.ps1
Windows CMD: .venv\Scripts\activate.bat
Linux: source .venv/bin/activate
macOS: source .venv/bin/activate

Build using python 3.11 version 3.11.9




list of other dependencies: This should be installed when requirements.txt file is ran but if not, run the needed commands below.
python -m pip install --upgrade pip
python -m pip install jupyter pyyaml

python -m pip install module_to_install  # recommend: use this code when installing any new module


1. Press Ctrl+shift+P for windows or cmd+shift+P for macOS to open the command palette.
2. type: Preferences: Open Keyboard Shortcuts (JSON) 
![alt text](image.png)

3. add this code to the file:
```json
[ // this bracket should be there by default
    {
        "key": "ctrl+alt+k",
        "command": "workbench.action.tasks.runTask",
        "args": "Activate venv + Quarto Preview"
      }
] // this bracket should be there by default
```