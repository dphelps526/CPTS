# CCNA Study Site

## Git Commands

### Initialize Git

* `git init`
* `git add .`
* `git commit -m "comment"`

### Push Code
* `git remote add origin https://github.com/dphelps526/CCNA`
* `git branch -M main`
* `git push -u origin main`

### Clone Repo
* `git clone https://github.com/dphelps526/CCNA`

### Save and Push Changes
* `git add .`
* `git commit -m "Updated comment"`
* `git push origin main`

### Pull Latest Changes
* `git pull origin main`

## HTB VPN

### Connecting

* `sudo openvpn user.ovpn`

### Checking 
 
When successfully connected to the VPN, you'll see a tun0 ethernet adapter
 * `sudo openvpn user.ovpn`

netstat -rn will show you the networks accessible via the vpn
 * `netstat -rn`