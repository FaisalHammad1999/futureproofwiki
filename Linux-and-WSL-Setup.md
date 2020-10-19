***

**_Warning: WSL is not officially supported by futureproof!_**

***
*_We recommend using Docker containers during futureproof - and beyond!_* \
*_For more details on other options for local env setup for Linux, check out the [Optional](https://github.com/getfutureproof/fp_guides_wiki/wiki/Linux-and-WSL-Setup#Optional) section below._*

***
# Dev Environment Setup: Linux / WSL
**Linux** \
If you are on Linux machine (as opposed to running WSL in Windows), skip to [Setting up your environment](https://github.com/getfutureproof/fp_guides_wiki/wiki/Linux-and-WSL-Setup#setting-up-your-environment). \
This guide is based on an Ubuntu 18.04 Bionic distro but can be adjusted to suit.

**WSL on Windows 10** \
_If you are running Windows 10, you have the option of accessing the Windows Subsystem Linux._
_There was a major update to WSL in May 2020 so make sure you know which one you are using / aiming to use whilst setting up, debugging and working in your environment. Be aware that since WSL 2 is (at time of writing) a very recent release, there may be a lot of guides that refer to the original WSL. If the article does not specify, it is likely to be referring to WSL 1. Check out the official comparison docs [here](https://docs.microsoft.com/en-us/windows/wsl/compare-versions). If you can't decide and you are starting from scratch, go for WSL 2._

***NB: WSL2 requires a Windows 10 build of 19041 or higher***

### Enable WSL & get your Linux distro
The [official guide](https://docs.microsoft.com/en-us/windows/wsl/install-win10) is excellent and we recommend working through it as your primary source.
When choosing your distro, Ubuntu is a solid choice and has lots of great documentation but if you prefer something else, go for it! The rest of this guide will be based on an Ubuntu install.

***

# Setting up your environment
## Essential: 

### Update your system
In your Linux terminal run: `sudo apt-get update` (this command may differ depending on your Linux distro)

### Connect git to GitHub
- Run: `git --version` to see if you have git installed.
- If git is not installed, find your appropriate install command [here](https://git-scm.com/download/linux) or run: `brew install git` (see *Install Homebrew* below).
- To configure it to connect to your GitHub account run:\
  `git config --global user.email “your@emailadd.com”`\
  `git config --global user.name “yourGithubUsername”`

### Setup an SSH key (and add it to your GitHub account)
- Run: `ssh-keygen` and follow the instructions. If it asks if you want to overwrite, say no.
- If you get stuck in this process, [this walk-through](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-1604) gives detailed info on each step.
- Visit the [GitHub SSH keys settings page](https://github.com/settings/keys) in your browser and click ‘New SSH Key’
- Give it a title (anything you want to indicate the machine this key is for) and paste in your key.

### Test run GitHub!
Complete [this practice repo](https://github.com/getfutureproof/fp_study_notes_hello_github). Roll call!

### Setup Docker and VSCode Remote - Containers
Complete [this practice repo](https://github.com/getfutureproof/fp_study_notes_hello_docker) using the [accompanying walkthrough](https://github.com/getfutureproof/fp_guides_wiki/wiki/Setting-up-Containers-with-VS-Code)!

***

## Optional
### Installing packages
Your package manager will depend on the Linux distro you chose. In Ubuntu your installs will generally follow the pattern of:
`sudo apt-get install <package-name>`. Often you will be searching for the 'binaries' of packages/apps. Check out this documentation for installing [nodejs on Ubuntu and Debian distros](https://github.com/nodesource/distributions/blob/master/README.md).

### Install Node Version Manager
[nvm](https://github.com/nvm-sh/nvm/blob/master/README.md) is a great tool that lets us switch between node versions. This can be extremely useful when working with others.

### Install Homebrew
[Homebrew](https://docs.brew.sh/Homebrew-on-Linux) is a popular package manager for Mac & Linux applications.
