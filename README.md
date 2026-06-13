# Shadowrocket Rules

Public Shadowrocket smart split routing config for macOS.

## Overview

This repository keeps a single reusable Shadowrocket profile:

- `shadowrocket-smart-split.conf`

It is designed for a practical split setup: local networks and common China services go direct, selected global services go through `PROXY`, and the final fallback is `PROXY`.

## Rule Layout

The config is ordered deliberately:

1. Reject obvious ad and tracking domains.
2. Keep local and private networks direct.
3. Keep DNS bootstrap domains direct.
4. Apply Apple-specific direct/proxy exceptions.
5. Route selected global services through `PROXY`.
6. Keep mainland China and common local services direct.
7. End with `GEOIP,CN,DIRECT` and `FINAL,PROXY`.

Rule order matters because Shadowrocket evaluates from top to bottom. More specific rules should appear before broad suffix rules.

## Validate on macOS

Check whether Shadowrocket is still acting as the primary VPN/proxy path:

```sh
scutil --nc status Shadowrocket
scutil --proxy
scutil --dns | grep -E 'nameserver|if_index'
```

## Notes

- This repository intentionally does not include proxy nodes, subscription URLs, passwords, tokens, or account-specific values.
- If your proxy policy group is not named `PROXY`, replace `,PROXY` with your actual policy group name.
- Apple Private Relay domains are kept on `PROXY` in this template. Change those rules to `DIRECT` if that better matches your policy.

## Sources

- Apple Support: https://support.apple.com/en-us/101555
- Apple Platform Deployment: https://support.apple.com/guide/deployment/about-software-updates-depc4c80847a/web
