# Secrets in Source: View Source Code to Find the Flag  CTF Write-up

**Lab:** [Secrets in Source](https://hackerdna.com/labs/secrets-in-source)
**Platform:** HackerDNA
**Difficulty:** Very Easy
**Category:** Web Security
**Date:** November 1, 2026
**Flags:** 1

*This was done against an authorized HackerDNA lab instance, not a real production system.*

## First impressions

This lab drops you on a basic landing page for a fictional company. Nothing obviously interesting on the surface  no broken links, no login form, nothing to click. The hint is in the lab's own title: the answer isn't on the page, it's in the page.

## Checking the source

Whenever a page looks this plain, the first move is to check what's actually being sent in the HTML, not just what's rendered. Opened the page source with `Ctrl+U`.

## Finding the flag path

Scrolling through the raw HTML, there's a developer comment sitting near the bottom that was clearly never meant to stay in a shipped page  something like a TODO note mentioning the flag's file path and saying it should be moved somewhere more secure "later." Classic case of a reminder-to-self that never got acted on and ended up shipping straight to production.

```html
<!-- TODO: move the flag "[path redacted]" in a more secure location -->
```

## Retrieving the flag

The comment gives you the exact path. Appending that path to the lab's base URL and navigating there directly returns the flag with no authentication, no access control, nothing standing in the way:

```
[flag redacted]
```

## Vulnerability Breakdown

| Vuln | Root Cause | Impact | Fix |
|---|---|---|---|
| **Sensitive Information Disclosure via HTML comments** | A developer left a TODO comment in the page's HTML containing the exact path to a sensitive file | Anyone viewing the page source (a single keystroke away) could read the comment and retrieve the file directly, with no authentication required | Strip developer comments and debug notes before deploying to production; never store sensitive paths, credentials, or notes in client-delivered code |
| **Missing access control on the exposed endpoint** | The flag file itself had no authentication or authorization check  anyone with the URL could fetch it | Full access to the protected resource for any unauthenticated visitor who found (or guessed) the path | Any resource meant to be restricted needs a real server-side access control check, not just an unguessable-sounding path |

## Lessons Learned

- This is basically the textbook example of why "security through obscurity" doesn't work  a random-looking folder name isn't protection if the path itself is written down in a comment right next to it.
- Checking page source should be step one on literally any web challenge, even ones that look like nothing's going on. The interesting stuff is often not rendered on the page at all.
- Dev comments and TODOs are exactly the kind of thing that should get cleaned up before anything ships  it's such a small thing to forget and such an easy win for anyone looking.

## Attack Chain Summary

```
Landing page with nothing visibly interesting
   → viewed page source (Ctrl+U)
   → found a dev TODO comment exposing the flag's file path
   → navigated directly to that path
   → no auth required
   → FLAG
```

Lab: [https://hackerdna.com/labs/secrets-in-source](https://hackerdna.com/labs/secrets-in-source)
