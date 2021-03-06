# Ansible MacOS environment initializer for developers
[![Build Status](https://travis-ci.org/GlaIZier/macos-environment.svg?branch=master)](https://travis-ci.org/GlaIZier/macos-environment)

## What does it do
1. adds configs (dotfiles like .bashrc) from a github repository to your home directory '~'
2. installs homebrew and homebrew (git, wget, python... check the full list in the config) along with cask (idea, chrome... check the full list in the config) apps ([homebrew role](https://github.com/geerlingguy/ansible-role-homebrew))
3. installs mas and mas applications ([mas role](https://github.com/geerlingguy/ansible-role-mas))
4. installs sdkman and sdk applications ([sdkman role](https://github.com/Comcast/ansible-sdkman))
5. installs zsh, oh-my-zsh and configures it ([oh-my-zsh role](https://github.com/viasite-ansible/ansible-role-zsh))
6. creates a pair of ssh keys
7. installs additional npm, python, ruby and php packages
8. creates folders in the home directory

## Dependencies
[homebrew role](https://github.comgc/geerlingguy/ansible-role-homebrew)\
[mas role](https://github.com/geerlingguy/ansible-role-mas)\
[sdkman role](https://github.com/Comcast/ansible-sdkman)\
[oh-my-zsh role](https://github.com/viasite-ansible/ansible-role-zsh) \
In order to install configs in the home directory, a repository with them is required. My [repository](https://github.com/GlaIZier/configs) is used by default

## Issues
1. For some reason after installing homebrew using the homebrew role, {{ ansible_pkg_mgr }} is unknown in ansible facts (see [this issue](https://github.com/geerlingguy/ansible-role-homebrew/issues/117) and [this issue](https://github.com/Comcast/ansible-sdkman/issues/42)). For a workaround, a manual explicit setup is used in pre_tasks section. Useful links:
https://stackoverflow.com/questions/29305335/how-can-i-persist-an-ansible-variable-across-ansible-roles \
https://everythingshouldbevirtual.com/automation/ansible-using-set_facts-module/ \
https://stackoverflow.com/questions/30763709/ansible-playbook-execute-in-this-order-task-role-task-role-task 
2. For the oh-my-zsh role, it fails on the "Download fzf" step, which uses the unarchive module. This issue is described [here](https://github.com/viasite-ansible/ansible-role-zsh/issues/18) As a workaround fzf is installed by the homebrew role.
3. This role is not fully idempotent. This forcefully re-installs dotfiles, configs and also sdkman packages if their vesions are not specified. 
4. For Russia. If https://get.sdkman.io/ is unreachable, make sure you use proxy by, say, `export {http,https}_proxy="<server>"`. To unset `unset {http,https}_proxy` or reload the machine. 
5. Sometimes even with proxy enabled sdkman does not want to download packages (progress bar is not showing at all and the error about curl or a missing package occurs. No idea why this happens. Maybe switching from offline and online mode could help. Or remove `rm -rf ~/.sdkman` and reinstall sdkman

## Installation
  1. Ensure Apple's command line tools are installed (`xcode-select --install` to launch the installer).
  2. [Install Ansible](http://docs.ansible.com/intro_installation.html).
  3. Clone this repository to your local drive.
  4. Run `$ ansible-galaxy install -r requirements.yml` inside this directory to install required Ansible roles.
  5. Adjust packages to be installed in default.config.yml or create a custom config.yml and overwrite them according to your necessities
  6. Enable proxy (see issues) if needed
Note: If some Homebrew commands fail, you might need to agree to Xcode's license or fix some other Brew issue. Run brew doctor to see if this is the case.

## Run
### Run all tasks
`ansible-playbook main.yml -i inventory -K` inside this directory. Enter your account password when prompted. 
### Run separate tasks
Filter by tags can be used: `ansible-playbook main.yml -i inventory -K --tags "dotfiles,homebrew"` or \
`ansible-playbook main.yml -i inventory -K --tags "dotfiles,homebrew"` \
Or update the config to disable some tasks, e.g. `configure_homebrew: no` \
By using these strategies you can execute tasks one by one.

## Additional setup
1. If you install sdkman, make sure that profiles contain SDKMAN_DIR. Especially if you tried to run the playbook several times
2. Download [powerline](https://github.com/powerline/fonts) fonts \ 
   Choose fonts for powerline for iTerm2 (e.g. Droid Sans Mono for Powerline or another one): iTerm2-Preferences-Profiles-Text-Font \
   More info in Readme of the [oh-my-zsh role](https://github.com/viasite-ansible/ansible-role-zsh)
3. Install color scheme for iTerm2 \
iTerm2-Preferences-Profiles-Colors-Color Presets-Solarized Dark
4. Install transparency and blur
iTerm2-Preferences-Profiles-Window

## Config
You can override any of the defaults configured in `default.config.yml` by creating a `config.yml` file and setting the overrides in that file. For example, you can customize the installed packages and apps with something like:

    homebrew_installed_packages:
      - cowsay
      - git
      - go
    
    mas_installed_apps:
      - { id: 443987910, name: "1Password" }
      - { id: 498486288, name: "Quick Resizer" }
      - { id: 557168941, name: "Tweetbot" }
      - { id: 497799835, name: "Xcode" }
    
    composer_packages:
      - name: hirak/prestissimo
      - name: drush/drush
        version: '^8.1'
    
    gem_packages:
      - name: bundler
        state: latest
    
    npm_packages:
      - name: webpack
    
    pip_packages:
      - name: mkdocs

Any variable can be overridden in `config.yml`; see the supporting roles' documentation for a complete list of available variables.

## Idempotence
This playbook has several inidempotent steps:
1. .zshrc can be replaced by the dotfiles task and then by the .oh-my-zsh role if you run the whole playbook repeatedly. This is done to make this playbook work with with an arbitrary number of included tasks. 
2. The sdkman role changes candidates every time (maybe because the version is not specified?)
3. Sdkman role may fail to add SDKMAN_HOME in profiles when you run this playbook several times. If so, add it manually.

## More information
More information can be found in readmes for every role

## Inspired by [mac-dev-playbook](https://github.com/geerlingguy/mac-dev-playbook) 
