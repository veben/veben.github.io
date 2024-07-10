---
title: ".configurations"
date: 2024-07-10
tags:
  - ubuntu
  - zsh
  - workflow
  - dotfiles
---

Today I will share with you the **Configurations** (also known as **Dotfiles**) that I use for Windows and Ubuntu.

Saving your configurations is crucial for several reasons. It preserves customizations, ensures portability and consistency across environments, facilitates recovery from system failures, and saves time on new installations.

Best practices include backing up your dotfiles in a Git repository to track changes over time. On your system, centralize them in a single directory and use symlinks to point to the correct locations. Additionally, automate their installation to easily reinstall an environment from scratch.

You can find my configuration repository [here](https://github.com/veben/.configurations)

## Windows configurations
My configurations for setting up a fresh Windows environment are straightforward:
- Activate **Dark Mode**
- Install the **QWERTY International Keyboard**
- Install and customize **WSL2 with Ubuntu**, **Windows Terminal**, and **VSCode**

For more information in the specific steps, refer to this [readme](https://github.com/veben/.configurations/blob/main/windows/README.md)

## Ubuntu configurations
My configurations for a fresh Ubuntu environment involves several steps to ensure everything runs smoothly and efficiently:
- **Utilities**: Install `git`, `jq` and `xclip`
- **Font**: Install a Nerd Font for improved terminal aesthetics.
- **Shell**: Install **zsh** and set it as the default shell.
- **Tools**: Install additional cool tools like `starship`, `eza`, `fzf` and `lazygit`
- **Configurations**: Implement various configuration settings.

For a more detailled list, refer to this [readme](https://github.com/veben/.configurations/blob/main/ubuntu/README.md)

### Configuration Directory Structure
All configurations are centralized in a single directory named `.config` in my home directory. Here’s the structure:

```plaintext
├── .config
│  ├── git
│  │  ├── .gitconfig
│  │  ├── .gitconfig.perso
│  │  └── .gitconfig.pro
│  ├── starship.toml
│  └── zsh
│     ├── .zaliases
│     ├── .zhistory
│     ├── .zplugins
│     ├── .zprofile
│     ├── .zshrc
│     └── plugins
├── README.md
├── scripts
└── setup.sh
```

### Git Configurations
The `git` folder contains all Git-related configurations, including aliases, email, and username settings. The `.gitconfig` file dynamically includes either `.gitconfig.perso` or `.gitconfig.pro` based on the context. A detailed blog post on this setup will be published in the future.

### Zsh configurations
The `zsh` folder is organized into multiple configuration files:
- `.zaliases`: Defines all aliases.
- `.zhistory`: Manages command history settings.
- `.zplugins`: Points to plugins located in the `plugins` folder.
- `.zprofile`: Contains configurations for tools like **Docker**.
- `.zshrc`: The main configuration file for zsh.

Again, a future blog post will delve deeper into zsh configurations.

### Starship configurations
[Starship](https://starship.rs/) offers extensive customization for your shell prompt. Here’s how my prompt looks:

<img src="../assets/images/shell_prompt.png" alt="shell_prompt" style="width:800px;height:auto;">

You can explore all the configurations and presets [here](https://starship.rs/config/)

### Scripts
The `scripts` folder includes scripts frequently used and intended for installation in a fresh environment.

### Setup
The `setup.sh` script automates the installation of all configurations in an Ubuntu environment. For example, the following lines create symbolic links to point to configurations in the `.config` folder:

```sh
export XDG_CONFIG_HOME=$HOME/.config
ln -sf $XDG_CONFIG_HOME/git/.gitconfig $HOME/.gitconfig
ln -sf $XDG_CONFIG_HOME/zsh/.zshrc $HOME/.zshrc
```

You can visualize the full script [here](https://github.com/veben/.configurations/blob/main/ubuntu/setup.sh)