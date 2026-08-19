---
title: "Bringing Back the 1971 Social Network: A Quick Guide to Finger"
date: 2026-08-19T00:00:00+00:00
draft: false
tags: ["Networking", "Unix", "Self-Hosting", "History", "Linux"]
weight: -11
categories: ["Technology", "Programming"]
cover:
  image: "/images/finger-status.jpg"
  alt: "Finger terminal status example"
  caption: "A minimal Finger status page in plain text"
---

Long before algorithmic feeds, engagement metrics, and push notifications, the internet had a radically simple way to see what people were up to: Finger.

Originally conceived in 1971 at Stanford and formalized under RFC 742 (and later RFC 1288), Finger is a minimalist, plain-text networking protocol running over TCP port 79. When you query a user via `finger user@host`, the remote machine returns status metadata alongside the contents of a simple text file: `.plan`.

Legendary developers like John Carmack used their `.plan` files throughout the 1990s as raw, transparent devlogs. If you appreciate the IndieWeb, decentralized protocols, or sheer Unix minimalism, running your own Finger presence is a fun project that takes only a few minutes.

## What Finger Was Actually Built For (Beyond the Microblog)

While tech culture remembers Finger fondly as a proto-microblog, its formal definition under **RFC 742** and **RFC 1288** is the **Name/Finger User Information Protocol**.

It wasn't designed as a social media platform; it was built as a remote presence and directory query system. Prior to Finger, systems like WAITS only offered the rudimentary `WHO` command, which dumped cryptic terminal line IDs. Finger humanized system status queries by returning:

- Full user names and email addresses.
- Terminal idle times and physical office locations.
- Presence data to check if someone was physically available in the building.

## Why It Almost Disappeared: Privacy & Social Engineering

In the trust-based internet of the 1970s and 80s, openly broadcasting user metadata was seen as a convenience. Over time, it became a major security vector:

1. **Reconnaissance & Social Engineering:** Attackers queried company servers via Finger to extract complete employee rosters, real names, and email patterns. This intelligence formed the backbone of targeted impersonation and corporate phishing attacks.
2. **Buffer Overflows:** As famously demonstrated by the 1988 Morris Worm, early `fingerd` implementations suffered from memory vulnerabilities that allowed remote arbitrary code execution.

By the late 1990s, system administrators globally disabled TCP port 79 to plug these reconnaissance holes, turning Finger from a core network utility into a niche hacker artifact.

<p align="center">
  <img src="/images/finger-status.jpg" alt="Example Finger status page with a minimal terminal-style profile" width="900" />
</p>

## Method 1: The Zero-Setup Hosted Route (Happy Net Box)

If you don't want to manage a server or open firewall ports, community directories like `happynetbox.com` provide a web UI that bridges into the Finger network.

1. Create a free account at `happynetbox.com`.
2. Paste your plain-text status, ASCII art, or daily log into the browser editor.
3. Save your changes.

Anyone around the world can immediately check your status from their local terminal:

```bash
finger mazafard@happynetbox.com
```

## Method 2: Self-Hosting on an Ubuntu Server

If you have your own Ubuntu machine (a VPS, cloud instance, or home server), you can run a native Finger daemon using modern, secure tooling.

### Step 1: Install `openbsd-inetd` and `ffingerd`

The original `fingerd` had security flaws in the 1980s. On modern Debian/Ubuntu systems, use `ffingerd`—a secure, drop-in replacement designed specifically for public-facing servers.

```bash
sudo apt update
sudo apt install openbsd-inetd ffingerd finger -y
```

### Step 2: Configure the Super-Server

Open the super-server configuration:

```bash
sudo nano /etc/inetd.conf
```

Ensure the following line is present (or replace the existing finger entry):

```text
finger stream tcp nowait nobody /usr/sbin/tcpd /usr/sbin/ffingerd
```

Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`), then reload the service:

```bash
sudo systemctl restart openbsd-inetd
```

### Step 3: Create Your `.plan` File

Create a `.plan` file in your user's home directory and ensure it has worldwide read permissions so the `nobody` user can serve it:

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

### Step 4: Open Port 79 in the Firewall

Finger operates on TCP port 79. Allow traffic through UFW:

```bash
sudo ufw allow 79/tcp
sudo ufw reload
```

> If your server is hosted on a cloud provider like Hetzner, AWS, or DigitalOcean, ensure port 79 TCP is also allowed in their security group or cloud firewall settings.

### Step 5: Test Your Setup

From your local laptop or another remote terminal, run:

```bash
finger yourusername@your-server-ip
```

If you map a standard DNS A record (for example, `finger.yourdomain.com`) pointing directly to your server IP, you can query it via your domain:

```bash
finger yourusername@finger.yourdomain.com
```

## Why Run Finger Today?

Finger is not trying to replace modern communications. It is an artifact of computing history that remains fully functional: text-based, self-owned, free of tracking, and refreshingly direct.

That combination is hard to ignore. It is a tiny, honest protocol that reminds us that the internet once felt more personal, more local, and less optimized for attention.

If you want a small project with a lot of personality, Finger is a perfect way to bring a little of that old internet back into the present.
