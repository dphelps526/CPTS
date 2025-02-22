# Nmap Scripting Engine (NSE) Cheat Sheet

## Overview

The Nmap Scripting Engine (NSE) allows you to run Lua scripts to interact with services, discover vulnerabilities, and gather detailed information about a target. NSE scripts are organized into 14 categories:

| **Category** | **Description** |
|--------------|-----------------|
| **auth**     | Determines authentication credentials. |
| **broadcast**| Performs host discovery via broadcast and automatically feeds discovered hosts into other scans. |
| **brute**    | Attempts to log in to services by brute-forcing credentials. |
| **default**  | Scripts executed with the `-sC` option. |
| **discovery**| Gathers information about accessible services. |
| **dos**      | Tests services for denial-of-service vulnerabilities (use with caution). |
| **exploit**  | Attempts to exploit known vulnerabilities. |
| **external** | Uses external services to process data. |
| **fuzzer**   | Sends varied input to identify vulnerabilities and unexpected packet handling. |
| **intrusive**| Runs potentially disruptive scripts. |
| **malware**  | Checks if the target system is infected with malware. |
| **safe**     | Executes non-intrusive scripts that do not harm the target. |
| **version**  | Enhances service detection capabilities. |
| **vuln**     | Identifies specific vulnerabilities in services. |

## Defining NSE Scripts

### Default Scripts

Run the default NSE scripts using:

```sh
sudo nmap <target> -sC
```

### Specific Script Category

Run all scripts within a specific category:

```sh
sudo nmap <target> --script <category>
```
*Example:*

```sh
sudo nmap 10.129.2.28 --script vuln
```

### Defined (Specific) Scripts

Specify one or more scripts by name:

```sh
sudo nmap <target> --script <script1>,<script2>,...
```
*Example (targeting SMTP port 25):*

```sh
sudo nmap 10.129.2.28 -p 25 --script banner,smtp-commands
```

This command retrieves the SMTP banner and lists available SMTP commands from the target.

## Aggressive Scanning with NSE

The aggressive scan option (`-A`) combines several features:

- Service version detection (`-sV`)
- OS detection (`-O`)
- Traceroute (`--traceroute`)
- Default NSE scripts (`-sC`)

*Example:*

```sh
sudo nmap 10.129.2.28 -p 80 -A
```

This scan can reveal:

- Web server details (e.g., Apache version, HTTP headers, webpage title)
- Operating system guesses (e.g., various Linux versions)

## Vulnerability Assessment Example

To scan for vulnerabilities on HTTP port 80 using the vuln category:

```sh
sudo nmap 10.129.2.28 -p 80 -sV --script vuln
```
This command:

- Performs service version detection on port 80.
- Uses NSE scripts from the "vuln" category to check for known vulnerabilities (e.g., via `http-enum`, `vulners`, etc.).
- May reveal additional details like WordPress versions, HTTP header information, and potential CVE references.

## Reference

For more details on available NSE scripts and their categories, refer to the official documentation:
[https://nmap.org/nsedoc/index.html](https://nmap.org/nsedoc/index.html)
