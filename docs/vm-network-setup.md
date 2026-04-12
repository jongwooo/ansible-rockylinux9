# VM Network Manual Setup

Initial VM network configuration is done manually, not via Ansible.

## Network Overview

Each VM has two NICs:

| Connection Name | Type | Subnet | Purpose |
|-----------------|------|--------|---------|
| shared | Shared | 192.168.64.0/24 | Host SSH access, internet |
| internal | Host-Only | 10.220.100.0/24 | VM internal communication |

- `enp0s1`: Shared network — DHCP (192.168.64.0/24)
- `enp0s2`: Host-Only network — `internal`, static (10.220.100.*)

## bastion01

```bash
hostnamectl set-hostname bastion01
nmcli con add type ethernet ifname enp0s1 con-name shared ipv4.method auto connection.autoconnect yes
nmcli con add type ethernet ifname enp0s2 con-name internal ipv4.method manual ipv4.addresses 10.220.100.10/24 connection.autoconnect yes
nmcli con up shared
nmcli con up internal
```

## web01

```bash
hostnamectl set-hostname web01
nmcli con add type ethernet ifname enp0s1 con-name shared ipv4.method auto connection.autoconnect yes
nmcli con add type ethernet ifname enp0s2 con-name internal ipv4.method manual ipv4.addresses 10.220.100.11/24 connection.autoconnect yes
nmcli con up shared
nmcli con up internal
```

## was01

```bash
hostnamectl set-hostname was01
nmcli con add type ethernet ifname enp0s1 con-name shared ipv4.method auto connection.autoconnect yes
nmcli con add type ethernet ifname enp0s2 con-name internal ipv4.method manual ipv4.addresses 10.220.100.21/24 connection.autoconnect yes
nmcli con up shared
nmcli con up internal
```

## was02

```bash
hostnamectl set-hostname was02
nmcli con add type ethernet ifname enp0s1 con-name shared ipv4.method auto connection.autoconnect yes
nmcli con add type ethernet ifname enp0s2 con-name internal ipv4.method manual ipv4.addresses 10.220.100.22/24 connection.autoconnect yes
nmcli con up shared
nmcli con up internal
```

## db01

```bash
hostnamectl set-hostname db01
nmcli con add type ethernet ifname enp0s1 con-name shared ipv4.method auto connection.autoconnect yes
nmcli con add type ethernet ifname enp0s2 con-name internal ipv4.method manual ipv4.addresses 10.220.100.31/24 connection.autoconnect yes
nmcli con up shared
nmcli con up internal
```

## db02

```bash
hostnamectl set-hostname db02
nmcli con add type ethernet ifname enp0s1 con-name shared ipv4.method auto connection.autoconnect yes
nmcli con add type ethernet ifname enp0s2 con-name internal ipv4.method manual ipv4.addresses 10.220.100.32/24 connection.autoconnect yes
nmcli con up shared
nmcli con up internal
```

## SSH Port Change

All VMs use port **11122** instead of the default 22.

```bash
# 1. Change sshd port
sed -i 's/^#Port 22/Port 11122/' /etc/ssh/sshd_config
systemctl restart sshd

# 2. Update iptables rule (22 -> 11122)
sed -i 's/--dport 22/--dport 11122/' /etc/sysconfig/iptables
systemctl restart iptables
```
