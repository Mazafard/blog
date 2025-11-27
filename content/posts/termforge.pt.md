---
title: "TermForge: Modernizando meu Workflow no Terminal"
date: 2025-11-27T00:00:00+00:00
draft: false
tags: ["Terminal", "Ansible", "DevOps", "macOS", "Kubernetes", "Neovim"]
weight: -8
categories: ["Ferramentas", "DevOps"]
---

Eu sempre curti o projeto original [jazik/termenv](https://github.com/jazik/termenv) — é um playbook Ansible limpo que faz o bootstrap de um ambiente de terminal excelente. Mas depois de viver nele por um tempo, me peguei querendo um suporte mais firme ao macOS, provisionamento automático do iTerm2 e algumas ferramentas focadas em Kubernetes. Decidi fazer um fork do projeto e construir essas peças extras. O resultado é o **TermForge**, minha versão renovada da ideia de "terminal numa caixa".

## Por que Fork ao invés de PR?

Inicialmente, explorei melhorias incrementais no repositório upstream. Mas bem rápido as mudanças viraram uma bola de neve — novos playbooks, uma mudança de nome opcional, roles exclusivas para macOS, reescrita da documentação e ajudantes de Kubernetes que modificam vários arquivos. Em vez de bagunçar o escopo do original, fiz um fork e abracei uma nova identidade para o projeto, mantendo os créditos do trabalho original.

---

## Destaques das Funcionalidades

### 1. Stack de Shell Multiplataforma
*   **Linux**: ainda usa gerenciadores de pacotes nativos com `sudo`.
*   **macOS**: agora se apoia no Homebrew/Homebrew Cask para `zsh`, `tmux`, fontes, Node e iTerm2.
*   **Detecção automática do zsh**: resolve o caminho do `zsh` instalado (`/opt/homebrew/bin/zsh` no Apple Silicon) antes de torná-lo o shell de login e o adiciona ao `/etc/shells`.
*   **dircolors no macOS**: Homebrew `coreutils` + um bloco de alias garantem que o `dircolors` funcione, mesmo que o macOS não venha com ele.

**Experimente:**
```bash
ansible-playbook -i hosts --ask-become-pass termforge.yml --tags "zsh,zsh-dircolors-solarized"
```

### 2. Provisionamento do iTerm2 (macOS)
*   Instala o iTerm2 via Homebrew Cask quando ele ainda não está presente.
*   Pula automaticamente se `/Applications/iTerm.app` existir (sem erros se você já instalou manualmente).

**Mire apenas nessa role:**
```bash
ansible-playbook -i hosts --ask-become-pass termforge.yml --tags iterm2
```

### 3. Fontes + Polimento de UI
*   O downloader de Nerd Fonts instala Fira Mono por padrão (ou qualquer arquivo que você especificar).
*   No macOS, as fontes caem em `~/Library/Fonts/<Font>`, então aparecem instantaneamente sem precisar de `fc-cache`.
*   Instruções pós-instalação do iTerm2 no README mostram como selecionar a Nerd Font, habilitar ligaduras ou apontar o iTerm2 para sua pasta de sincronização.

### 4. Stack Neovim (Lua, Telescope, Treesitter, Copilot Opcional)
*   Instala Neovim mais ripgrep para o Telescope.
*   Copia uma config Lua pronta para uso (keymaps, plugins, LSP).
*   Playbook opcional `neovim-copilot.yml` instala Copilot/CopilotChat depois.
*   Role CSpell garante Node/npm, instala `cspell`, solta a config e injeta keymaps para diagnósticos.

**Instale o Copilot depois:**
```bash
ansible-playbook -i hosts neovim-copilot.yml
```

### 5. Ajudantes Kubernetes
*   Nova role `kubernetes-tools` checa se o `kubectl` já está no seu `PATH`.
*   Se sim:
    *   **macOS**: instala `k9s` e `kubectx` via Homebrew (kubectx traz o `kubens`, então sem fórmula extra).
    *   **Linux**: baixa o tarball binário do k9s, instala em `/usr/local/bin`, clona o repo do kubectx e cria symlinks para `kubectx` e `kubens`.
*   Se o `kubectl` não estiver presente, a role inteira pula silenciosamente — sem inchaço até você realmente trabalhar com clusters.

**Force apenas a role de ajudantes:**
```bash
ansible-playbook -i hosts termforge.yml --tags k8s-tools
```

### 6. Ajustes de Qualidade de Vida
*   Instalação do `fzf` inclui a ligação com plugin do oh-my-zsh mais opções padrão (`--height 40% --layout=reverse --border`).
*   Role do `tmux` instala o tema, define `base-index 1` e adiciona bindings de navegação inteligente Vim/Tmux.
*   O README agora documenta:
    *   Pré-requisitos específicos de macOS (instalação do Homebrew, aviso de interpretador, como definir `ANSIBLE_PYTHON_INTERPRETER`).
    *   Uso de tags e combinações de roles.
    *   Setups de teste Docker + Vagrant com instruções atualizadas para o renomeado `termforge.yml`.
    *   Um cheat sheet de Neovim atualizado e screenshots.

---

## Visão Geral de Uso

### Clone + Run
```bash
git clone https://github.com/Mazafard/TermForge.git
cd TermForge
ansible-playbook -i hosts --ask-become-pass termforge.yml
```
*   `termenv.yml` ainda existe, mas agora apenas importa `termforge.yml`, então scripts legados continuam funcionando.

### Customize com Tags
Exemplos:
```bash
ansible-playbook -i hosts termforge.yml --skip-tags iterm2
ansible-playbook -i hosts termforge.yml --tags "neovim,k8s-tools"
```

### Instalando Fontes
```bash
ansible-playbook -i hosts nerdfonts.yml -e "font_name=Hack"
```
Depois mude o perfil do seu terminal para a nova fonte (instruções do iTerm2 estão no README).

---

## Conclusões

Fazer o fork me permitiu ser rápido: renomear o projeto, reorganizar a documentação e adicionar as peças específicas de macOS/Kubernetes sem forçar o upstream a aceitar uma grande reforma opinativa. Se você quer:

*   Um bootstrap de terminal multiplataforma,
*   Setup automático do iTerm2,
*   Neovim pronto para Lua/LSP/Copilot,
*   Padrões de tmux/fzf/oh-my-zsh,
*   Nerd Fonts e dircolors funcionando no macOS,
*   Ajudantes de Kubernetes que só aparecem quando você realmente usa `kubectl`,

…então o TermForge vale uma testada.

O código está no [GitHub](https://github.com/Mazafard/TermForge), o README cobre todos os detalhes, e você pode rodar o playbook em pedaços ou tudo de uma vez. Happy forging!
