---
title: "O Regresso à Rede Social de 1971: Guia Rápido para o Finger"
date: 2026-08-19T00:00:00+00:00
draft: false
tags: ["Networking", "Unix", "Self-Hosting", "History", "Linux"]
weight: -11
categories: ["Technology", "Programming"]
cover:
  image: "/images/finger-status.jpg"
  alt: "Exemplo de página de estado do Finger"
  caption: "Uma página de estado minimalista do Finger em texto simples"
---

Muito antes dos feeds com algoritmos, métricas de engagement e notificações constantes, a Internet tinha uma forma incrivelmente simples de ver o que as pessoas estavam a fazer: o Finger.

Criado originalmente em 1971 em Stanford e formalizado pelo RFC 742 (e mais tarde pelo RFC 1288), o Finger é um protocolo minimalista em texto simples que corre sobre a porta TCP 79. Quando consulta um utilizador com `finger user@host`, a máquina remota devolve metadados de estado juntamente com o conteúdo de um ficheiro simples chamado `.plan`.

Programadores lendários como John Carmack usaram os seus ficheiros `.plan` ao longo dos anos 90 como diários de desenvolvimento transparentes e crus. Se aprecia a IndieWeb, protocolos descentralizados ou a minimalismo puro do Unix, ter a sua própria presença no Finger é um projeto divertido que demora apenas alguns minutos.

<p align="center">
  <img src="/images/finger-status.jpg" alt="Exemplo de página de estado do Finger com um visual mínimo em terminal" width="900" />
</p>

## Método 1: A Opção Sem Servidor (Happy Net Box)

Se não quiser gerir um servidor nem abrir portas na firewall, diretórios comunitários como `happynetbox.com` oferecem uma interface web que comunica com a rede Finger.

1. Crie uma conta gratuita em `happynetbox.com`.
2. Cole o seu estado em texto simples, arte ASCII ou diário diário no editor do navegador.
3. Guarde as alterações.

Qualquer pessoa no mundo pode consultar o seu estado a partir do terminal local:

```bash
finger mazafard@happynetbox.com
```

## Método 2: Alojamento Próprio num Servidor Ubuntu

Se tiver o seu próprio servidor Ubuntu (VPS, instância cloud ou servidor em casa), pode executar um daemon nativo de Finger com ferramentas modernas e seguras.

### Passo 1: Instalar o `openbsd-inetd` e o `ffingerd`

O `fingerd` original tinha falhas de segurança nos anos 80. Em sistemas modernos Debian/Ubuntu, use o `ffingerd`: uma alternativa segura e compatível, concebida especificamente para servidores públicos.

```bash
sudo apt update
sudo apt install openbsd-inetd ffingerd finger -y
```

### Passo 2: Configurar o Super-Server

Abra a configuração do super-server:

```bash
sudo nano /etc/inetd.conf
```

Certifique-se de que a linha seguinte está presente (ou substitua a entrada atual do finger):

```text
finger stream tcp nowait nobody /usr/sbin/tcpd /usr/sbin/ffingerd
```

Guarde e saia (`Ctrl+O`, `Enter`, `Ctrl+X`) e depois reinicie o serviço:

```bash
sudo systemctl restart openbsd-inetd
```

### Passo 3: Criar o Ficheiro `.plan`

Crie um ficheiro `.plan` na pasta pessoal do utilizador e garanta permissões de leitura pública para que o utilizador `nobody` possa servi-lo:

```bash
cat <<'EOF' > ~/.plan
======================================================
  mazafard's terminal space
======================================================

--- 2026-08-19 ---
Serving plain text over port 79.
Minimalism over algorithms.

* Blog: https://blog.fard.pt
* X   : https://x.com/mazafard
EOF

chmod 644 ~/.plan
```

### Passo 4: Abrir a Porta 79 na Firewall

O Finger comunica através da porta TCP 79. Permita o tráfego no UFW:

```bash
sudo ufw allow 79/tcp
sudo ufw reload
```

> Se o seu servidor estiver alojado numa cloud como Hetzner, AWS ou DigitalOcean, confirme também que a porta TCP 79 está desbloqueada na firewall da plataforma.

### Passo 5: Testar a Configuração

A partir do seu portátil ou de outro terminal remoto, execute:

```bash
finger yourusername@your-server-ip
```

Se mapear um registo DNS A simples (por exemplo, `finger.oseudominio.pt`) para o IP do seu servidor, pode consultar o serviço também pelo domínio:

```bash
finger yourusername@finger.oseudominio.pt
```

## Porque Usar Finger Hoje?

O Finger não pretende substituir as comunicações modernas. É um artefacto da história da informática que continua totalmente funcional: baseado em texto, próprio do utilizador, sem tracking e extremamente direto.

Essa combinação é rara e valiosa. O Finger é um protocolo pequeno, honesto e cheio de personalidade, que lembra que a Internet já foi mais pessoal, mais local e menos otimizada para a atenção.

Se procura um projeto pequeno com muito carácter, o Finger é uma forma excelente de trazer um pouco dessa Internet antiga para o presente.
