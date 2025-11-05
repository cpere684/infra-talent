```markdown
# infra-talent — Quickstart & Safety Checklist

What this package contains (minimal, ready-to-run)
- playbook.yml               : top-level play that calls role 'talent'
- inventory.ini              : example inventory (edit host IP and ansible_user)
- group_vars/all.yml         : minimal variables to set (ssh_pub_key, talent_user, timezone)
- roles/talent/tasks/main.yml: tasks to install packages, create user, set timezone, add shortcuts
- roles/talent/handlers/main.yml: handlers (apt update, restart display manager)
- README_QUICKSTART.md       : this file

IMPORTANT SAFETY NOTE about networking
- netplan changes are disabled by default. Do NOT set `netplan_config` in group_vars/all.yml for multiple hosts.
- For static IPs use host_vars/<hostname>.yml and test carefully on one host with console access available.

Quickstart (3 steps)

1) Download & unzip (example)
- curl -L -o infra-talent.zip "https://your.site/downloads/infra-talent-v1.0.zip"
- unzip infra-talent.zip && cd infra-talent

2) Edit the two required files
- inventory.ini
  - Replace vm1, 192.168.56.101 and ansible_user with your target host's IP/DNS and login user.
  - If using SSH key, set ansible_ssh_private_key_file path in the inventory entry.
- group_vars/all.yml
  - Set ssh_pub_key to the contents of your control machine public key (e.g., cat ~/.ssh/infrahub_test.pub).
  - Set talent_user to the username you want to create.
  - Set timezone to your timezone (e.g., "America/New_York").
  - Leave netplan_config empty unless you know what you are doing.

3) Dry-run and then run against a single host
- Dry-run (simulate changes):
  ansible-playbook -i inventory.ini playbook.yml --limit vm1 --check --diff
- Real run (first-time):
  ansible-playbook -i inventory.ini playbook.yml --limit vm1 --ask-become-pass

Useful options
- --limit <host_or_group> : restrict run to single host or group
- --tags cleanup          : run only cleanup-tagged tasks (apt autoremove)
- --skip-tags network     : if you add a network tag later and want to skip network changes
- -e "@vault.yml"         : use this to pass ansible-vault file if you encrypt secrets

Verify the result (on the target VM)
- Check package installed (e.g., firefox):
  dpkg -l | grep firefox
- Check user exists:
  id talent
- Check authorized_keys:
  sudo cat /home/talent/.ssh/authorized_keys

Rollbacks & cleanup
- Remove user:
  sudo deluser --remove-home talent
- Remove packages (example):
  sudo apt remove --purge -y google-chrome-stable libreoffice
  sudo apt autoremove -y

If things go wrong (SSH lost after network change)
- If you lose SSH due to network changes, use console access (provider console, VM GUI, or host machine console).
- Restore a backup netplan YAML (if you saved one) or revert changes from /etc/netplan and run `sudo netplan apply`.

Best practices
- Test first on a disposable VM or snapshot.
- Run commands against one host with --limit.
- Keep secrets out of group_vars in public downloads — use Ansible Vault for passwords or service tokens.
- Provide a SHA256 checksum for the ZIP on your website and recommend users verify downloads.

End of quickstart.
```
