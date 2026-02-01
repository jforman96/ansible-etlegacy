# ETLegacy Server – Ansible Deployment

A clean, modern ETLegacy game server deployment using Ansible.  
No legacy mod folders, no outdated structure, no manual setup.  
Just a reproducible install that boots and runs the same way every time.

## What this role does

- Installs ETLegacy dedicated server binaries  
- Extracts original Wolf:ET pak files (pak0–pak2) automatically  
- Creates a minimal, modern directory layout:

```
/opt/etlegacy/
  bin/       # server binary
  etmain/    # pk3 files (maps, assets)
  configs/   # server.cfg and related configs
  home/      # fs_homepath (logs, downloads, runtime data)
  logs/      # server logs
```

- Deploys a tuned server.cfg with dynamic map voting  
- Sets up a systemd service for automatic startup  
- Ensures idempotency (safe to re-run anytime)

## Requirements

- Debian/Ubuntu host  
- Ansible 2.10+  
- Python + apt module  
- Root or sudo access  

## Quick start

Add the role to your playbook:

```yaml
- hosts: etlegacy
  become: yes
  roles:
    - etlegacy_base
```

Run it:

```
ansible-playbook -i hosts etlegacy.yml
```

Check the server:

```
systemctl status etlegacy
journalctl -u etlegacy -f
```

## Custom maps

Place additional maps into:

```
/opt/etlegacy/etmain/
```

Add them to `etlegacy_mapvote_list` to include them in dynamic voting.

## Notes

- Fast download (nginx) and MOTD are optional and disabled by default  
- The layout follows ETLegacy’s recommended fs_basepath/fs_homepath separation  
- Designed for homelabs, LAN parties, and small public servers  