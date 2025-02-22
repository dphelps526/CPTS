# Host and Port Scanning Cheat Sheet

## Overview

It is essential to understand how the tool we use works and how it performs and processes its different functions. We only fully understand the results if we know what they mean and how they are obtained. Therefore, we'll take a closer look at some scanning methods. After confirming that our target is alive, we want to get a more accurate picture of the system. The information we need includes:

- Open ports and their services
- Service versions
- Information provided by the services
- Operating system

## Port States

There are a total of 6 different states for a scanned port:

| State               | Description |
|---------------------|-------------|
| **open**            | Connection established (TCP, UDP, SCTP). |
| **closed**          | The port is closed; a TCP RST is received. |
| **filtered**        | Nmap cannot determine if the port is open or closed (no response or error). |
| **unfiltered**      | Occurs during a TCP-ACK scan; the port is accessible, but its state is undetermined. |
| **open&#124;filtered** | No response received; indicates a firewall or packet filter may be protecting the port. |
| **closed&#124;filtered** | Only occurs in IP ID idle scans; it's impossible to determine if the port is closed or filtered. |

## Discovering Open TCP Ports

By default, Nmap scans the top 1000 TCP ports using a SYN scan (`-sS`) when run as root (due to raw socket requirements). When not run as root, it defaults to a TCP connect scan (`-sT`). You can define ports individually (`-p 22,25,80,139,445`), by range (`-p 22-445`), by most frequent ports (`--top-ports=10`), by scanning all ports (`-p-`), or by doing a fast scan (`-F` for the top 100 ports).

## Scanning Top 10 TCP Ports

```sh
sudo nmap 10.129.2.28 --top-ports=10
```

**Example Output:**

```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:36 CEST
Nmap scan report for 10.129.2.28
Host is up (0.021s latency).

PORT     STATE    SERVICE
21/tcp   closed   ftp
22/tcp   open     ssh
23/tcp   closed   telnet
25/tcp   open     smtp
80/tcp   open     http
110/tcp  open     pop3
139/tcp  filtered netbios-ssn
443/tcp  closed   https
445/tcp  filtered microsoft-ds
3389/tcp closed   ms-wbt-server
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 1.44 seconds
```

**Options:**

| Option            | Description                                |
|-------------------|--------------------------------------------|
| `10.129.2.28`     | Target IP address.                         |
| `--top-ports=10`  | Scans the top 10 most frequent ports.      |

!!! note
    In this scan, only the top 10 TCP ports are scanned, and Nmap displays their state accordingly.

## Nmap - Trace the Packets

To clearly view the SYN scan, disable ICMP echo requests, DNS resolution, and ARP ping:

```sh
sudo nmap 10.129.2.28 -p 21 --packet-trace -Pn -n --disable-arp-ping
```

**Options:**

| Option               | Description                                       |
|----------------------|---------------------------------------------------|
| `-p 21`             | Scans only port 21.                               |
| `--packet-trace`     | Shows all packets sent and received.            |
| `-Pn`               | Disables ICMP echo requests.                      |
| `-n`                | Disables DNS resolution.                          |
| `--disable-arp-ping` | Disables ARP ping.                                |

**Sample Trace:**

```
SENT (0.0429s) TCP 10.10.14.2:63090 > 10.129.2.28:21 S ttl=56 id=57322 iplen=44 seq=1699105818 win=1024 <mss 1460>
RCVD (0.0573s) TCP 10.129.2.28:21 > 10.10.14.2:63090 RA ttl=64 id=0 iplen=40 seq=0 win=0
```

### Breakdown

- **SENT**: Nmap sends a TCP SYN packet to port 21.
- **RCVD**: The target responds with a TCP packet with the RST and ACK flags (RA), indicating the port is closed.

## Connect Scan

The TCP Connect Scan (`-sT`) uses the full TCP three-way handshake:
- **Open**: The target responds with a SYN-ACK.
- **Closed**: The target responds with an RST.

This scan is highly accurate but less stealthy, as it fully establishes a connection. It is useful when the target’s firewall drops incoming packets but allows outgoing ones.

## Connect Scan on TCP Port 443

```sh
sudo nmap 10.129.2.28 -p 443 --packet-trace --disable-arp-ping -Pn -n --reason -sT
```

**Example Output:**

```
CONN (0.0385s) TCP localhost > 10.129.2.28:443 => Operation now in progress
CONN (0.0396s) TCP localhost > 10.129.2.28:443 => Connected
Nmap scan report for 10.129.2.28
Host is up, received user-set (0.013s latency).

PORT    STATE SERVICE REASON
443/tcp open  https   syn-ack
```

**Options:**

| Option      | Description                            |
|-------------|----------------------------------------|
| `-sT`      | Uses the TCP Connect scan.             |
| `--reason` | Displays the reason for a port's state.  |

## Filtered Ports

When a port is filtered, no response is received or an ICMP error is returned.

### Scanning Port 139 (Dropped Packets)

```sh
sudo nmap 10.129.2.28 -p 139 --packet-trace -n --disable-arp-ping -Pn
```

**Sample Output:**

```
SENT (0.0381s) TCP 10.10.14.2:60277 > 10.129.2.28:139 S ttl=47 id=14523 iplen=44 seq=4175236769 win=1024 <mss 1460>
SENT (1.0411s) TCP 10.10.14.2:60278 > 10.129.2.28:139 S ttl=45 id=7372 iplen=44 seq=4175171232 win=1024 <mss 1460>
...
PORT    STATE    SERVICE
139/tcp filtered netbios-ssn
```

### Scanning Port 445 (Rejected Packets)

```sh
sudo nmap 10.129.2.28 -p 445 --packet-trace -n --disable-arp-ping -Pn
```

**Sample Output:**

```
SENT (0.0388s) TCP 10.129.2.28:52472 > 10.129.2.28:445 S ttl=49 id=21763 iplen=44 seq=1418633433 win=1024 <mss 1460>
RCVD (0.0487s) ICMP [10.129.2.28 > 10.129.2.28 Port 445 unreachable (type=3/code=3)] iplen=72
```

**Explanation:** An ICMP reply (type 3, code 3) indicates that the port is unreachable—likely due to a firewall rule.

## Discovering Open UDP Ports

UDP scans (`-sU`) are slower because UDP is stateless. Nmap sends empty datagrams and often receives no response.

### UDP Port Scan (Fast Scan)

```sh
sudo nmap 10.129.2.28 -F -sU
```

**Example Output:**

```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:01 CEST
Nmap scan report for 10.129.2.28
Host is up (0.059s latency).
Not shown: 95 closed ports
PORT     STATE         SERVICE
68/udp   open|filtered dhcpc
137/udp  open          netbios-ns
138/udp  open|filtered netbios-dgm
631/udp  open|filtered ipp
5353/udp open          zeroconf
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 98.07 seconds
```

**Options:**

| Option | Description            |
|--------|------------------------|
| `-F`   | Scans the top 100 ports. |
| `-sU`  | Performs a UDP scan.     |

### UDP Port 137 Scan with Trace and Reason

```sh
sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 137 --reason
```

**Sample Output:**

```
SENT (0.0367s) UDP 10.10.14.2:55478 > 10.129.2.28:137 ttl=57 id=9122 iplen=78
RCVD (0.0398s) UDP 10.129.2.28:137 > 10.10.14.2:55478 ttl=64 id=13222 iplen=257
...
PORT    STATE SERVICE    REASON
137/udp open  netbios-ns udp-response ttl 64
```

### UDP Port 100 Scan (Port Unreachable)

```sh
sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 100 --reason
```

**Sample Output:**

```
SENT (0.0445s) UDP 10.10.14.2:63825 > 10.129.2.28:100 ttl=57 id=29925 iplen=28
RCVD (0.1498s) ICMP [10.129.2.28 > 10.10.14.2 Port unreachable (type=3/code=3)] iplen=56
...
PORT    STATE  SERVICE     REASON
100/udp closed unknown port-unreach ttl 64
```

### UDP Port 138 Scan (No Response)

```sh
sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 138 --reason
```

**Sample Output:**

```
SENT (0.0380s) UDP 10.10.14.2:52341 > 10.129.2.28:138 ttl=50 id=65159 iplen=28
SENT (1.0392s) UDP 10.10.14.2:52342 > 10.129.2.28:138 ttl=40 id=24444 iplen=28
...
PORT    STATE         SERVICE     REASON
138/udp open|filtered netbios-dgm no-response
```

## Version Scan

The `-sV` option enables version detection, which identifies service names, versions, and additional details.
```sh
sudo nmap 10.129.2.28 -Pn -n --disable-arp-ping --packet-trace -p 445 --reason -sV
```

**Example Output:**

```
Starting Nmap 7.80 ( https://nmap.org ) at 2022-11-04 11:10 GMT
SENT (0.3426s) TCP 10.10.14.2:44641 > 10.129.2.28:445 S ttl=55 id=43401 iplen=44 seq=3589068008 win=1024 <mss 1460>
RCVD (0.3556s) TCP 10.129.2.28:445 > 10.10.14.2:44641 SA ttl=63 id=0 iplen=44 seq=2881527699 win=29200 <mss 1337>
...
PORT    STATE SERVICE     REASON         VERSION
445/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
```

**Options:**

| Option                | Description                                               |
|-----------------------|-----------------------------------------------------------|
| `-sV`                | Performs a service/version scan.                          |
| `--reason`           | Displays the reason for a port's state.                   |
| _Other options_       | Same as previous scans: `-Pn`, `-n`, `--disable-arp-ping`, `--packet-trace`. |

## Summary

This cheat sheet covers key Nmap commands for host and port scanning. Adjust scanning options based on your target's firewall settings and network conditions to achieve optimal results.
