***
*_We recommend using Docker containers during futureproof - and beyond!_* \
*_For more details on other options for local env setup for MacOS, check out the [Optional](https://github.com/getfutureproof/fp_guides_wiki/wiki/MacOS-Setup#Optional) section below._*
***

# Dev Environment Setup: MacOS
_Please note this is designed for MacOS Catalina. If you are on a previous version, you may need to refer to external materials if you come across any issues!_

## Essential: 
### Install Xcode Command Line Tools
In your terminal run: `xcode-select --install` 
If you hit any issues, download directly from [here](https://developer.apple.com/download/more/?=command%20line%20tools) (select the most recent stable version or the one stated for your OS version).
The installation can take a little time - at least 5 minutes. Grab yourself a tea.

### Connect git to GitHub
- Run: `git version` to see if you have git installed.
- You probably do but if not run: `brew install git` (see *Install Homebrew* below) or download directly [here](https://sourceforge.net/projects/git-osx-installer/files/).
- To configure it to connect to your GitHub account run:\
  `git config --global user.email “your@emailadd.com”`\
  `git config --global user.name “yourGithubUsername”`

### Setup an SSH key (and add it to your GitHub account)
- Run: `ssh-keygen` and follow the instructions. If it asks if you want to overwrite, say no.
- Run: `cat ~/.ssh/id_rsa.pub` and copy the output.
- Visit the [GitHub SSH keys settings page](https://github.com/settings/keys) in your browser and click ‘New SSH Key’
- Give it a title (anything you want to indicate the machine this key is for) and paste in your key.

### Test run GitHub!
Complete [this practice repo](https://github.com/getfutureproof/fp_study_notes_hello_github). Roll call!

### Setup Docker and VSCode Remote - Containers
Complete [this practice repo](https://github.com/getfutureproof/fp_study_notes_hello_docker) using the [accompanying walkthrough](https://github.com/getfutureproof/fp_guides_wiki/wiki/Setting-up-Containers-with-VS-Code)!

### Get node locally and install a global package
Although we will use Docker for many things, we will also install node locally for some global use tools that we may use.
- Download and install nodejs using the [official installers](https://nodejs.org/en/download/) or Homebrew (see below)
- Confirm your node installation
    + In your terminal, run `node -v`
    + If you see a version number eg. `v12.19.0` then your install was successful!
    + You should also get version numbers back for `npm -v` and `npx -v`

- Install Node Version Manager
[nvm](https://github.com/nvm-sh/nvm/blob/master/README.md) is a great tool that lets us switch between node versions. This can be extremely useful when working with others.
    + In your terminal, run `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.36.0/install.sh | bash`
    + Confirm the install with `nvm ls`
    + If `nvm ls` gives you an error, see [Troubleshooting](https://github.com/getfutureproof/fp_guides_wiki/wiki/MacOS-Setup#Troubleshooting) below
    + Install a new version of nvm with `nvm install <version>` eg `nvm install 12.19.0`
    + You can install as many different versions of node as you like and switch between them with `nvm use <version>

- Install your first global node package
    + `npm install -g laughs`
    + `ha` - wait for a joke! 

***

## Optional: 
### Install Zsh
- Run: `echo $SHELL` to see if you have Zsh installed. 
- If it does not output `/bin/zsh` run: `brew install zsh` (see *Install Homebrew* below) and `chsh -s /bin/zsh`
- Restart your terminal.

### Install Homebrew
[Homebrew](https://brew.sh/) is a popular package manager for Mac & Linux applications.

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