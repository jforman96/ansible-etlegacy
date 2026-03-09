# ETLegacy Base — Ansible Role

Deploys a clean, modern ETLegacy dedicated server.  
No legacy folder structure, no manual setup, fully idempotent.

## What this role does

- Creates system user/group and directory layout
- Downloads ETLegacy server binaries
- Extracts original Wolf:ET pak files (pak0–pak2) automatically
- Deploys a tuned `server.cfg` from template
- Deploys a systemd service with restart and journal logging

## Directory layout

```
/opt/etlegacy/
  etmain/        # base game pak files (pak0–pak2)
  legacy/        # mod files (ETLegacy + custom packs)
  home/
  home/legacy/   # server.cfg, logs, runtime data
```

## Usage

```yaml
- hosts: etlegacy
  become: yes
  roles:
    - etlegacy_base
    - etlegacy_maps   # optional: custom maps
    - etlegacy_mods   # optional: sound/mod packs
```

## Key variables

| Variable | Default | Description |
|---|---|---|
| `etlegacy_version` | `2.83.2` | Informational — update `etlegacy_download_url` when upgrading |
| `etlegacy_download_url` | etlegacy.com file 700 | Direct download link |
| `etlegacy_hostname` | `ET:Legacy Server` | Server name shown in browser |
| `etlegacy_password` | `""` | Empty = public server, set for private |
| `etlegacy_rcon_password` | `<CHANGE_ME>` | Remote console password |
| `etlegacy_www_baseurl` | `""` | HTTP base URL for fast downloads |
| `etlegacy_www_fallbackurl` | `""` | Fallback if fast download fails |
| `etlegacy_advert` | `1` | `0` = hide from public master server browser |
| `etlegacy_maxclients` | `20` | Max simultaneous players |
| `etlegacy_map` | `battery` | Starting map |
| `etlegacy_gametype` | `6` | 6 = Campaign |
| `etlegacy_min_map_age` | `1` | Prevent same map vote immediately |
| `etlegacy_voice_chats_allowed` | `5` | Voice lines per round |

## Notes

- Fast download requires an HTTP server (ET engine does not support HTTPS)
- Secrets (`etlegacy_password`, `etlegacy_rcon_password`) should be in a private vars file or Ansible Vault
- The `g_password` cvar is only written to server.cfg when `etlegacy_password` is non-empty
