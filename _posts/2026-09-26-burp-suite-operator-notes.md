---
layout: post
title: "Burp Suite in a web test: how I actually use it"
---

In an authorized web test, the useful evidence is often in the HTTP conversation, not in what the page renders. Burp Suite is the industry-standard proxy I use to see that conversation, change one thing on purpose, and judge what the server did next. I wrote this after the TryHackMe Burp Suite room. The point is how I make decisions with the tool, not a tour of every button.

## What Burp is for

Burp sits between my browser and the app and captures HTTP/HTTPS. I can view and modify requests before they hit the server, inspect responses, and send interesting requests into other Burp tools. That matters to me because trust decisions — cookies, tokens, parameters, methods — live in those messages.

## Proxy

Proxy is where I capture traffic between me and the target. With intercept on, I can forward a request, drop it, or send it to Repeater or Intruder. With intercept off, history still fills so I can pull requests later.

For recon I read method, path, parameters, cookies, and auth-related headers. I want to understand how the app is spoken to before I start changing things.

## Repeater

I send a logged request to Repeater when I want a clean A/B test: I change one parameter, header, or method, send it, and compare the response to the last one.

Repeater talks to the real target. It is not a sandbox and it is not stealth. I use Proxy to catch live browser traffic. I use Repeater for deliberate follow-up on a single request under my control.

## Intruder

I use Intruder to send many similar requests with marked positions filled from payload lists. I only reach for it after I understand the request and I need controlled volume — not as my first click. In the labs that looked like trying many values against a login field or fuzzing a parameter. The skill I’m practicing is matching the attack type to the question I’m asking.

### Attack types

**Sniper** — one list into one position; everything else stays fixed.  
I use it when one field is the variable (for example a known username and many password candidates in a lab).

**Battering ram** — the same payload value goes into every marked position at once.  
I use it when I’m testing the same string in multiple places (for example username and password both set to the same candidate).

**Pitchfork** — lists advance together row by row (line 1 with line 1).  
I use it when I have paired values to try together.

**Cluster bomb** — every combination across lists.  
I use it when I need the full cross product and I accept that the request count grows fast.

## Reading responses

I sort on signals instead of reading every row:

- **Status** — identical codes across almost all results are often noise. Outliers matter to me: a redirect after a login attempt, 401/403 vs 200, a sudden 429, unexpected 500s.
- **Length** — I find the common size (baseline), hide it, then inspect big jumps or clusters of the same non-baseline size.
- **Time** — normal jitter is noise. If responses are consistently much slower than baseline, the server waited or did something expensive. I treat that as a clue to investigate carefully in scope.
- **Body** — success vs failure strings, unexpected error text, obvious structural change.
- **Headers / cookies** — a new `Set-Cookie`, or a `Location` that clearly differs from the failure path.

My practical order: sort by length, then status, then grep for a few known strings.

## How I work a request

1. I catch it in Proxy / history  
2. I understand method, path, params, cookies  
3. I use Repeater first if one change answers the question  
4. I use Intruder only when I need controlled volume — and I pick the mode that matches the question  
5. I sort responses by length/status before reading bodies  

## Mistakes I corrected

- Treating Repeater as offline or stealth (it hits the real server)  
- Defaulting to Cluster Bomb when Sniper or Pitchfork matched the question  
- Opening every response instead of finding baseline length/status first  

## What I’d say in an interview

Burp is how I see and control HTTP in a web test. Repeater is my one-change comparison tool. Intruder is disciplined variation with a mode that matches the question. I decide what mattered by response diffs — status, length, body, headers — not by the page looking slightly weird.

Authorized lab practice only. Not a client cookbook.
