# Shadowrocket Rules

Public Shadowrocket rule templates for smart split routing on macOS, including direct rules for Apple Software Update.

## Problem

During a macOS 27 Beta OTA update, the download succeeded through Shadowrocket, but the prepare phase failed with:

```text
Failed to prepare the software update. Please try again.
MSU_ERR_GLOBAL_TICKET_INVALID(53)
```

The local diagnosis showed Software Update traffic was routed through a Shadowrocket Network Extension tunnel and local HTTP/HTTPS proxy at `127.0.0.1:1082`. Apple update personalization and signing endpoints are sensitive to routing, cache, TLS interception, and regional ticket generation. Keeping these domains direct is safer than routing the whole Apple update path through a proxy.

## Files

- `shadowrocket-smart-split.conf`: full public template based on the local configuration.
- `apple-software-update-direct.rules`: small drop-in rule block for existing configs.

## Recommended Fix

Place the Apple Software Update direct rules before broad Apple, China GeoIP, and final fallback rules:

```text
DOMAIN,gs.apple.com,DIRECT
DOMAIN,gdmf.apple.com,DIRECT
DOMAIN,mesu.apple.com,DIRECT
DOMAIN,xp.apple.com,DIRECT
DOMAIN,ocsp.apple.com,DIRECT
DOMAIN,valid.apple.com,DIRECT
DOMAIN-SUFFIX,swcdn.apple.com,DIRECT
DOMAIN-SUFFIX,swdownload.apple.com,DIRECT
DOMAIN-SUFFIX,swdist.apple.com,DIRECT
DOMAIN-SUFFIX,swscan.apple.com,DIRECT
DOMAIN-SUFFIX,updates.cdn-apple.com,DIRECT
DOMAIN-SUFFIX,oscdn.apple.com,DIRECT
DOMAIN-SUFFIX,mzstatic.com,DIRECT
DOMAIN-SUFFIX,itunes.apple.com,DIRECT
DOMAIN-SUFFIX,apple.com,DIRECT
DOMAIN-SUFFIX,icloud.com,DIRECT
DOMAIN-SUFFIX,icloud-content.com,DIRECT
```

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
- Apple Private Relay domains are kept on `PROXY` in the full template because the original local policy intentionally did that.
