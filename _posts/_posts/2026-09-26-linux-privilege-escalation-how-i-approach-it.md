---
layout: post
title: "Linux privilege escalation: how I approach it"
---

After I have a low-privilege shell in an authorized lab, I’m not looking for a magic exploit list. I’m looking for places the system (or a higher-privilege process) trusts me — or a file/program I can influence — more than it should.

## What I’m trying to learn first

Before I chase a single vector, I build a picture:

- **Who am I?** uid/groups — especially anything that implies admin-adjacent trust (sudo, docker, and similar).
- **What is this box?** OS/distro and rough kernel age — enough to know the platform, not a CVE scavenger hunt.
- **What’s running?** processes and services that might be more privileged than my shell.
- **What’s listening locally?** services on localhost that the outside world never sees.
- **What’s oddly writable or oddly privileged?** files and programs whose permissions don’t match a locked-down baseline.

I use tools when they speed that picture up. I don’t start by pasting an automated dump I don’t understand.

## How I think about sudo

`sudo -l` answers a trust question: which commands may I run as a higher user, and under what rules (password required or not, arguments restricted or not)?

If a listing looks dangerous, my report language is: *this account can invoke X as root (or another user) under these conditions; that expands trust beyond a normal user shell; remove the right or replace it with a safer path.*

I don’t treat every sudo line as an instant win. I treat it as a trust boundary I have to explain.

## How I think about SUID (and similar)

A SUID binary runs with the **owner’s** privileges when I execute it. The trust question is: should a normal user be able to run this program as that owner?

Same shape for other “runs with extra power” patterns I meet in labs (SGID, some capabilities, cron jobs that run as root but depend on a file I can change). I’m asking: *who intended this trust, and is user input or a writable path inside that trust boundary?*

Fix language stays boring on purpose: remove the bit, don’t ship the tool that way, or stop the privileged job from depending on a user-writable file.

## My loop on the next box

1. Confirm who I am and what groups I land in  
2. Sketch OS, processes, local listeners  
3. Look for trust edges: sudo rules, odd privileged programs, privileged scheduled tasks tied to writable paths  
4. Prove impact only inside the lab rules  
5. Write it so a lead understands the trust failure — not so someone can replay a recipe  

## Automated tools (where they fit)

LinPEAS-style scripts are useful **after** I know what “weird” would look like, or when I want a second pass for things I missed. They are not the methodology. If I can’t explain a highlight in plain words, I haven’t learned it yet.

Authorized lab practice. This is how I think, not a client attack cookbook.
