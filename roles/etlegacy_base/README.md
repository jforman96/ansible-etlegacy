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
  etmain/            # pk3 files (maps, assets)
  legacy/            # mod files shipped with ETLegacy
  home/              # fs_homepath (runtime data)
  home/legacy/       # server.cfg lives here
```

- Deploys a tuned server.cfg with dynamic map voting  
- Sets up a systemd service for automatic startup  
- Ensures idempotency (safe to re-run anytime)

## Requirements

- Linux host  
- Ansible 2.10+  
- Python 3.12+  
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