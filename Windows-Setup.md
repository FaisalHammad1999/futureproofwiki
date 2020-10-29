***
*_We recommend combining GitBash and Docker containers during futureproof - and beyond!_* \
*_For more details on other options for local env setup for Windows, check out [this great resource](https://docs.microsoft.com/en-us/windows/dev-environment/overview)._*
***

# Dev Environment Setup: Windows

### Git & GitBash Setup
Install the [latest release of git](https://git-scm.com/). Keep the standard options (make sure Git Bash is included in your install).
When selecting the various options:
- your default editor: Visual Studio Code
- PATH environment: 'Git from the command line and also from 3rd-party software'
- HTTPS transport backend: OpenSSL library
- line ending conversions: 'checkout as-is, commit Unix-style line endings'
- git pull: default
- credential helper: Git Credential Manager Core
- enable symbolic links: true
Others are up to you but the defaults are generally good.

Open GitBash and configure git:
`git config --global user.name "your name"`
`git config --global user.email "your email"`

When working on different operating systems, our line endings may end up getting changed. We will unify our line ending to match Unix-style.
- `git config --global core.autocrlf input`

### Set up your SSH key
- run `ssh-keygen` and follow the instructions. If it asks if you want to overwrite, say no.
- Run `cat ~/.ssh/id_rsa.pub` and copy the output
- Visit the [GitHub SSH Keys setting page](https://github.com/settings/keys) in your browser and click 'New SSH Key'
- Give it a title, anything you like to indicate the machine this key is for, and paste in your key
- The first time you clone and push you may see warning that a new RSA key is being added, this is normal!

*NB: If using the VS Code terminal, we recommend selecting Git Bash as your Default Shell*
- Open a new terminal in VS Code (see the application top bar)
- From the dropdown that likely says 1. Powershell, choose 'Select Default Shell' and select Git Bash
- Close the terminal and open a new one - the dropdown should now say '1. bash'

### Test run GitHub!
Complete [this practice repo](https://github.com/getfutureproof/fp_study_notes_hello_github). Roll call!

### Setup Docker and VSCode Remote - Containers
Complete [this practice repo](https://github.com/getfutureproof/fp_study_notes_hello_docker) using the [accompanying walkthrough](https://github.com/getfutureproof/fp_guides_wiki/wiki/Setting-up-Containers-with-VS-Code)!

### Get node locally and install a global package
Although we will use Docker for many things, we will also install node locally for some global use tools that we may use.
- Download and install nodejs using the [official installers](https://nodejs.org/en/download/)
    + When going through the install wizard, the standard options are all okay for our usage
    + Check the box to allow automatic installation of additional tools
    + Select 'yes' when asked if you want to let node make changes to your device
    + After the installation, lookout for the Powershell window prompting to install the additional tools and press any key to continue
    + Again, select 'yes' when asked if you want to let Powershell make changes to your device
    + Get a cup of tea, this part can take a few minutes as it installs a bunch of other good things!
    + Once the scripts have finished running, follow the prompt to hit ENTER and restart your machine
- Confirm your node installation
    + After restarting your machine, open GitBash and type `node -v`
    + If you see a version number eg. `v12.19.0` then your install was successful!
    + You should also get version numbers back for `npm -v` and `npx -v`
- Install your first global node package
    + `npm install -g laughs`
    + `ha` - wait for a joke! 
