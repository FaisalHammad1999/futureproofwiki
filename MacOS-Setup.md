***
*_We recommend using Docker containers during futureproof - and beyond!_* \
*_For more details on other options for local env setup for MacOS, check out the [Optional](https://github.com/getfutureproof/fp_guides_wiki/wiki/MacOS-Setup#Optional) section below._*
***

# Dev Environment Setup: MacOS
_Please note this is designed for MacOS Catalina. If you are on a different version, you may need to refer to external materials if you come across any issues!_

## Preparing: 
### Install Xcode Command Line Tools
In your terminal run: `xcode-select --install` 
If you hit any issues, download directly from [here](https://developer.apple.com/download/more/?=command%20line%20tools) (select the most recent stable version or the one stated for your OS version).
The installation can take a little time - at least 5 minutes. Grab yourself a tea.

### Install Homebrew
_Note this is not really "essential" but will be very useful!_
[Homebrew](https://brew.sh/) is a popular package manager for Mac & Linux applications.
- Run `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
- Get another cup of tea and let it do its thing, this could take a while.

---

### Connect git to GitHub
- Run: `git version` to see if you have git installed.
- You probably do but if not run: `brew install git` (this is using Homebrew - see above)
- To configure it to connect to your GitHub account run:\
  `git config --global user.email “your@emailadd.com”`\
  `git config --global user.name “yourGithubUsername”`

### Setup an SSH key (and add it to your GitHub account)
- Run: `ssh-keygen` and follow the instructions. If it asks if you want to overwrite, say no.
- Run: `cat ~/.ssh/id_rsa.pub` and copy the output.
- Visit the [GitHub SSH keys settings page](https://github.com/settings/keys) in your browser and click ‘New SSH Key’
- Give it a title (anything you want to indicate the machine this key is for) and paste in your key.

---

### Test run GitHub!
Complete [this practice repo](https://github.com/getfutureproof/fp_study_notes_hello_github). Roll call!

---

### Setup Docker and VSCode Remote - Containers
Complete [this practice repo](https://github.com/getfutureproof/fp_study_notes_hello_docker) using the [accompanying walkthrough](https://github.com/getfutureproof/fp_guides_wiki/wiki/Setting-up-Containers-with-VS-Code)!

---

### Get node locally and install a global package
Although we will use Docker for many things, we will also install node locally for some global use tools that we may use. [nvm](https://github.com/nvm-sh/nvm/blob/master/README.md) is a great tool that lets us install multiple versions of node and switch between them easily.
- ***Install Node Version Manager**
    + In your terminal, run `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.36.0/install.sh | bash`
    + Confirm the install with `nvm ls`
    + If `nvm ls` gives you an error, see [Troubleshooting](https://github.com/getfutureproof/fp_guides_wiki/wiki/MacOS-Setup#Troubleshooting) below
    + Install a version of node with `nvm install <version>` eg. `nvm install 12.19.0`
    + Make it your default version with `nvm alias default <version>` eg. `nvm alias default 12.19.0` and restart your shell
    + You can install as many different versions of node as you like and switch between them with `nvm use <version>` or update your default as shown above
- **Confirm your node installation**
    + In your chosen shell, run `node -v` (you'll have to reopen the shell if you just set a new default in nvm)
    + If you see a version number eg. `v12.19.0` then your install was successful!
    + You should also get version numbers back for `npm -v` and `npx -v`
- **Install your first global node package**
    + `npm install -g laughs`
    + `ha` - wait for a joke!

---

### Get python locally and discover the Zen of Python
MacOS usually comes with Python 2.7 pre-installed, which is great except that we want Python 3 which is a fair bit different. We will take over control of our Python versions by using a tool called `pyenv`
- **Download pyenv using Homebrew (see below for installing Homebrew) 
    + `brew install pyenv`
- **Install Python 3.9.1 with pyenv**
    + At time of writing 3.9.1 is the latest stable version
    + `pyenv install 3.9.1` (we can easily get other versions later on if you want)
- **Confirm your python installation**
    + In your chosen shell type `python3 --version`
    + If you see a version number eg. `Python 3.9.1` then your install was successful!
    + You should also get a version number back for `python3 -m pip --version`
- **Check out the 'Zen of Python'**
    + `python3` - to enter a python shell (bye, bash!)
    + `import this` - this will show you the Zen of Python!
    + `exit()` - to get back to bash

***

## Optional: 
### Install Zsh
- Run: `echo $SHELL` to see if you have Zsh installed. 
- If it does not output `/bin/zsh` run: `brew install zsh` (see *Install Homebrew* above) and `chsh -s /bin/zsh`
- Restart your terminal.

***

## Frivolous Optional Extras! 
### Some Terminal Alternatives
- [iTerm2](https://www.iterm2.com/) - get in with the cool kids
- [cool-retro-term](https://github.com/Swordfish90/cool-retro-term) - take it back to the ‘good old days’
- [Kitty](https://sw.kovidgoyal.net/kitty/) - honestly I’ve never heard of this until now but it does have a great name

### Some Command Line Cool Stuff
- [oh my zsh](https://ohmyz.sh/) - zsh configuration framework
- [Powerlevel10k](https://github.com/romkatv/powerlevel10k) - awesome theme
- [neovim](https://neovim.io/) - nice alternative to vim if you like command line editors

### General Tools
- [Moom](https://manytricks.com/moom/) - a window management tool for MacOS
- [Alfred](https://www.alfredapp.com/) - a Spotlight alternative and more
- [CheatSheet](https://mediaatelier.com/CheatSheet/?lang=en) - see all available shortcuts for current app by holding command key
- [Bartender](https://www.macbartender.com/) - keep your menu bar neat and tidy

***

## Troubleshooting
### NVM Installation
If the curl command above does not successfully complete the install, you'll need to add some code to your terminal configuration file. To find this file:
- `cd ~`
- `ls -lah`
- In the printed list of files, you are looking for a file called `.zshrc`, `.bashrc`, `.bash_profile` or `.profile`
- Open whichever of those you find with `code <filename>` eg `code .zshrc`
- Into that file, paste the following:
```
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```
- Save the file with <key>Cmd</key>+<key>s</key> and close
- In your terminal run `source ~/<filename>` eg `source ~/.zshrc`
- NVM should now work, test it with `nvm ls`

### Admin/permissions errors
If you are getting permissions errors, you may be using an account which does not have full owner access to the computer's filesystem. If you know that you should (ie. it is your machine or the original owner does not mind you having owner access), you can run the following:
- `cd ~`
- `sudo chown -R <your-username> .`
    + If you are not sure what your username is, it is usually visible in your terminal prompt

### zsh compinit errors
Sometimes zsh gets wrongly suspicious of your files! You can disable this 'feature' by adding the following to your `.zshrc` file (see 'NVM Installation' above)
- `ZSH_DISABLE_COMPFIX="true"`
- Save and close the file and run `source ~/.zshrc` in your terminal