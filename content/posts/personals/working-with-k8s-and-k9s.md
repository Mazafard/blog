---
author: ["Maza Fard"]
date: '2025-11-22'
description: A quick chat about Kubernetes (K8s) and K9s, what they are, and why they're awesome.
tags: ["kubernetes", "k8s", "k9s", "devops", "tools"]
categories: ["technology", "devops"]
series: ["devops tools"]
ShowToc: true
weight: 2
title: Working with K8s and K9s
---

## What's the deal with Kubernetes (K8s)?

So, let's talk about Kubernetes, or K8s as everyone calls it. Think of it as the captain of your container ship. It's basically this massive open-source platform that handles all the messy stuff for you—deploying, scaling, and managing your containerized apps. Google started it way back when, and now it's pretty much the standard everywhere.

### Why it's cool

1.  **It Scales:** Need more power because your app is going viral? K8s spins up more instances automatically. Traffic died down? It scales back to save you money. Super easy.
2.  **It Heals Itself:** This is my favorite part. If a container crashes, K8s just restarts it. If a whole server (node) dies, it moves your stuff to a healthy one. It's like having a self-repairing system.
3.  **Run Anywhere:** Whether you're on your laptop, in the cloud, or on some dusty server in a basement, K8s doesn't care. It runs where you need it.
4.  **Huge Community:** Everyone uses it, so there are tons of tools and plugins to make it do whatever you want.

## And what about K9s?

Okay, so `kubectl` is great and all, but typing long commands all day gets old fast. Enter K9s. It's a terminal-based UI that makes navigating your clusters feel a bit like a video game. It watches your cluster in real-time and lets you interact with everything using simple keyboard shortcuts.

### Why you'll love K9s

1.  **Super Fast:** No more typing `kubectl get pods -n my-namespace` fifty times a day. Just navigate with arrow keys and press Enter.
2.  **Real-time Action:** You see what's happening as it happens. Pod crashing? You'll see it turn red instantly.
3.  **Keyboard Ninja:** Once you learn the hotkeys, you'll be flying through your resources. It feels really slick.
4.  **Easy Management:** Want to see logs? Press `l`. Want a shell inside a pod? Press `s`. Port-forwarding? `shift-f`. It's that simple.

## Quick Start

Want to see it in action? Here are a few commands to get you rolling.

First, check what's running with `kubectl`:

```bash
# List all pods in the current namespace
kubectl get pods

# Check the services
kubectl get svc
```

Now, fire up `k9s` (assuming you've installed it):

```bash
k9s
```

Boom! You're in the matrix. Use your arrow keys to move around, and press `?` to see all the cool shortcuts.

## Conclusion

Honestly, K8s is a beast for managing apps, but K9s is what makes it actually fun to work with. It saves so much time and headache. If you haven't tried this combo yet, definitely give it a shot!
