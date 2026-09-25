# TryHackMe: Checkmate

Checkmate is a password-security CTF built around an admin who reused weak, pattern-based passwords across a firewall console, employee portal, social app, and SSH. I treated it as an authentication review: find where secrets were guessable or derivable, then prove impact without spraying everything blindly.

## What the systems trusted

They trusted that “default,” company-flavored, and personal passwords were secret enough. They trusted that public pages and a social profile would not leak enough material to build a better wordlist. They trusted online logins (and later SSH) would keep answering guesses. Port 5000 was rate-limited; the real assessment surface was the services on 5001–5003 plus SSH — scope mattered.

## How I noticed it

Wrong-password responses and a clear login form told me online guessing was in play on the firewall portal. The employee site was content-rich, so a site-derived list beat a generic dump. HR-style personal fields on the portal were enough to build a personal list for the social login. On the social profile, the image and posts were not just flavor — they carried recoverable material (including a hash tied to the picture name, and a post that telegraphed password habits). Each stage reused the same mistake: secrets tied to something an outsider could collect.

## Approach (high level)

I confirmed request method and failure text before aiming any password tool — wrong path or wrong failure string wastes the list. Defaults and common pairs for the first service. For the employee portal, a custom list from the site’s own language. For social, a personal list from details the org had already centralized. For the image-backed secret, identify and recover the name from the hash rather than guessing in the dark. For SSH, a short list shaped by his stated password habits — not a raw rockyou marathon. When a tool “failed” earlier in the week on other labs, it was usually aim (path / fail string), not magic.

## What I’d tell a client to fix

Ban default and shared passwords. Enforce unique secrets and a password manager. Add lockout or throttling and MFA on internet-facing auth, including SSH where possible. Stop putting recoverable secrets in public assets or profile media. Train people that “clever personal patterns” are still public once HR and social overlap. Run periodic password audits with the same discipline: targeted lists, clear scope, logged attempts.

## What I’d get faster at next time

Building the right small list before launching a long spray. Reading failure pages and scope notes first. Treating profile media and “helpful” password posts as credential material, not lore.

## Tools touched

Hydra (online guessing), CeWL (site-derived words), CUPP (personal lists), Hashcat (recovering material from a hash). The point was list quality and aim — not the tool logo.
