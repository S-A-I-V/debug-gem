---
tags: [os, infrastructure, systems]
aliases: [linux, unix]
---
# Linux

Operating system kernel. Foundation for servers, containers, cloud, and most of the internet.

## Known For These Error Patterns
- [[Permission & Authorization Errors]] — File permissions, sudo, SELinux/AppArmor
- [[Resource Exhaustion Errors]] — Disk full, OOM killer, file descriptor limits, process limits
- [[Connection & Network Errors]] — Firewall (iptables/nftables), DNS, routing
- [[Configuration & Environment Errors]] — PATH, env vars, config files

## Essential Debug Commands
- `strace -p <pid>` — trace system calls
- `lsof -i :8080` — what's using port 8080
- `ss -tlnp` — listening TCP ports
- `top` / `htop` — process resource usage
- `df -h` / `df -i` — disk space / inodes
- `free -m` — memory usage
- `dmesg` — kernel messages (OOM kills, hardware errors)
- `journalctl -u <service>` — service logs

## Related
- [[Docker]] — containers are Linux processes
- [[MOC — Cloud & Infrastructure]]

## My Notes

