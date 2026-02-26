# ansible-etlegacy

Ansible roles for deploying a full [ET:Legacy](https://www.etlegacy.com/) game server,
demonstrated on [wolf.jflab.cz](https://wolf.jflab.cz).

This is the **public** companion to a private Ansible repository.
It shows the structure and logic — secrets and real credentials are **not** included.
Replace every `<CHANGE_ME>` placeholder with your own values before running.

---

## Roles

### `etlegacy_base`
Installs and configures the ET:Legacy dedicated server binary.

- Downloads the ETLegacy server archive and extracts it to `etlegacy_install_dir`
- Extracts the original Wolf:ET `pak0/1/2.pk3` files from the Splash Damage installer
- Deploys `server.cfg` from a template
- Creates a systemd service (`etlegacy.service`) and starts it

Key variables (see `defaults/main.yml`):

| Variable | Default | Description |
|---|---|---|
| `etlegacy_install_dir` | `/opt/etlegacy` | Install path |
| `etlegacy_server_name` | `wolf.jflab.cz` | Hostname used by nginx and acme.sh |
| `etlegacy_hostname` | `ET:Legacy Server` | In-game server name |
| `etlegacy_password` | `<CHANGE_ME>` | Join password |
| `etlegacy_rcon_password` | `<CHANGE_ME>` | RCON password |
| `etlegacy_maxclients` | `20` | Max players |

---

### `etlegacy_maps`
Downloads additional map pk3 files into `etlegacy_install_dir/etmain/`.

---

### `etlegacy_mods`
Deploys server-side mod files (legacy mod config, banners, greetings, rules).

---

### `wolf_live_chat`
Deploys a lightweight Python web app that reads the ET:Legacy server log
and exposes a live chat view at `/chat`.

- `chat.py` — HTTP backend (default port `8080`) parsing `server.log`
- `index.html` — CRT-style browser frontend, auto-refreshes every 2 s
- `etl-show-chat` — CLI tool to dump chat to the terminal
- `live-chat.service` — systemd unit, starts on boot

Key variables:

| Variable | Default | Description |
|---|---|---|
| `live_chat_dir` | `/opt/live-chat` | App install directory |
| `live_chat_port` | `8080` | Backend listen port |
| `live_chat_password` | `<CHANGE_ME>` | Frontend login password |
| `live_chat_log_file` | `<etlegacy_install_dir>/home/legacy/server.log` | Log to parse |

---

### `nginx_etlegacy`
Installs nginx and deploys a vhost config for the server.

- HTTP → HTTPS redirect
- Serves `/legacy/` and `/etmain/` for auto-download
- Reverse-proxies `/chat/api` to the live-chat backend
- Requires TLS certificate files at the paths set in `nginx_ssl_certificate`
  and `nginx_ssl_certificate_key` (provisioned by `system_tuning`)

---

### `system_tuning`
Handles OS-level setup: firewall, swap, sysctl, and TLS certificate.

- **firewalld** — opens `ssh`, `http`, `https`, and `27960/udp` (game port)
- **sysctl** — sets `vm.swappiness` via `/etc/sysctl.d/99-tuning.conf`
- **swap** — creates a swapfile, activates it, and adds it to `/etc/fstab`
- **SSL directory** — creates `/etc/ssl/wolf` with correct ownership for nginx
- **acme.sh** — installs acme.sh, issues an EC-256 cert from ZeroSSL via
  DNS nsupdate challenge, installs cert to the SSL directory

Key variables:

| Variable | Default | Description |
|---|---|---|
| `firewall_udp_ports` | `[27960]` | Extra UDP ports to open |
| `swap_file` | `/.swapfile` | Swapfile path |
| `swap_size_mb` | `1024` | Swapfile size in MB |
| `sysctl_swappiness` | `10` | `vm.swappiness` value |
| `acme_email` | `<CHANGE_ME>` | ACME account e-mail |
| `acme_nsupdate_server` | `<CHANGE_ME>` | DNS server for nsupdate challenge |
| `acme_nsupdate_zone` | `<CHANGE_ME>` | DNS zone for TXT record |
| `acme_nsupdate_key_content` | *(define in host_vars)* | TSIG key for nsupdate |

---

## Usage

```bash
# Install required Ansible collections
ansible-galaxy collection install ansible.posix

# Run against your host (set inventory / vars first)
ansible-playbook -i inventory.yml wolf.yml
```

> **Note:** This repo does not include an inventory file or any secrets.
> You need to provide your own inventory and set all `<CHANGE_ME>` variables.
