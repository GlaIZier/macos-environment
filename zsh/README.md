# Zsh playbook. Didn't work on M1 macbook. Not needed anymore?
https://github.com/viasite-ansible/ansible-role-zsh

## What does it do
Configures zsh

## Installation 
Run `ansible-galaxy install -r requirements.yml` inside this directory to install required Ansible roles.

## Run
ansible-playbook -i "localhost," -c local -K zsh.yml

## Install manually
oh-my-zsh
fzf with useful key bindings like ctrl+r
zsh-autosuggestions
Powerlevel10k theme
