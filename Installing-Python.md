Although, at time of writing, Python 2.x is still to be found as the default `python` install on many systems, it is no longer maintained since January of 2020. Python 3.x has some distinct differences so unless you specifically want to test your code in 2.x, make sure you have access to Python 3.x. You can confirm your version with `python --version`.

There are multiple ways to manage your Python versions. This guide looks at just one of them.

## Installing [pyenv](https://github.com/pyenv/pyenv) / [pyenv-win](https://github.com/pyenv-win/pyenv-win)
**On MacOS and Linux**, [pyenv](https://github.com/pyenv/pyenv) is a great option for installing multiple versions and being able to switch between them.
- Install `pyenv` via homebrew: `brew install pyenv`
- Add pyenv init to your shell:
    - `echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.<your-shell-config-file>`
    - Your shell config file may be `.zshrc`, `.bash_profile`, `.bashrc`
    - If you are not sure, `cd ~`, `ls -lah` and see which one is there!

**On Windows**, there is a [pyenv-win](https://github.com/pyenv-win/pyenv-win) port.
- Install `pyenv` via [Chocolatey](https://chocolatey.org/): `choco install pyenv-win` _(Do not install `pyenv` via your administrative shell as this will not give you access as a local user)_
- Close and reopen your terminal app and run `pyenv --version`
    - If you receive a command not found error, check the [documentation](https://github.com/pyenv-win/pyenv-win#finish-the-installation) for how to manually set environment variables rvrn if you used [Chocolatey](https://chocolatey.org/).
- Navigate to your home directory and run `pyenv rehash`

## Basic [pyenv](https://github.com/pyenv/pyenv) / [pyenv-win](https://github.com/pyenv-win/pyenv-win) usage
**On MacOS and Linux**
- Install a Python version with pyenv: `pyenv install <latest-version>` (use `pyenv install -l` to see all the options, you will need to replace `<latest-version>` with the actual latest version eg. 3.9.1!)
- See your installed Python version: `pyenv versions`
- Switch to a version: `pyenv global <version>` eg. `pyenv global 3.9.1`
- Close and reopen your terminal app.
- Confirm success by running `python --version`

**On Windows**

- Install a Python version with pyenv: `pyenv install <latest-version>` (use `pyenv install -l` to see all the options. If you do not see all the versions of Python you are expecting you may need to run `pyenv update`. You will need to replace `<latest-version>` with the actual latest version eg. 3.9.1!).
- See your installed Python version: `pyenv versions`
- Switch to a version: `pyenv global <version>` eg. `pyenv global 3.9.1`
- Run `pyenv rehash` to update pyenv with your new settings.
- Confirm success by running `python --version`

_Note: if you have previously installed Python on your machine this may override pyenv-win and so you might have to [uninstall this before pyenv will work](https://www.educative.io/edpresso/how-to-uninstall-python)._