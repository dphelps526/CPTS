# Host Discovery Cheat Sheet

## Overview
When conducting an internal penetration test, the first step is to identify active hosts on the network. Nmap provides various host discovery options to determine if a target is online.

!!! tip "Best Practices"
    - Always store scan results for comparison, documentation, and reporting.
    - Different tools may produce different results, so it's beneficial to compare findings.

## Scan a Network Range
```sh
sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5
```

**Options:**

| Option         | Description                                                  |
|----------------|--------------------------------------------------------------|
| `10.129.2.0/24`| Target network range.                                        |
| `-sn`         | Disables port scanning.                                      |
| `-oA tnet`    | Saves results in all formats with prefix 'tnet'.             |

!!! note
    This works only if firewalls allow it. Other techniques may be needed if hosts are unresponsive.

## Scan an IP List
### Example `hosts.lst`:
```sh
cat hosts.lst
```
```
10.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28
```
### Scan Command:
```sh
sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5
```

**Options:**

| Option | Description                          |
|--------|--------------------------------------|
| `-iL`  | Reads targets from `hosts.lst`.      |

## Scan Multiple IPs
```sh
sudo nmap -sn -oA tnet 10.129.2.18 10.129.2.19 10.129.2.20 | grep for | cut -d" " -f5
```

### Define Range:
```sh
sudo nmap -sn -oA tnet 10.129.2.18-20 | grep for | cut -d" " -f5
```

## Scan a Single IP
```sh
sudo nmap 10.129.2.18 -sn -oA host
```

### Output Example:
```
Host is up (0.087s latency).
MAC Address: DE:AD:00:00:BE:EF
```

**Options:**

| Option      | Description                                  |
|-------------|----------------------------------------------|
| `-sn`       | Disables port scanning.                      |
| `-oA host`  | Saves results with prefix 'host'.            |

## Ensure ICMP Echo Requests are Sent
```sh
sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace
```

**Options:**

| Option            | Description                         |
|-------------------|-------------------------------------|
| `-PE`            | Uses ICMP Echo requests.            |
| `--packet-trace`  | Displays sent/received packets.     |

## Display Reason for Host Being Up
```sh
sudo nmap 10.129.2.18 -sn -oA host -PE --reason
```

**Options:**

| Option         | Description                                    |
|----------------|------------------------------------------------|
| `--reason`     | Shows why the host is marked as up.            |

## Disable ARP Requests
```sh
sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace --disable-arp-ping
```

**Options:**

| Option                | Description                                      |
|-----------------------|--------------------------------------------------|
| `--disable-arp-ping`  | Forces ICMP Echo requests instead of ARP.        |

## Summary
This cheat sheet provides essential Nmap commands for host discovery in internal penetration testing. Adjust options based on firewall configurations and network conditions.
