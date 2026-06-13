# Shadowrocket Rules

Public Shadowrocket smart split routing config for macOS, with Apple Software Update routed directly.

## Problem

During a macOS 27 Beta OTA update, the download succeeded through Shadowrocket, but the prepare phase failed with:

```text
Failed to prepare the software update. Please try again.
MSU_ERR_GLOBAL_TICKET_INVALID(53)
```

The local diagnosis showed Software Update traffic was routed through a Shadowrocket Network Extension tunnel and local HTTP/HTTPS proxy at `127.0.0.1:1082`. Apple update personalization and signing endpoints are sensitive to routing, cache, TLS interception, and regional ticket generation. Keeping these domains direct is safer than routing the whole Apple update path through a proxy.

## File

- `shadowrocket-smart-split.conf`: the single public config template.

## Recommended Fix

Use `shadowrocket-smart-split.conf` directly, or copy its Apple section into your own Shadowrocket config. Keep the order:

1. Apple Private Relay proxy rules, if you intentionally proxy Private Relay.
2. Apple Software Update, beta enrollment, recovery, and certificate validation direct rules.
3. General Apple direct rules such as `apple.com`, `icloud.com`, and `mzstatic.com`.
4. China GeoIP and final fallback rules at the end.

This order matters because Shadowrocket rules are evaluated top to bottom. A broad `apple.com,DIRECT` rule placed too early can make a later `apple-relay.apple.com,PROXY` rule unreachable.

## Verify on macOS

Check whether Shadowrocket is still acting as the primary VPN/proxy path:

```sh
scutil --nc status Shadowrocket
scutil --proxy
scutil --dns | grep -E 'nameserver|if_index'
```

For the safest macOS beta install attempt, keep Shadowrocket disconnected or make sure the Apple update domains above are direct. Then retry from System Settings > Software Update.

## Notes

- This repository intentionally does not include proxy nodes, subscription URLs, passwords, tokens, or account-specific values.
- If your proxy policy group is not named `PROXY`, replace `,PROXY` with your actual policy group name.
- Apple Private Relay domains are kept on `PROXY` in the template because the original local policy intentionally did that.

## Sources

- Apple Support: https://support.apple.com/en-us/101555
- Apple Platform Deployment: https://support.apple.com/guide/deployment/about-software-updates-depc4c80847a/web
