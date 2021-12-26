# Cluster

Currently, I have a cluster of seven Odroid C4s, but I want to leave general
instructions for setting up clusters for myself.

## Worker Node

Just set up any OS you want with dynamic IP assignment set up to work on the
interface upon which they're connected to the network. Make sure they have
valid `ssh` credentials that you know of and can be accessed via `ssh`.

I needed to add to the `/etc/environment` file as well in order to allow access
to the internet through the squid proxy.

## Master Node

First, I had to add entries to these files:

- /etc/default/isc-dhcp-server
- /etc/dhcp/dhcpd.conf 

Then, I needed to configure the interface where I connect to the cluster's
network.

```bash
sudo ifconfig enp60s0 10.10.0.1 netmask 255.255.255.0
```

I started `isc-dhcp-server` using the systemd unit which was installed via `apt
install`, and then ran `watch dhcp-lease-list` to see the servers register, as
well as `tcpdump -A -vv -i enp60s0 port 67` to listen for the DHCP protocol
activity.

Once I saw the machines had connected, I `ssh`ed into them using the
credentials I had set up on each node.

I started the `squid` http proxy and edited `/etc/squid/squid.conf`, replacing
`http_access deny all` with `http_access allow all`. The workers have to be
configured to use this proxy as well.

## Scripts

The `./get-nodes` script can be used to list the nodes on such a network.
