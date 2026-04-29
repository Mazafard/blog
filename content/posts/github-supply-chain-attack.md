---
title: "Uncovering a Massive GitHub Supply Chain Attack: When a Friend's Repo Bites Back"
date: 2026-04-29T00:00:00+00:00
draft: false
tags: ["Security", "GitHub", "Supply Chain", "Malware", "Cybersecurity"]
weight: -1
categories: ["Security", "Programming"]
---

It started like any other day. I was casually reviewing a friend's GitHub repository when a massive, unreadable block of text caught my eye. It was sitting quietly inside a Python file, but the variables were pure gibberish. My cybersecurity spider-sense immediately tingled—this was heavily obfuscated code.

What I didn't know at that moment was that I had just stumbled upon a massive, highly sophisticated supply chain attack infecting hundreds of repositories across GitHub.

Here is the story of how I found it, reverse-engineered it, and how you can protect your own codebases.

## The Suspicious Snippet

The code I found looked like this. It's a classic obfuscation technique: hiding the true intention of the script behind nested layers of encoding and dynamic execution.

```python
# -*- coding: utf-8 -*-
aqgqzxkfjzbdnhz = __import__('base64')
wogyjaaijwqbpxe = __import__('zlib')
idzextbcjbgkdih = 134
qyrrhmmwrhaknyf = lambda dfhulxliqohxamy, osatiehltgdbqxk: bytes([wtqiceobrebqsxl ^ idzextbcjbgkdih for wtqiceobrebqsxl in dfhulxliqohxamy])
lzcdrtfxyqiplpd = 'eNq9W19z3MaRTy......SN' # Massive base64 string truncated
runzmcxgusiurqv = wogyjaaijwqbpxe.decompress(aqgqzxkfjzbdnhz.b64decode(lzcdrtfxyqiplpd))
ycqljtcxxkyiplo = qyrrhmmwrhaknyf(runzmcxgusiurqv, idzextbcjbgkdih)
exec(compile(ycqljtcxxkyiplo, '<>', 'exec'))
```

Looking at the last three lines, the execution flow was clear: 
1. Decode from Base64.
2. Decompress using Zlib.
3. Decrypt using an XOR operation (with the key `134`).
4. Execute the malicious payload directly in memory using the highly dangerous `exec()` function.

## Cracking the Code 

I initially tried to decrypt the payload manually, but dealing with the massive string and the nested operations was getting tedious. So, I spun up an AI assistant (Claude) in an isolated environment and asked it to write a safe "deobfuscator." 

The goal was simple: replace the dangerous `exec()` with a `print()` statement to dump the hidden payload as plain text without actually running it.

Here is the script we used to disarm and extract the payload:

```python
import base64
import zlib

key = 134
# The giant string goes here
payload_base64 = 'eNq9W19z3MaRTy...' 

# Unwrap the layers
decompressed_data = zlib.decompress(base64.b64decode(payload_base64))
decoded_bytes = bytes([b ^ key for b in decompressed_data])
hidden_script = decoded_bytes.decode('utf-8')

print("--------------------------------------------------")
print("🚨 THE HIDDEN PAYLOAD IS: 🚨\n")
print(hidden_script)
print("\n--------------------------------------------------")
```

## The Monster Inside

Running the decoder in a sandbox revealed the true nature of the beast. The resulting Python script was a highly sophisticated **Dropper/Loader** designed to steal information.

*(Note: The full decrypted payload is massive, but here are the key terrifying features it contained)*

1. **Blockchain Command & Control (C2):** Instead of connecting to a traditional, easily blockable IP address, the malware queries the **Solana blockchain**. It looks up the transaction history of a specific wallet and extracts encrypted commands hidden inside the transaction "Memos". This makes taking down the attacker's infrastructure nearly impossible.
2. **Geofencing (The Russian Exception):** The script includes a function called `_isRussianSystem()`. It checks the system's language, timezone, and locale. If the infected machine is located in Russia or CIS countries, the malware quietly exits. This is a classic tactic used by threat actors to avoid the attention of local law enforcement.
3. **Bring Your Own Environment:** The malware silently detects your operating system (Windows, macOS, or Linux) and downloads a portable version of **Node.js** directly from the official website. It then uses this downloaded Node.js to execute a secondary, invisible JavaScript file (likely a stealer like Lumma or RedLine) to siphon passwords, cookies, and crypto wallets.

## The Scale of the Infection

Thinking this might be an isolated incident, I took a snippet of the obfuscated code and searched for it across GitHub. 

The results were chilling. **Over 300 repositories were infected with this exact same code.** After some investigation, it seems this malicious code is being injected directly into files during the `git commit` process. While the exact initial vector (whether it's a compromised VS Code extension, a malicious npm/PyPI package, or a hijacked terminal tool) is still a mystery I am investigating, the outcome is clear: developers are unwittingly pushing malware to their own repositories.

## The Real Danger: Poisoned AI Models

But here's what keeps me up at night: **AI training data.**

Thousands of machine learning engineers and AI researchers are actively scraping GitHub repositories to train their large language models (LLMs), code generation models, and security analysis tools. They're treating open-source code as "free training data." What they don't realize is that they're potentially vacuuming up these malicious, obfuscated snippets and feeding them directly into their neural networks.

Imagine a scenario where an AI model is trained on 300+ infected repositories. The malware code, embedded deep within thousands of legitimate code samples, becomes part of the model's learned patterns. Fast forward to production: developers use this "trained" model to:
- Generate code suggestions (and the model suggests obfuscated malware)
- Analyze security vulnerabilities (but the model itself contains hidden backdoors)
- Validate third-party dependencies (while unknowingly recommending compromised packages)

The nightmare scenario isn't just a poisoned model—it's an undetectable one. The malware lives inside the mathematical weights and biases of the neural network, invisible to any static code analysis. It won't trigger sandboxes or antivirus scanners because it's not "running" code; it's embedded as learned behavior. You'd never find it until the model starts generating malicious suggestions in production, potentially compromising thousands of downstream projects simultaneously.

This is a supply chain attack that transcends repositories and infects the very tools we use to write secure code.

## The Mitigation: A Quick Band-Aid Fix

Until we pinpoint exactly which tool or package is hijacking the commit process, we need a way to stop the bleeding. 

Because this malware relies on injecting a massive, continuous Base64 string into your code, the easiest way to prevent your repo from being infected (and spreading it to others) is to set up a strict **Pre-commit Hook**.

You can block any commit that contains an unnaturally long string (e.g., over 100 characters without spaces). Here is a simple concept for a Git pre-commit hook that you can add to your `.git/hooks/pre-commit` file:

```bash
#!/bin/bash
# A simple pre-commit hook to catch massive base64 injections

if git diff --cached | grep -E '[a-zA-Z0-9+/]{100,}'; then
    echo "🚨 SECURITY ALERT: A suspiciously long string (potential Base64 payload) was detected."
    echo "Commit rejected. Please review your code for injected malware."
    exit 1
fi
```

## Conclusion

Supply chain attacks are getting smarter. They are no longer just targeting production servers; they are living inside our development environments, hijacking our commits, and using decentralized blockchains to hide their tracks. 

Check your repositories, review your dependencies, and if you see a giant block of random letters in your Python files, don't run it. Stay safe out there!
