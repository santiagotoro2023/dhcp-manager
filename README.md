# dhcp-manager

A Bash CLI utility for installing and managing ISC DHCP Server on Debian.

## Prepare the Server (Debian)
Before installing the script and with it setting up the DHCP, make sure your server device is configured as such. I myself usually use a Debian 12.3.0 VM with two NICs.

(Replace ens33 and ens36 with your actual interface names)

First, set a static IP Address for the NIC the DHCP will be listening on. This is a requirement for IDC DHCP Server:
```bash
sudo nano /etc/network/interfaces
```

Example configuration:
```bash
auto ens33		# This is the NAT interface
iface ens33 inet dhcp

auto ens36
iface ens36 inet static	# This is the interface the DHCP will be listening on
	address 192.168.20.1
	netmask 255.255.255.0
```

Enable IP forwarding to make sure your Server actually routes packets between the two networks:
```bash
sudo nano /etc/sysctl.conf
```

Edit the option 'net.ipv4.ip_forward' and enable it:
```bash
net.ipv4.ip_forward=1
```

Apply the new configuration:
```bash
sudo sysctl -p
```

Set up the NAT with iptables:
```bash
sudo iptables -t nat -A POSTROUTING -o ens33 -j MASQUERADE
sudo iptables -A FORWARD -i ens36 -o ens33 -j ACCEPT
sudo iptables -A FORWARD -i ens33 -o ens36 -m state --state RELATED,ESTABLISHED -j ACCEPT
```

Make iptables rules persistent across reboots:
```bash
sudo apt install iptables-persistent
sudo netfilter-persistent save
```

## Install
```bash
git clone "https://github.com/santiagotoro2023/dhcp-manager"
```
```bash
cd dhcp-manager
```
```bash
sudo cp dhcp-manager /usr/local/bin/dhcp-manager
```
```bash
sudo chmod +x /usr/local/bin/dhcp-manager
```

## Usage
```bash
sudo dhcp-manager          # interactive wizard
sudo dhcp-manager --help   # full command reference
```
