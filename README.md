# ansible-pcap-to-zed-lake

The goal is to provide a near real-time packet trace for analysis. 

**To get started:**
Set up a machine with 2 interfaces (VM, laptop, or raspberry pi).

Specify one interface to listen to a TAP or SPAN/mirrored port.

This port will be set in promisc mode. (provided by /etc/rc.local)

Every 1-2 minutes the pcap files will be converted and loaded into a zed lake...


## Requirements
Ubuntu 22.04, I'm running in a cloud image on proxmox. *See the Disclaimer below

```
ansible-galaxy install geerlingguy.ntp
```

## Variables
vars/main.yml

or 

any of the roles/*/defaults/main.yml

# Run the playbook

``` ansible-playbook -i hosts.yml playbook.yml```

## Proxmox Settings
*Ignore this section if you are not using proxmox.*

Some additional settings are needed in proxmox.

```
ip link set $IFACE promisc on

brctl setageing vmbr3 0
brctl setfd vmbr3 0

```

### Persistance settings



 https://forum.proxmox.com/threads/network-sensor-monitor-session-promiscuous.37845/
 ```
/etc/network/interfaces
...
iface enxe inet manual
        up /sbin/ip link set $IFACE promisc on
#NIC_3_SPAN
...
auto vmbr3
iface vmbr3 inet manual
        bridge-ports enxe
        bridge-stp off
        bridge-fd 0
        bridge-vlan-aware yes
        bridge-vids 2-4094
        up /usr/sbin/brctl setageing vmbr3 0
        up /usr/sbin/brctl setfd vmbr3 0
#SPAN bridge
 ```

# Disclaimer

I hacked this together, for my needs, in way too many hours. It's not meant for production use.

This has only been tested for my sandbox environment. Your results may vary.
