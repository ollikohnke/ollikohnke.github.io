# Linux Networking

## Hostname

* **Viewing the Hostname:**
    * `hostname`: Displays the current hostname.
    * `hostnamectl`: Provides more detailed system information, including the hostname.

* **Setting the Hostname (permanently):**
    *  Edit `/etc/hostname` (replace the existing hostname with the new one).
    *  Edit `/etc/hosts` (update the hostname entry).
    *  Reboot the system or run `hostnamectl set-hostname new_hostname` for immediate effect (systemd).

* **Setting the Hostname (temporarily):**
    * `hostname new_hostname`: Sets the hostname for the current session only.


## IP Addresses and Gateway

* **Viewing IP Addresses:**
    * `ip addr show`: Displays all network interfaces and their IP addresses.
    * `ip route show`: Shows routing table, including default gateway.

* **Setting Static IP Addresses (example using `ip` command - requires root privileges):**
    * `sudo ip addr add 192.168.1.100/24 dev eth0`: Adds a static IP address to interface `eth0`.  (Replace `eth0` with your interface name).
    * `sudo ip link set eth0 up`: Brings the interface up.
    * `sudo ip route add default via 192.168.1.1`: Sets the default gateway. (Replace with your gateway IP).

* **Finding the Default Gateway:**
    * `ip route show default`: Shows the default gateway.


## Network Interfaces

* **Listing Interfaces:**
    * `ip link show`: Lists all network interfaces.

* **Bringing an Interface Up/Down (requires root privileges):**
    * `sudo ip link set eth0 up`: Brings interface `eth0` up.
    * `sudo ip link set eth0 down`: Brings interface `eth0` down.


## Bridging and NAT

* **Creating a Bridge (requires root privileges):**
    * `sudo brctl addbr br0`: Creates a bridge named `br0`.
    * `sudo ip link set br0 up`: Brings the bridge up.
    * `sudo brctl addif br0 eth0`: Adds interface `eth0` to the bridge.
    * `sudo brctl addif br0 eth1`: Adds interface `eth1` to the bridge.

* **Configuring NAT (using `iptables` - requires root privileges):**
    * `sudo iptables -t nat -A POSTROUTING -o <external_interface> -j MASQUERADE`: Enables NAT for the bridge.
    * `sudo iptables -A FORWARD -i <bridge_interface> -o <external_interface> -j ACCEPT`: Allows forwarding traffic.
    * `sudo iptables -A FORWARD -i <external_interface> -o <bridge_interface> -j ACCEPT`: Allows forwarding traffic in the opposite direction.
    * **Important:** `iptables` rules need to be saved persistently (method varies by distribution). Consider using `iptables-save` and `iptables-restore` or a tool like `netfilter-persistent`.

* **Viewing iptables rules:**
    * `sudo iptables -L -n -v`: Shows the current iptables rules.
    * `sudo iptables -t nat -L -n -v`: Shows the NAT iptables rules.

* **Deleting iptables rules (requires root privileges):**
    * `sudo iptables -F`: Flushes all iptables rules.
    * `sudo iptables -t nat -F`: Flushes all NAT iptables rules.
    * **Caution:** Flushing iptables rules can disrupt network connectivity.


## DNS Configuration

* **Viewing DNS Servers:**
    * `cat /etc/resolv.conf`: Shows the current DNS server configuration.
    * `systemd-resolve --status`: (systemd-resolved) Shows DNS configuration managed by systemd-resolved.

* **Setting DNS Servers (using `/etc/resolv.conf` - not recommended, may be overwritten):**
    * Edit `/etc/resolv.conf` and add the DNS server addresses.

* **Setting DNS Servers (using `systemd-resolved` - preferred method):**
    * Use `systemd-resolve` commands to manage DNS settings.


## Firewall Configuration (using UFW)

* **Viewing Firewall Status:**
    * `sudo ufw status`: Shows the firewall status (active or inactive).
    * `sudo ufw status numbered`: Shows the firewall rules with numbers.

* **Enabling/Disabling UFW:**
    * `sudo ufw enable`: Enables the firewall.
    * `sudo ufw disable`: Disables the firewall.

* **Allowing a Port:**
    * `sudo ufw allow 80/tcp`: Allows HTTP traffic on port 80.
    * `sudo ufw allow 443/tcp`: Allows HTTPS traffic on port 443.
    * `sudo ufw allow from 192.168.1.0/24`: Allows all traffic from the 192.168.1.0/24 subnet.
    * `sudo ufw allow in on eth0`: Allows all incoming traffic on eth0.
    * `sudo ufw allow out on eth0`: Allows all outgoing traffic on eth0.

* **Allowing a Service:**
    * `sudo ufw allow ssh`: Allows SSH traffic (usually port 22).
    * `sudo ufw allow http`: Allows HTTP traffic (usually port 80).
    * `sudo ufw allow https`: Allows HTTPS traffic (usually port 443).

* **Deleting a Rule:**
    * `sudo ufw delete allow 80/tcp`: Deletes the rule allowing port 80. (Use the rule number from `ufw status numbered` for more precision).

* **Deleting all rules:**
    * `sudo ufw reset`: Deletes all rules. (Use with caution!)


## Network Monitoring

* **`ss`:** A versatile command for displaying socket statistics. Useful for seeing active connections.
    * `ss -t -a`: Shows all TCP connections.
    * `ss -u -a`: Shows all UDP connections.

* **`netstat`:** (Older command, may not be available on all systems) Provides network statistics.

* **`tcpdump`:** A powerful packet capture tool. Use with caution as it can generate large files.

## Identifying Open Ports and Associated Processes

* **`ss` (again, very useful):**  `ss -tulnp` This command shows listening TCP sockets, their ports, and the process ID (PID) associated with each.  You can then use `ps aux | grep <PID>` to get more information about the process.

* **`lsof -i -P -n`:** This command lists all open files, including network sockets, without resolving names or ports.  It's particularly helpful for identifying processes listening on specific ports.  (Requires root privileges: `sudo lsof -i -P -n`)

* **`netstat -tulnp`:** (Older command, may not be available on all systems) Similar to `ss -tulnp`.


