# Service Enumeration Cheat Sheet

## Overview

Service enumeration is the process of identifying running services, their exact versions, and additional banner information from open ports on a target system. This is crucial for:

- **Vulnerability Research:** Pinpointing known vulnerabilities for a specific service version.
- **Exploit Matching:** Finding and tailoring exploits that target the exact service and OS.

## Process Overview

1. **Initial Quick Scan:**  
   Perform a fast port scan to get an overview of open ports without generating excessive traffic.
2. **Service Version Detection:**  
   Use the `-sV` flag to probe open ports and capture service banners and version details.
3. **Monitoring Scan Progress:**  
   - Press the **Space Bar** during a scan to view real-time updates.
   - Use `--stats-every=5s` to automatically print scan status every 5 seconds.
4. **Increase Verbosity:**  
   Adding `-v` (or `-vv`) provides more detailed output as services are discovered.
5. **Manual Banner Grabbing:**  
   If automated scans miss some banner details, manually connect to the service using tools like `nc` (netcat) or capture network traffic with `tcpdump`.

## Example Commands

### Basic Service Version Detection

Perform a full port scan with service version detection:
```sh
sudo nmap 10.129.2.28 -p- -sV
```

- `-p-` scans all ports.
- `-sV` probes each open port for banner and version info.

### Monitoring Progress with Periodic Stats

Show scan progress every 5 seconds:

```sh
sudo nmap 10.129.2.28 -p- -sV --stats-every=5s
```
This prints status updates (elapsed time, completion percentage, and estimated time remaining) every 5 seconds.

### Increasing Verbosity

See discovered open ports as soon as they’re detected:

```sh
sudo nmap 10.129.2.28 -p- -sV -v
```
The `-v` flag increases output detail, listing open ports and banner information in real time.

### Advanced Scan with Packet Tracing

Use additional options to disable certain probes and trace packets:

```sh
sudo nmap 10.129.2.28 -p- -sV -Pn -n --disable-arp-ping --packet-trace
```

- `-Pn`: Skips host discovery (assumes target is up).
- `-n`: Disables DNS resolution.
- `--disable-arp-ping`: Prevents ARP ping, using only TCP probes.
- `--packet-trace`: Displays every packet sent and received.

## Banner Grabbing

Nmap collects banners from services which often include the version and other identifying information. For example, the output might include:

```
PORT      STATE SERVICE      VERSION
22/tcp    open  ssh          OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
25/tcp    open  smtp         Postfix smtpd
80/tcp    open  http         Apache httpd 2.4.29 ((Ubuntu))
```
### Note:

- Banners are sent after a successful three-way TCP handshake.
- Some services may not display banners immediately or may require manual intervention to retrieve the full details.

## Manual Banner Grabbing Techniques

### Using Netcat (nc)

Connect directly to a service to capture its banner:

```sh
nc -nv 10.129.2.28 25
```

Expected output:

```
Connection to 10.129.2.28 port 25 [tcp/*] succeeded!
220 inlane ESMTP Postfix (Ubuntu)
```

This method can reveal extra details not captured by Nmap.

### Using Tcpdump to Intercept Traffic

Capture the handshake and banner exchange:

```sh
sudo tcpdump -i eth0 host 10.10.14.2 and 10.129.2.28
```

Example output (simplified):

```
18:28:07.128564 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [S], ...
18:28:07.255151 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [S.], ...
18:28:07.319306 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [P.], length 35: SMTP: 220 inlane ESMTP Postfix (Ubuntu)
```

- The first three lines represent the TCP handshake (SYN, SYN-ACK, ACK).
- The `[P.]` packet shows the service banner being transmitted with the PSH flag.

## Key Considerations

- **Traffic Generation:**  
  A full scan with `-sV` can generate significant traffic. Start with a quick scan to minimize detection.
- **Accuracy vs. Stealth:**  
  Detailed scans (with `-v` and `--packet-trace`) provide more information but are less stealthy.
- **Banner Manipulation:**  
  Some services may alter or hide their banners. Always verify with manual checks if necessary.

## Summary

Service enumeration is essential for mapping out a target’s attack surface by identifying open ports, service names, and versions. Use a combination of:

- Automated scanning with Nmap (`-sV`, `-v`, `--stats-every`)
- Advanced options for detailed tracing (`--packet-trace`, `-Pn`, `-n`, `--disable-arp-ping`)
- Manual banner grabbing (using `nc` and `tcpdump`)

This comprehensive approach helps ensure you capture accurate and complete service information for vulnerability assessment and further exploitation.
