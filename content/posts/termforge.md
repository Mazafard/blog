---
title: "TermForge: Modernizing My Terminal Workflow"
date: 2025-11-27T00:00:00+00:00
draft: false
tags: ["Terminal", "Ansible", "DevOps", "macOS", "Kubernetes", "Neovim"]
categories: ["Tools", "DevOps"]
---

I’ve always liked the original [jazik/termenv](https://github.com/jazik/termenv) project—it’s a clean Ansible playbook that bootstraps a great terminal environment. But after living in it for a while, I found myself wanting tighter macOS support, automatic iTerm2 provisioning, and some Kubernetes-focused tooling. I decided to fork the project and build out those extra pieces. The result is **TermForge**, my refreshed take on the “terminal in a box” idea.

## Why Fork Instead of PR?

Initially, I explored incremental improvements to the upstream repo. Pretty quickly the changes snowballed—new playbooks, an opt-in name change, macOS-only roles, documentation rewrites, and Kubernetes helpers that modify multiple files. Rather than disrupt the original’s scope, I forked it and leaned into a new project identity while crediting the original work.

---

## Feature Highlights

### 1. Cross-Platform Shell Stack
*   **Linux**: still uses native package managers with `sudo`.
*   **macOS**: now leans on Homebrew/Homebrew Cask for `zsh`, `tmux`, fonts, Node, and iTerm2.
*   **Automatic zsh detection**: resolves the installed `zsh` path (`/opt/homebrew/bin/zsh` on Apple Silicon) before making it the login shell and adds it to `/etc/shells`.
*   **dircolors on macOS**: Homebrew `coreutils` + an alias block ensure `dircolors` works even though macOS doesn’t ship it.

**Try it:**
```bash
ansible-playbook -i hosts --ask-become-pass termforge.yml --tags "zsh,zsh-dircolors-solarized"
```

### 2. iTerm2 Provisioning (macOS)
*   Installs iTerm2 via Homebrew Cask when it isn’t already present.
*   Skips automatically if `/Applications/iTerm.app` exists (no errors when you’ve already installed it manually).

**Target the role alone:**
```bash
ansible-playbook -i hosts --ask-become-pass termforge.yml --tags iterm2
```

### 3. Fonts + UI Polish
*   Nerd Fonts downloader installs Fira Mono by default (or any archive you specify).
*   On macOS, fonts land in `~/Library/Fonts/<Font>`, so they appear instantly without `fc-cache`.
*   iTerm2 post-install instructions in the README walk through selecting the Nerd Font, enabling ligatures, or pointing iTerm2 at your sync folder.

### 4. Neovim Stack (Lua, Telescope, Treesitter, Copilot Optional)
*   Installs Neovim plus ripgrep for Telescope.
*   Copies a ready-to-use Lua config (keymaps, plugins, LSP).
*   Optional `neovim-copilot.yml` playbook installs Copilot/CopilotChat later.
*   CSpell role ensures Node/npm, installs `cspell`, drops config, and injects keymaps for diagnostics.

**Install Copilot later:**
```bash
ansible-playbook -i hosts neovim-copilot.yml
```

### 5. Kubernetes Helpers
*   New `kubernetes-tools` role checks whether `kubectl` is already in your `PATH`.
*   If yes:
    *   **macOS**: installs `k9s` and `kubectx` via Homebrew (kubectx ships `kubens`, so no extra formula needed).
    *   **Linux**: downloads the k9s binary tarball, installs to `/usr/local/bin`, clones the kubectx repo, and symlinks both `kubectx` and `kubens`.
*   If `kubectl` isn’t present, the entire role quietly skips—no bloat until you actually work with clusters.

**Force only the helper role:**
```bash
ansible-playbook -i hosts termforge.yml --tags k8s-tools
```

### 6. Quality-of-Life Tweaks
*   `fzf` install includes the oh-my-zsh plugin wiring plus default options (`--height 40% --layout=reverse --border`).
*   `tmux` role installs the theme, sets `base-index 1`, and adds Smart Vim/Tmux navigation bindings.
*   README now documents:
    *   macOS-specific prerequisites (Homebrew install, interpreter warning, how to set `ANSIBLE_PYTHON_INTERPRETER`).
    *   Tag usage and role combinations.
    *   Docker + Vagrant test setups with updated instructions for the renamed `termforge.yml`.
    *   A refreshed Neovim cheat sheet and screenshots.

---

## Usage Overview

### Clone + Run
```bash
git clone https://github.com/<your-username>/termforge.git
cd termforge
ansible-playbook -i hosts --ask-become-pass termforge.yml
```
*   `termenv.yml` still exists but now just imports `termforge.yml`, so legacy scripts keep working.

### Customize with Tags
Examples:
```bash
ansible-playbook -i hosts termforge.yml --skip-tags iterm2
ansible-playbook -i hosts termforge.yml --tags "neovim,k8s-tools"
```

### Installing Fonts
```bash
ansible-playbook -i hosts nerdfonts.yml -e "font_name=Hack"
```
Then switch your terminal profile to the new font (iTerm2 instructions are in the README).

---

## Takeaways

Forking let me move fast: rename the project, reorganize docs, and add the macOS/Kubernetes-specific pieces without forcing upstream to accept a big opinionated overhaul. If you want:

*   A cross-platform terminal bootstrap,
*   Automatic iTerm2 setup,
*   Neovim ready for Lua/LSP/Copilot,
*   tmux/fzf/oh-my-zsh defaults,
*   Nerd Fonts and dircolors working on macOS,
*   Kubernetes helpers that only appear when you actually use `kubectl`,

…then TermForge is worth a spin.

The code’s on GitHub, the README covers all the details, and you can run the playbook piecemeal or all at once. Happy forging!
