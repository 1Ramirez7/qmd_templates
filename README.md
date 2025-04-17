# qmd_templates
This files should help use qmd files in any new device. This repo will include all the necessary programs, files and packages needed to run this qmd files. 





1.  making venv:
python -m venv .venv

To force a specific python version: py -3.12 -m venv .venv
Note that this python version must be installed on your OS already, it should have been installed in the same process when first installing VS code. 


1. activate environment:
Windows powershell: .venv\Scripts\Activate.ps1
Windows CMD: .venv\Scripts\activate.bat
Linux: source .venv/bin/activate
macOS: source .venv/bin/activate

Build using python 3.12 version 3.12.10

Freeze current packages: pip freeze > requirements.txt
Install from txt file: pip install -r requirements.txt



list of other dependencies: This should be installed when requirements.txt file is ran but if not, run the needed commands below.
python -m pip install --upgrade pip
python -m pip install jupyter pyyaml
python -m pip install module_to_install  # recommend: use this code when installing any new module





## Issues with running qmd files/code chunks

As more libraries/packages are installed to improve the qmd files and or simply doing what the AI chatbot said, you can run into issues. Sometimes AI's will have you input code in the terminal that can change the environment paths or other things that only CS majors would know. Installing new packages/libraries can overwrite other libraries/packages which is really hard to troubleshoot. AI's love to include random libraries that are not needed, so just tell AI to work with the libraries/packages you are working with unless the new libary/package is 100% needed. These qmd files are an introduction to using quarto markdown files, but once you want to explore better functions then this set up may not be enough. Beware of going above and beyond becuase it is common to hit a bug at 10:30pm where nothing is working properly, and you will have no idea why (and AI might just be running around in loops).

If you are having troubles running the qmd files, you can always delete most files and restart. 
You can delete: .Rhistory, freeze folder, and .jupyter_cache folder.  These file are automatically made when running the qmd files. 

If you are having more torubles, you can just save the qmd files in a temporary folder, and delete the whole repository off your laptop/pc and re-clone the orginal repository. You will than just re-run code to make the enviroment and install dependencies. Then re add your qmd files. 

If you are still having troubles then I recommend going to the data or computer science labs for help. 

