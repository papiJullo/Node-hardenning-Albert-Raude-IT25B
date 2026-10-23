<<<<<<< HEAD
# Node Hardening - Lab 9 (K-B302-23-07)
 
| | |
|---|---|
| Host | `K-B302-23-07` |
| User | `opilane` |
| OS | Ubuntu 24.04.4 LTS |
| Kernel | 6.6.87.2-microsoft-standard-WSL2 |
| Environment | WSL |
| Run on | 2026-10-23 09:28 EEST |
 
## Notes
- WSL has no router VM and no desktop environment. ProxyJump was demonstrated through localhost (`router` and `lan-b` both resolve to 127.0.0.1) and nmap scanned localhost.
- `find` used `-xdev` so it does not crawl the Windows drives under /mnt, a small deviation from the lab command.

## Submission checklist
- [x] captured `before.txt`
- [x] captured `after.txt`
- [x] recorded the diff
- [x] documented each removal with justification
- [x] hardened SSH (drop-in `00-hardening.conf`)
- [x] validated `sshd -t`
- [x] reloaded SSH without restarting (sshd MainPID 5537 -> 5537)
- [x] disabled password authentication (effective `sshd -T` value)
- [x] configured `ProxyJump`
- [x] enabled unattended updates
- [x] proved automatic updates ran
- [x] verified port exposure and password refusal
 
## 9.A - Measure and reduce
 
| Metric | Before | After | Change |
|---|---|---|---|
| Listening TCP sockets (unique port/process) | 3 | 3 | 0 |
| Running services | 15 | 16 | 1 |
| Installed packages | 665 | 594 | -71 |
| SUID binaries | 14 | 14 | 0 |
 
Files: [before.txt](evidence/before.txt), [after.txt](evidence/after.txt), [diff.txt](evidence/diff.txt)
 
**Listening ports closed:**
- none
 
**Listening ports newly opened:**
- none
 
**Services no longer running:**
- none
 
71 packages were removed in total (targets plus dependencies cleaned up by autoremove); 0 SUID binaries disappeared.
 
### Justification table
 
| Change | What did this expose? | What would break without it? |
|---|---|---|
| Purged `cups` | Print service and network printer discovery (631/tcp IPP, mDNS/browsing) | Local and network printing stops |
| Purged `cups-browsed` | Print service and network printer discovery (631/tcp IPP, mDNS/browsing) | Local and network printing stops |
| Purged `avahi-daemon` | mDNS/DNS-SD multicast discovery (5353/udp); advertises host and services on the LAN | .local name resolution and automatic device discovery fail |
| Purged `telnetd` | Cleartext remote login (23/tcp); credentials sniffable | Legacy telnet clients can no longer connect |
| Purged `inetutils-telnetd` | Cleartext remote login (23/tcp); credentials sniffable | Legacy telnet clients can no longer connect |
| Purged `vsftpd` | Cleartext FTP service (21/tcp), credential and anonymous-access attack surface | FTP-based file transfers stop working |
| Purged `samba` | SMB/NetBIOS listeners (139/445); long history of remote exploits | Windows/SMB file and printer sharing stops |

### Account audit
Full output: [accounts-audit.txt](evidence/accounts-audit.txt)
- Accounts (UID >= 1000, login shell) other than `opilane` needing review: none
- Accounts with an empty password field: none
- Accounts deleted by this run: none
 
## 9.B - SSH and patching
 
### sshd hardening
- Key login to localhost worked before changing anything: yes
- Drop-in: [00-hardening.conf](evidence/00-hardening.conf) (named `00-` so it is read first and wins over other drop-ins); main file [sshd_config](evidence/sshd_config)
- Effective values: [sshd-effective.txt](evidence/sshd-effective.txt)
- `sshd -t` passed: yes; applied with `systemctl reload ssh` (existing sessions stay alive)
- Password login attempt result ([password-refused.txt](evidence/password-refused.txt)): opilane@localhost: Permission denied (publickey).
 
### ProxyJump
Config: [ssh_config_demo](evidence/ssh_config_demo). Test output: [proxyjump-test.txt](evidence/proxyjump-test.txt) (`K-B302-23-07`)
 
### Unattended upgrades
[unattended-upgrades.txt](evidence/unattended-upgrades.txt) - enabled in `20auto-upgrades`: yes. Last run result: No packages found that can be upgraded unattended and no pending auto-removals
 
### Verification with nmap
[nmap.txt](evidence/nmap.txt) - open TCP ports: 22/tcp (ssh)

 
## Reproduce
```bash
bash harden.sh        # BLOAT=1, STALE_USERS, KEEP_PW=1, TARGET, REPO options are documented in the script header
```
=======
# Node-hardenning-Albert-Raude-IT25B
>>>>>>> bf906dae56694ebe892f27cc5bf850031b1dedb6
