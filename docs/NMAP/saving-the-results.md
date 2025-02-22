# Saving Nmap Results Cheat Sheet

## Overview

When running Nmap scans, it's important to save the results for later analysis, comparison, and reporting. Nmap supports saving output in three primary formats:

- **Normal Output (-oN):** Saves a human-readable report with a `.nmap` extension.
- **Grepable Output (-oG):** Saves a simplified report designed for parsing with tools like `grep` (uses the `.gnmap` extension).
- **XML Output (-oX):** Saves the scan in structured XML format (`.xml`), useful for further processing or generating HTML reports.

You can also use the **-oA** option to save the results in all three formats at once.

## Example Command

```sh
sudo nmap 10.129.2.28 -p- -oA target
```

**Explanation:**

- `10.129.2.28` – The target IP.
- `-p-` – Scans all ports.
- `-oA target` – Saves output as `target.nmap`, `target.gnmap`, and `target.xml` in the current directory.

## Reviewing the Output Files

- **Normal Output (`target.nmap`):** Contains the full scan report with detailed information.
- **Grepable Output (`target.gnmap`):** Contains a simplified format which is easier to search through (e.g., for open ports).
- **XML Output (`target.xml`):** Provides structured data for automated parsing or conversion.

## Converting XML Output to HTML

You can convert the XML output into a more readable HTML report using `xsltproc`:
```sh
xsltproc target.xml -o target.html
```

This creates an HTML file (`target.html`) that displays the scan results in a clear and structured format.
