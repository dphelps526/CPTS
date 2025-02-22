# Common Tools

## SSH
Secure Shell (SSH) is a network protocol that runs on port 22 by default. We can use SSH to login remotely to the server by using the username @ the remote server IP, as follows:

``` bash
ssh username@ipaddress
ssh bob@10.60.15.1
```

## Netcat
Netcat, ncat, or nc, is an excellent network utility for interacting with TCP/UDP ports. Its primary usage is for connecting to shells. For example, SSH is programmed to handle connections over port 22 to send all data and keys. We can connect to TCP port 22 with netcat:

``` bash
netcat 10.10.10.10 22
```

## Tmux
terminal multiplexer. Start tmux with:

``` bash
tmux
```

Then everytime you want to give a command to tmux, use CTRL + b first then

* `c` - New Window
* `Shift + %` - Split vertically
* `Shift + "` - Split vertically
* `<number>` - Switch windows
* `Shift + <up or down arrow>` - Choose which pane