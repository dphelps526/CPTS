# Nmap Commands Cheat Sheet

## Basic Nmap Commands
Basic Nmap scanning command examples, often used at the first stage of enumeration.

| Command                                                        | Description                                                                                                       |
|----------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| `nmap -sP 10.0.0.0/24`                                           | Nmap scans the network, listing machines that respond to ping.                                                   |
| `nmap -p 1-65535 -sV -sS -T4 target`                             | A full TCP port scan with service version detection. (T1-T5 sets the scan speed.)                                 |
| `nmap -v -sS -A -T4 target`                                       | Verbose output; stealth SYN scan; T4 timing; OS and version detection + traceroute and scripts against target.    |
| `nmap -v -sS -A -T5 target`                                       | Verbose output; stealth SYN scan; T5 timing; OS and version detection + traceroute and scripts against target.    |
| `nmap -v -sV -O -sS -T5 target`                                   | Verbose output; stealth SYN scan; T5 timing; OS and version detection.                                             |
| `nmap -v -p 1-65535 -sV -O -sS -T4 target`                        | Verbose output; stealth SYN scan; T4 timing; OS and version detection + full port range scan.                       |
| `nmap -v -p 1-65535 -sV -O -sS -T5 target`                        | Verbose output; stealth SYN scan; T5 timing; OS and version detection + full port range scan.                       |

> **Note:** Aggressive scan timings (T5) are faster but can yield inaccurate results. T5 may miss ports; T3-4 often provide a better compromise depending on your network environment.

---

## Nmap Scan from File

| Command                         | Description                                                   |
|---------------------------------|---------------------------------------------------------------|
| `nmap -iL ip-addresses.txt`     | Scans a list of IP addresses (options can be added before/after). |

---

## Nmap Scan All Ports

| Command             | Description                                                        |
|---------------------|--------------------------------------------------------------------|
| `nmap -p- target`   | Scans all TCP ports on a target (full port range scan).             |

---

## Nmap Output Formats

| Command                                                                 | Description                                                                          |
|-------------------------------------------------------------------------|--------------------------------------------------------------------------------------|
| `nmap -sS -sV -T5 10.0.1.99 -oA output-all-formats`                     | Outputs Nmap results in all formats.                                                 |
| `nmap -sV -p 139,445 -oG grep-output.txt 10.0.1.0/24`                   | Outputs "grepable" output to a file (e.g., for Netbios servers).                       |
| `nmap -sS -sV -T5 10.0.1.99 --webxml -oX - \| xsltproc --output file.html -` | Exports Nmap output to an HTML report.                                               |

---

## Nmap Netbios Examples

| Command                                                                       | Description                                                                                           |
|-------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| `nmap -sV -v -p 139,445 10.0.0.1/24`                                           | Finds all Netbios servers on the subnet.                                                              |
| `nmap -sU --script nbstat.nse -p 137 target`                                   | Displays the Netbios name.                                                                            |
| `nmap --script-args=unsafe=1 --script smb-check-vulns.nse -p 445 target`        | Checks if Netbios servers are vulnerable to MS08-067.                                                 |

> **Warning:** The argument `--script-args=unsafe=1` has the potential to crash servers/services. Use with caution!

---

## Nmap Nikto Scan
Nmap + Nikto scanning for specific discovered HTTP ports.

| Command                                                                      | Description                                                                                                   |
|------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `nmap -p80 10.0.1.0/24 -oG - \| nikto.pl -h -`                              | Scans for HTTP servers on port 80 and pipes the output into Nikto for further scanning.                        |
| `nmap -p80,443 10.0.1.0/24 -oG - \| nikto.pl -h -`                           | Scans for HTTP/HTTPS servers on ports 80 and 443 and pipes the output into Nikto for further scanning.          |

---

## Nmap Cheatsheet: Target Specification
Nmap allows hostnames, IP addresses, and subnets.

**Examples:**  
`blah.highon.coffee`, `nmap.org/24`, `192.168.0.1`, `10.0.0-255.1-254`

| Command            | Description                                                        |
|--------------------|--------------------------------------------------------------------|
| `-iL`              | Input from list of hosts/networks.                                 |
| `-iR`              | Iterate hosts: choose random targets from the input file.          |
| `--exclude`        | Exclude specific hosts/networks. Format: `host1,host2,host3,...`     |
| `--excludefile`    | Exclude hosts list from a file.                                    |

---

## Host Discovery

| Command                   | Description                                                                                                             |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------|
| `-sL`                    | List Scan – simply lists targets to scan.                                                                              |
| `-sn`                    | Ping scan/sweep – runs a network scan with port scanning disabled.                                                     |
| `-Pn`                    | Treat all hosts as online; skip host discovery.                                                                        |
| `-PS/PA/PU/PY[portlist]`  | TCP SYN/ACK, UDP, or SCTP discovery to given ports (e.g., `-PS22` to check if host is up on port 22).                    |
| `-PE/PP/PM`              | ICMP echo, timestamp, and netmask request discovery probes.                                                            |
| `-PO[protocol list]`     | IP Protocol Ping.                                                                                                      |
| `-n` or `-R`             | Never do DNS resolution / Always resolve (default behavior varies).                                                    |

---

## Scan Techniques

| Command(s)                             | Description                                 |
|----------------------------------------|---------------------------------------------|
| `-sS`                                  | TCP SYN scan.                               |
| `-sT`                                  | TCP Connect scan.                           |
| `-sA`                                  | TCP ACK scan.                               |
| `-sW`                                  | TCP Window scan.                            |
| `-sM`                                  | Maimon scan.                                |
| `-sU`                                  | UDP Scan.                                   |
| `-sN`                                  | TCP Null scan.                              |
| `-sF`                                  | TCP FIN scan.                               |
| `-sX`                                  | TCP Xmas scan.                              |
| `--scanflags`                          | Customize TCP scan flags.                   |
| `-sI zombie host[:probeport]`          | Idle scan.                                  |
| `-sY`                                  | SCTP INIT scan.                             |
| `-sZ`                                  | SCTP COOKIE-ECHO scan.                      |
| `-sO`                                  | IP protocol scan.                           |
| `-b "FTP relay host"`                  | FTP bounce scan.                            |

---

## Port Specification and Scan Order

| Command                     | Description                                                                   |
|-----------------------------|-------------------------------------------------------------------------------|
| `-p`                       | Specify ports (e.g., `-p80,443` or `-p1-65535`).                               |
| `-p U:PORT`                | Scan UDP ports (e.g., `-p U:53`).                                               |
| `-F`                       | Fast mode: scans fewer ports than the default scan.                           |
| `-r`                       | Scan ports consecutively (do not randomize the order).                        |
| `--top-ports "number"`      | Scan the specified number of most common ports.                               |
| `--port-ratio "ratio"`      | Scan ports more common than the given ratio.                                  |

---

## Service Version Detection

| Command                             | Description                                                                                       |
|-------------------------------------|---------------------------------------------------------------------------------------------------|
| `-sV`                              | Probe open ports to determine service/version information.                                        |
| `--version-intensity "level"`       | Set intensity from 0 (light) to 9 (try all probes).                                               |
| `--version-light`                   | Limit to the most likely probes (intensity 2).                                                    |
| `--version-all`                     | Try every single probe (intensity 9).                                                             |
| `--version-trace`                   | Show detailed version scan activity (useful for debugging).                                       |

---

## Script Scan

| Command                                                     | Description                                                                                                   |
|-------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `-sC`                                                      | Equivalent to `--script=default`.                                                                              |
| `--script="Lua scripts"`                                    | Specify a comma-separated list of directories, script files, or script categories to run.                       |
| `--script-args=n1=v1,[n2=v2,...]`                            | Provide arguments to scripts.                                                                                  |
| `--script-args-file=filename`                               | Provide NSE script arguments from a file.                                                                      |
| `--script-trace`                                            | Show all data sent and received during script execution.                                                       |
| `--script-updatedb`                                         | Update the NSE script database.                                                                                |
| `--script-help="Lua scripts"`                               | Show help about specified scripts.                                                                             |

---

## OS Detection

| Command                   | Description                                               |
|---------------------------|-----------------------------------------------------------|
| `-O`                     | Enable OS detection.                                      |
| `--osscan-limit`         | Limit OS detection to promising targets.                |
| `--osscan-guess`         | Guess the OS more aggressively.                         |

---

## Timing and Performance
Options which take TIME can be in seconds or appended with `ms`, `s`, `m`, or `h` (e.g., `30m`).

| Command                                                             | Description                                                                  |
|---------------------------------------------------------------------|------------------------------------------------------------------------------|
| `-T 0-5`                                                            | Set timing template (higher is faster but less accurate).                    |
| `--min-hostgroup SIZE`<br>`--max-hostgroup SIZE`                      | Parallel host scan group sizes.                                              |
| `--min-parallelism NUMPROBES`<br>`--max-parallelism NUMPROBES`          | Controls probe parallelization.                                              |
| `--min-rtt-timeout TIME`<br>`--max-rtt-timeout TIME`<br>`--initial-rtt-timeout TIME` | Specify probe round trip time.                                              |
| `--max-retries TRIES`                                                | Limit the number of port scan probe retransmissions.                         |
| `--host-timeout TIME`                                                | Give up on a target after the specified time.                                |
| `--scan-delay TIME`<br>`--max-scan-delay TIME`                         | Adjust delay between probes.                                                 |
| `--min-rate NUMBER`                                                  | Send packets at no slower than the specified rate (packets per second).        |
| `--max-rate NUMBER`                                                  | Send packets at no faster than the specified rate (packets per second).        |

---

## Firewalls, IDS Evasion, and Spoofing

| Command                                                     | Description                                                                               |
|-------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| `-f; --mtu VALUE`                                           | Fragment packets (optionally specify the MTU).                                             |
| `-D decoy1,decoy2,ME`                                        | Cloak a scan by using decoy IP addresses.                                                  |
| `-S IP-ADDRESS`                                              | Spoof the source address.                                                                  |
| `-e IFACE`                                                  | Use the specified network interface.                                                       |
| `-g PORTNUM`<br>`--source-port PORTNUM`                      | Use the given source port for the scan.                                                    |
| `--proxies url1,[url2],...`                                  | Relay connections through HTTP or SOCKS4 proxies.                                          |
| `--data-length NUM`                                          | Append random data of specified length to sent packets.                                   |
| `--ip-options OPTIONS`                                       | Send packets with specified IP options.                                                    |
| `--ttl VALUE`                                                | Set the IP time-to-live field.                                                             |
| `--spoof-mac ADDR/PREFIX/VENDOR`                             | Spoof the MAC address.                                                                     |
| `--badsum`                                                  | Send packets with a bogus TCP/UDP/SCTP checksum.                                             |

---

## Nmap Scan Output File Options

| Command                                        | Description                                                                           |
|------------------------------------------------|---------------------------------------------------------------------------------------|
| `-oN`                                         | Output in Normal format.                                                              |
| `-oX`                                         | Output in XML format.                                                                 |
| `-oS`                                         | Output in "Script Kiddie" (1337 speak) format.                                          |
| `-oG`                                         | Output in Grepable format.                                                              |
| `-oA BASENAME`                                | Output in all three major formats at once (Normal, XML, and Grepable).                    |
| `-v`                                          | Increase verbosity (use `-vv` or more for greater effect).                             |
| `-d`                                          | Increase debugging level (use `-dd` or more for greater effect).                         |
| `--reason`                                    | Display the reason for a port's state.                                                  |
| `--open`                                      | Only show open (or possibly open) ports.                                                 |
| `--packet-trace`                              | Show all packets sent and received.                                                     |
| `--iflist`                                    | List host interfaces and routes (useful for debugging).                                 |
| `--log-errors`                                | Log errors/warnings to the normal-format output file.                                   |
| `--append-output`                             | Append to output files rather than overwriting them.                                    |
| `--resume FILENAME`                           | Resume an aborted scan from the specified file.                                         |
| `--stylesheet PATH/URL`                       | Specify an XSL stylesheet to transform XML output to HTML.                              |
| `--webxml`                                    | Use the reference stylesheet from Nmap.Org for portable XML output.                      |
| `--no-stylesheet`                             | Prevent associating an XSL stylesheet with the XML output.                              |

---

## Misc Nmap Options

| Command                           | Description                                                                |
|-----------------------------------|----------------------------------------------------------------------------|
| `-6`                             | Enable IPv6 scanning.                                                      |
| `-A`                             | Enable OS detection, version detection, script scanning, and traceroute.   |
| `--datedir DIRNAME`              | Specify a custom Nmap data file location.                                  |
| `--send-eth`<br>`--send-ip`        | Send packets using raw Ethernet frames or IP packets.                      |
| `--privileged`                   | Assume the user is fully privileged.                                       |
| `--unprivileged`                 | Assume the user lacks raw socket privileges.                               |
| `-V`                             | Show the Nmap version number.                                              |
| `-h`                             | Display the Nmap help screen.                                              |

