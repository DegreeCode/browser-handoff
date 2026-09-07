---
name: browser-handoff
description: Use the managed Docker cloud browser when the user asks to open, start, activate, share, hand off, or close the cloud browser, Chrome, Chromium, browser session, Docker browser, CDP browser, or noVNC browser. Also use this skill when interactive browser control is required.
---

# Browser Handoff

Use only the managed Docker Chromium session.

## Browser connection

Hermes browser tools must connect to:

http://127.0.0.1:9222

Do not change, remove, or replace `browser.cdp_url`.

Do not launch a separate local Chromium, Chrome, Playwright browser, or browser-use managed browser.

## Start

Before using the interactive browser, check whether CDP is already available:

```bash
curl -fsS http://127.0.0.1:9222/json/version
```

If it is not available, start the managed browser:

```bash
sudo -n /usr/local/bin/hermes-browser-start
```

If a proxy profile is explicitly required:

```bash
sudo -n /usr/local/bin/hermes-browser-start --proxy-profile PROFILE_NAME
```

Do not restart an already-running browser unnecessarily.

## Human handoff

When the user needs to control the current browser session:

```bash
sudo -n /usr/local/bin/hermes-browser-share
```

Give the user the returned noVNC URL and password.

Keep the same browser session running during handoff.

## End handoff

When the user is finished with manual control but wants the browser session preserved:

```bash
sudo -n /usr/local/bin/hermes-browser-unshare
```

This closes only the temporary sharing tunnel.

## Stop browser

Stop the browser only when the user requests it or when the browser session is no longer needed:

```bash
sudo -n /usr/local/bin/hermes-browser-stop
```

Do not start the browser again after stopping it unless a new browser operation actually requires it.

If the user asks to keep the browser or session alive, do not stop it.

## Proxy profiles

List available proxy profiles without exposing credentials:

```bash
sudo -n /usr/local/bin/hermes-browser-proxy list
```

Never display proxy configuration files, passwords, or proxy URLs.

## Security

* Never expose CDP port 9222 publicly.
* Use noVNC only through the temporary handoff tunnel.
* Never expose proxy credentials.

## Completion review

Before reporting that a browser task is complete, perform a final semantic review.

Re-read the user's original request and compare it with the evidence and results obtained during the browser session.

Decide one of:

- COMPLETE: the requested goal has been satisfied with sufficient evidence.
- CONTINUE: the goal is not yet satisfied or completion is uncertain.
- BLOCKED: the goal cannot currently be completed because of an obstacle that requires user action or cannot be resolved safely.

A successful browser command, page load, extraction, or partial result does not by itself mean the user's goal is complete.

If completion is uncertain, continue investigating rather than assuming success.

If blocked, clearly report what was completed, what remains, and what prevents further progress. Do not describe a blocked or partial result as complete.

Do not stop the managed browser as part of normal task cleanup until this completion review has been performed.

After COMPLETE, follow the normal browser stop rules unless the user requested that the session remain open.
