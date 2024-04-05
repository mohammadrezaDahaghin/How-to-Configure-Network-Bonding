# How to Configure Network Bonding

To use network bonding mechanism in **Ubuntu** or **Debian** based Linux systems, first you need to install the bonding kernel module and test if the bonding driver is loaded via **modprobe** command.

```bash
$ sudo modprobe bonding
$ lsmod | grep bond
```

To create a bond interface composed of the first two physical NCs in your system, issue the below command. However this method of creating bond interface is ephemeral and does not survive system reboot.

```bash
$ sudo ip link add bondme0 type bond mode 802.3ad
$ sudo ip link set ens37 master bondme0
$ sudo ip link set eth38 master bondme0
```

To create a permanent bond interface in **mode 0** type, use the method to manually edit interfaces configuration file, as shown in the below excerpt.

```bash
$ sudo nano /etc/network/interfaces
```

```bash
# The primary network interface
auto bond0
iface bond0 inet static
	address 192.168.1.150
	netmask 255.255.255.0	
	gateway 192.168.1.1
	dns-nameservers 192.168.1.1 8.8.8.8
	dns-search domain.local
		slaves eth0 eth1
		bond_mode 0
		bond-miimon 100
		bond_downdelay 200
		bond_updelay 200
```

In order to activate the **bond interface**, either restart network service, bring down the physical interface and rise the **bond interface** or **reboot** the machine in order for the kernel to pick-up the new bond interface.

```bash
$ sudo systemctl restart networking.service
or
$ sudo ifdown eth0 && ifdown eth1 && ifup bond0
```

The bond interface settings can be inspected by issuing the below commands.

```bash
$ ifconfig 
or 
$ ip a
```

Details about the bond interface can be obtained by displaying the content of the below kernel file using cat command as shown.

```bash
$ cat /proc/net/bonding/bond0
```

Next use **mii-tool** tool to check Network Interface Controller (NIC) parameters as shown.

```bash
$ mii-tool
```