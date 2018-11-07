# Table of contents

- [Python Virtual Environments](#python-virtual-environments)
  * [Packages required](#packages-required)
  * [Managing virtual environments with virtualenvwrapper](#managing-virtual-environments-with-virtualenvwrapper)
    + [Packages required](#packages-required-1)
    + [bashrc setup](#bashrc-setup)
    + [Virtualenvwrapper useful commands](#virtualenvwrapper-useful-commands)
    + [Examples](#examples)

# Python Virtual Environments



## Packages required

```bash
sudo apt install python-pip
sudo apt install python virtualenv
```



## Managing virtual environments with virtualenvwrapper

While virtual environments certainly solve some big problems with package management, they’re not perfect. After creating a few environments, you will start to see that they create some problems of their own, most of which revolve around managing the environments themselves. To help with this, the virtualenvwrapper tool was created, which is just some wrapper scripts around the main virtualenv tool



A few of the more useful features of virtualenvwrapper are that it:

- Organizes all of your virtual environments in one location
- Provides methods to help you easily create, delete, and copy environments
- Provides a single command to switch between environments



### Packages required



```bash
sudo pip install virtualenvwrapper
```



### bashrc setup

Once installed, you will need to activate its shell functions, which can be done by running source on the installed virtualenvwrapper.sh script



```bash
$ which virtualenvwrapper.sh
/usr/local/bin/virtualenvwrapper.sh
```



Using that path, add the following lines to your shell’s startup file which is your `~/.bashrc`

```bash
export WORKON_HOME=$HOME/.virtualenvs
export PROJECT_HOME=$HOME/projects
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python
export VIRTUALENVWRAPPER_VIRTUALENV=/usr/local/bin/virtualenv
export VIRTUALENVWRAPPER_VIRTUALENV_ARGS='--no-site-packages'
source /usr/local/bin/virtualenvwrapper.sh
```



### Virtualenvwrapper useful commands

You will also now have the shell commands available to you to help you manage the environments. Here are the most used:

| cmd            | Description                                 |
| -------------- | ------------------------------------------- |
| workon         | List or change working virtual environments |
| deactivate     | Switch from a virtual environment           |
| mkvirtualenv   | Create a new environment                    |
| rmvirtualenv   | Remove an environment                       |
| lsvirtualenv   | List all of the environments                |
| lssitepackages | Shows contents of `site-packages` directory |



### Examples



create a new virtual environment

```bash
$ mkvirtualenv my-new-virtualenvironment
(my-new-virtualenvironment) $
```



stopping an existing virtual environment

```bash
(my-new-virtualenvironment) $ deactivate
$
```



listing all virtual environments

```bash
$ workon
my-new-virtualenvironment
my-django-project
web-scraper
```



Activating an existing virtual environment

```bash
$ workon web-scraper
(web-scraper) $
```



