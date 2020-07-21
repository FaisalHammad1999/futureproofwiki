# Local Dev Environment Setup: WSL on Windows 10
_If you are running Windows 10, you have the option of accessing the Windows Subsystem Linux._
_There was a major update to WSL in May 2020 so make sure you know which one you are using / aiming to use whilst setting up, debugging and working in your environment. Be aware that since WSL 2 is (at time of writing) a very recent release, there may be a lot of guides that refer to the original WSL. If the article does not specify, it is likely to be refering to WSL 1. Check out the official comparison docs [here](https://docs.microsoft.com/en-us/windows/wsl/compare-versions). If you can't decide and you are starting from scratch, go for WSL 2._

***NB: WSL2 requires a Windows 10 build of 19041 or higher***

***

## Enable WSL & get your Linux distro
The [official guide](https://docs.microsoft.com/en-us/windows/wsl/install-win10) is excellent and we recommend working through it as your primary source.
When choosing your distro, Ubuntu is a solid choice and has lots of great documentation but if you prefer something else, go for it! The rest of this guide will be based on an Ubuntu install.

***

## Setting up your WSL environment
### Update your system
In your Linux terminal run: `sudo apt-get update`

### Installing packages
Your package manager will depend on the Linux distro you chose. In Ubuntu your installs will generally follow the pattern of:
`sudo apt-get install <package-name>`. Often you will be searching for the 'binaries' of packages/apps. Check out this documentation for installing [nodejs on Ubuntu and Debian distros](https://github.com/nodesource/distributions/blob/master/README.md).

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

### Install Node Version Manager
[nvm](https://github.com/nvm-sh/nvm/blob/master/README.md) is a great tool that lets us switch between node versions. This can be extremely useful when working with others.

### Install Homebrew
[Homebrew](https://docs.brew.sh/Homebrew-on-Linux) is a popular package manager for Mac & Linux applications.

### Code Editor
There is a plethora of code editor options and we encourage you to try a few and see what you feel comfortable with. Depending on which version of WSL you are running you may find that there are differences in the setup and/or additional steps when moving to WSL 2. Check out thees two articles from VS Code on original WSL install and then the WSL 2 updates.
[VS Code install on WSL](https://code.visualstudio.com/docs/remote/wsl)
[VS Code on WSL 2](https://code.visualstudio.com/blogs/2019/09/03/wsl2)
