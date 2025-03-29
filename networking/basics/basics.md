# Networking Basics

## Switching
Let's talk about some concepts before jump into the practical part of applying commands to really understand them.

**🧠 First: What is a host?**
A host is just a computer or device connected to a network.
- Your laptop? → A host.
- A server? → A host.
- A router? → Also a host (with extra functions).

👉 It’s any device that can send or receive data over a network.

**🧩 Second: What is a network interface?**
A network interface is the thing on your host that connects it to a network.

There are different types:

| Interface Type | Example Name | Description |
|----------------|--------------|-------------|
| Ethernet       | eth0, enp3s0 | Wired connection port |
| Wi-Fi          | wlan0        | Wireless connection |
| Loopback       | lo           | Internal-only, for testing (127.0.0.1) |
| Virtual        | docker0, veth1234 | Virtual links created by tools like Docker, Mininet |

👉 So if a host is the whole machine, a network interface is like one of its "network ports".

Now, we can talk about connect hosts without misunderstandings!

To connect two systems (hosts), we need a switch that can be connected locally or virtually to both systems, creating a network. To connect one system to the switch (network), we need an interface (physical or virtual depending on the host) on both hosts. For Linux, we can use the `ip link` command to manage network interfaces. 

A network interface is a point of interconnection between a computer and a network, allowing the computer to communicate over the network. The `ip link` command is used to display and modify network interfaces, providing a way to manage the connections between the systems and the network.

👉 **Linux ip Command Syntax**
The ip command accepts objects (subcommands), options, and additional arguments, allowing users to configure and manage network interfaces, routes, tunneling, etc. Below is the basic command syntax:

```bash
ip [option] [subcommand] [arguments]
```

The following list introduces the most frequently used ip command objects:

- `link (l)` configures network interfaces.
- `address (addr/a)` modifies protocol addresses (IP, IPv6).
- `route (r)` alters routing tables.
- `neigh (n)` manipulates neighbor objects.

Consider that the hosts do not need to share exactly the same network interface; they just need to have one that can connect to the network and is compatible with the network's communication protocol. For example, two computers can have different types of network interfaces, such as a WiFi card and an Ethernet port, and still communicate because both interfaces are compatible with the protocol of a WiFi router.

So, we'll use a command to see our available network's interfaces:

```bash
ip link

>> eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DORMANT group default qlen 1000
>> link/ether c4:3d:1a:aa:ea:ba brd ff:ff:ff:ff:ff:ff
>> altname eth0s20f3

```

Then, we add a network to our network interface:
> The `addr` option isn't creating or adding a whole network, it just connecting our host to the specified existing network.

```bash
ip addr add 192.168.1.10/24 dev eth0
```
**What does this command do?**
- `ip addr add`: Command to add a new IP address.
- `192.168.1.10/24`: The IP address you are assigning, with subnet mask /24 (which means 255.255.255.0).
- `dev eth0`: Apply this configuration to the network device/interface called eth0. Here, `dev` is a keyword which stands for *"device"*.

So, now we can **share and receive info across the network** whose ip we pass to the `ip addr add` command.


## Routing and Gateways
A router is an intelligent device that is capable of mange and communicate multiple networks.

The gateway something like the door of the router, the way in into the router and its respective networks.
So, the systems/hosts need to know where that gateway is to can go trough it to the router networks.
To see the existing routing configuration on the system, we'll use the `route` command.

So, if we need to add a route of the router we have to secify the network ip and the gateway ip. Just like this:

```bash
# Command
ip route add <network_ip to reach>/<cidr> via <gateway_ip>

# Example
ip route add 192.168.2.0/24 via 192.168.1.1
```

<p align="center">
  <img src="../../img/example_routing.png" alt="img">
</p>

In the example `192.168.2.0` is the ip of the purple network (switch) and `192.168.1.1` is the ip of the gateway for the blue network where we're located trying to get access to the purple network.

At this point, consider that when we're sending packets (units of data that contain information like source/destination addresses and the actual data being transmitted) our host or system uses its own router table.

A routing table is like a GPS navigation system for network packets - it's a set of rules that tells data which path to take to reach its destination network through available gateways. It is used for the host to know where to send the packets but it does not know which one is the final destination of the packet.

The host can deliver data directly to the network if we know it, or in case of not having the info about the destination network it will deliver the packets to its respective `default gateway`.

For example, if we set `ip route add default via 192.168.1.1` we're telling the system:
> "If a packet is going to an unknown destination (not on the local network), send it to 192.168.1.1. It will know how to forward it."

But, what if we don't have a router but a **host that can act as an intermediary for two hosts?**

In this example, we just have to:
1. Make sure that B is **acting as a router**.
2. Add the network we're trying to communicate with through the gateway of the "router".

<p align="center">
  <img src="../../img/host_as_router.png" alt="img">
</p>

But, there's a problem. If we try to ping the ip `192.168.2.5` we won't have a response back.

This happens by a default behaviour of Linux, it doesn't pass the packets from one interface (`eth0` for this example) to another (`eth1`). The main reason is for **security risk**, because in case of a private network in blue and a public one for the purple on the image, we don't want people in purple get access to info in blue network.

If we want to allow host B to forward packets across interfaces, we need to set it up.

```bash
# See the current boolean value of this behaviour
cat /proc/sys/net//ipv4/ip_forward

# Set it to True (this values is restored with reboots)
echo 1 > /proc/sys/net//ipv4/ip_forward

# To pin the value, we must set it into this path too
# /etc/sysctl/conf
net.ipv4.ip_forward = 1
```

## DNS Configurations in Linux

To connect to other networks we can use aliases for other hosts we want to reach. So, for that purpose we will modify the `/etc/hosts` file.

```bash
# Open the file with your preferred text editor
sudo nano /etc/hosts

# Add the alias (ip and the name we want to reach, in this case the DB server) 
192.168.1.10 DB-SERVER
```

This file is consider as the ground truth for the hostnames and their respective IPs in our own system, because the other host name could be something like "host02" but our system will always use the information in this file to resolve the name to the correct IP.

Actually, we could set an alias to a `www.google.com` and our system will use the information in this file to resolve the name to the IP name in the file, causing an error because we actualy want to test our connecting with internet, we would be assessing our connection to the DB server.

```bash
# Open the file with your preferred text editor
sudo nano /etc/hosts

# Add the alias (incorrect)
192.168.1.10 www.google.com

# So, if we ping to the www.google.com we will get a wrong response because we altered wrong the file
ping www.google.com
```

There's no limit to the number of aliases and hosts we can add to the file, we can add as many as we want.

A DNS server is a system that **translates human-readable hostnames into IP addresses**. It's like a phonebook for the internet. It will link each name to the correct IP address and the hosts in the network will use it instead of the `/etc/hosts` file.

**Nice, but how do we point to the DNS server?**

We can set the DNS server with IP `192.168.1.100` in the `/etc/resolv.conf` file (all DNS servers has this file).

```bash
# Open the file with your preferred text editor of our system
sudo nano /etc/resolv.conf

# Add the ip server with its respective name
nameserver        192.168.1.100
```

In this way, the system will use the DNS server to resolve the hostnames to the correct IPs and in case of changes we just have to update the file.

**But, what if I have the a common name in my etc/hosts file and in the DNS server?**

In this case, the system will use the information in the `/etc/hosts` file to resolve the name to the IP. If is not in the file, it will use the DNS server to resolve the name to the IP.

To change this behaviour, we can modify the `/etc/nsswitch.conf` file.

```bash
# Open the file with your preferred text editor of our system
sudo nano /etc/nsswitch.conf

# Change the order of the query
...
hosts: dns files
...
```

In this way, the system will use first the DNS server to resolve the name to the IP and if is not in the DNS server, it will use the information in the `/etc/hosts` file to resolve the name to the IP.

**Nice, but if I use a name that is not in the DNS server and not in the `/etc/hosts` file?**

In this case, the system will not be able to resolve the name to the IP and we will get an error.

```bash
ping www.facebook.com

>> ping: www.facebook.com: Temporary failure in name resolution
```

So, to avoid this we can set a default public ip in the DNS server that we know that map multiple hosts like the `8.8.8.8` ip from Google DNS.

```bash
# Open the file with your preferred text editor of our system
sudo nano /etc/resolv.conf

# Add the default public ip (host tries in the order of the list)
nameserver 192.168.1.100 # Our DNS server
nameserver 8.8.8.8 # Google DNS
```

In this way, the system will use the default public ip to resolve the name to the IP in case of not encounter the name in the DNS server and the `/etc/hosts` file.

Names such as `www.facebook.com` are called **FQDN** (Fully Qualified Domain Name) or domain names for short and they are used to identify a host in the network.

The **TLD** (Top Level Domain) is the last part of the FQDN. This reveals the type of the host, for example:

- `.com` for commercial websites
- `.org` for non-profit organizations
- `.net` for network-related entities
- `.edu` for educational institutions
- `.gov` for government agencies

For a domain name:
  - The `.` is the root of the domain name.
  - The `www` is the subdomain of the domain name.
  - The `facebook` is the host of the domain name.

For example, Google handles multiples subdomains like `www.google.com`, `mail.google.com`, `drive.google.com`, etc.

When we hit a domain name (like `apps.google.com`) in an organization, the company's DNS server follow the following steps:
1. Hit the DNS server of the organization.
2. As the internal server doesn't know the domain name, it will ask the root DNS server for the TLD server. This returns the IP of the TLD server.
3. From our host, we hit the root (TLD) server to ask for the host of the domain name (`google.com` in this case). This returns the IP address of the DNS server of Google.
4. Now, from our host, we hit the DNS server of Google to ask for the host of the domain name (`apps.google.com` in this case). This returns the IP address of the host.
5. To avoid run this flow every time, the DNS server of Google will cache the IP address of the host for some time (seconds up to minutes).

> 💡 With this knowledge, we can use better descriptive names for our IPs in the `/etc/hosts` file and DNS server in our company. So, instead of hit "web" we can hit something like "db.my_company.com" or "mail.my_company.com".

There'are types of recording in the DNS server:

| Record Type | Description | Example |
|------------|-------------|---------|
| **A Record** | Maps a hostname to an IPv4 address | example.com -> 192.168.1.1 |
| **AAAA Record** | Maps a hostname to an IPv6 address | example.com -> 2001:0db8:85a3:0000:0000:8a2e:0370:7334 |
| **CNAME Record** | Maps a hostname to another hostname | www.example.com -> example.com |
| **MX Record** | Maps a hostname to a mail exchange server | example.com -> mail.example.com |
| **NS Record** | Maps a hostname to a name server | example.com -> ns1.example.com |

Consider that altough `ping` is a very practical command to check name resolution, it's not the only way to do it. We can use other commands like `dig` or `nslookup` to check name resolution.

An extra consideration is that `nslookup` and `dig` are commands that ignore the `/etc/hosts` file because they are designed to be troubleshooting tools.

**nslookup:**
```bash
# Consider that nslookup is a command that ignore the `/etc/hosts` file
nslookup www.google.com

>> Server: 192.168.1.100
>> Address: 192.168.1.100#53

>> Non-authoritative answer:
>> Name: www.google.com
>> Address: 172.217.14.14
```

**dig:**
```bash
# Consider that dig is a command that ignore the `/etc/hosts` file
dig www.google.com

>> ; <<>> DiG 9.16.20 <<>> www.google.com

>> ;; global options: +cmd
>> ;; Got answer:
>> ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 4054
>> ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
>> 
>> ;; OPT PSEUDOSECTION:
>> ; EDNS: version: 0, flags:; udp: 4096

>> ;; QUESTION SECTION:
>> www.google.com. IN A

>> ;; ANSWER SECTION:
>> www.google.com. 300 IN A 172.217.14.14

>> ;; Query time: 0 msec
>> ;; SERVER: 192.168.1.100#53
>> ;; WHEN: Fri Mar 28 15:30:00 2025
>> ;; MSG SIZE  rcvd: 52
```

To facilitate the configuration we cal alos us e the `search` command in the `/etc/resolv.conf` file.

```bash
# Open the file with your preferred text editor of our system
sudo nano /etc/resolv.conf

# Add the search command
search my_company.com
```

In this way, the system will use the `search` command to resolve the name to the IP. For example, if we try to resolve the name just "mail" the system will try to resolve "mail.my_company.com".











































