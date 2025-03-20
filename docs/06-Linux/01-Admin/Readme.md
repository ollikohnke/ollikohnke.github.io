# Linux Administration - Quick Reference

### User Management

*   **Add user:**
    ```bash
    sudo adduser username
    ```
*   **Delete user:**
    ```bash
    sudo userdel username
    ```
*   **Delete user & home:**
    ```bash
    sudo userdel -r username
    ```
* **List all users**
    ```bash
    cat /etc/passwd
    ```
* **List all users with home directory**
    ```bash
    awk -F: '$3 >= 1000 && $7 != "/usr/sbin/nologin" {print $1}' /etc/passwd
    ```

### Group Management

*   **Add group:**
    ```bash
    sudo addgroup groupname
    ```
*   **Delete group:**
    ```bash
    sudo groupdel groupname
    ```
*   **Add user to group:**
    ```bash
    sudo usermod -a -G groupname username
    ```
*   **Remove user from group:**
    ```bash
    sudo gpasswd -d username groupname
    ```
*   **Check user's groups:**
    ```bash
    groups username
    ```
* **List all groups**
    ```bash
    cat /etc/group
    ```

### Password Management

*   **Change user password:**
    ```bash
    sudo passwd username
    ```
*   **Force password change on next login:**
    ```bash
    sudo chage -d 0 username
    ```
*   **Lock user account:**
    ```bash
    sudo passwd -l username
    ```
*   **Unlock user account:**
    ```bash
    sudo passwd -u username
    ```
* **Check password expiration**
    ```bash
    chage -l username
    ```

### File/Directory Ownership

*   **Change owner:**
    ```bash
    sudo chown newowner filename
    sudo chown newowner:newgroup filename
    sudo chown -R newowner:newgroup directoryname
    ```
*   **Change group:**
    ```bash
    sudo chgrp newgroup filename
    sudo chgrp -R newgroup directoryname
    ```

### File/Directory Permissions

*   **Symbolic permissions:**
    ```bash
    chmod u+rwx filename  # Owner: rwx
    chmod g+r filename    # Group: r
    chmod o-w filename    # Others: -w
    chmod a+x filename    # All: x
    ```
*   **Numeric permissions:**
    ```bash
    chmod 755 filename    # rwxr-xr-x
    chmod 644 filename    # rw-r--r--
    chmod 700 filename    # rwx------
    ```
*   **Recursive permissions:**
    ```bash
    chmod -R 755 directoryname
    ```

### Special Permissions

*   **Set SGID (group inheritance):**
    ```bash
    chmod g+s directoryname
    ```
*   **Set SUID (user inheritance):**
    ```bash
    chmod u+s filename
    ```
*   **Set sticky bit:**
    ```bash
    chmod +t directoryname
    ```

### Bash Configuration

*   **Edit bash profile:**
    ```bash
    nano ~/.bashrc
    ```
*   **Apply changes:**
    ```bash
    source ~/.bashrc
    ```

### Aliases

*   **Create alias (in `~/.bashrc`):**
    ```bash
    alias ll='ls -alF'
    alias ..='cd ..'
    alias ...='cd ../..'
    ```
*   **List aliases:**
    ```bash
    alias
    ```

### Process Management

*   **List processes (by name):**
    ```bash
    ps aux | grep processname
    ```
*   **List processes (by port):**
    ```bash
    netstat -tulpn | grep portnumber
    ss -tulpn | grep portnumber
    ```
*   **List all running processes:**
    ```bash
    top
    ```
*   **Kill process (by PID):**
    ```bash
    kill PID
    ```
*   **Force kill process (by PID):**
    ```bash
    kill -9 PID
    ```
*   **Kill process (by name):**
    ```bash
    killall processname
    ```
* **Show process tree**
    ```bash
    pstree
    ```

### SSH

*   **Connect to server:**
    ```bash
    ssh user@server_ip
    ```
*   **Copy file to server:**
    ```bash
    scp filename user@server_ip:/path/to/destination
    ```
*   **Copy file from server:**
    ```bash
    scp user@server_ip:/path/to/file /path/to/destination
    ```
*   **Generate SSH key pair:**
    ```bash
    ssh-keygen -t rsa -b 4096
    ```
*   **Copy public key to server:**
    ```bash
    ssh-copy-id user@server_ip
    ```
*   **Disable password auth (server):**
    *   Edit `/etc/ssh/sshd_config`: `PasswordAuthentication no`
    *   `sudo systemctl restart sshd`
*   **Change SSH port (server):**
    *   Edit `/etc/ssh/sshd_config`: `Port new_port`
    *   `sudo systemctl restart sshd`
*   **SSH config file (`~/.ssh/config`):**
    ```
    Host server_alias
        HostName server_ip
        User username
        Port port_number
        IdentityFile ~/.ssh/id_rsa
    ```
    *   Connect: `ssh server_alias`
* **Check SSH version**
    ```bash
    ssh -V
    ```

### System Information

* **Show kernel version**
    ```bash
    uname -r
    ```
* **Show OS version**
    ```bash
    cat /etc/os-release
    ```
* **Show uptime**
    ```bash
    uptime
    ```
* **Show memory usage**
    ```bash
    free -h
    ```
* **Show disk usage**
    ```bash
    df -h
    ```
* **Show current directory**
    ```bash
    pwd
    ```
* **List directory content**
    ```bash
    ls -alF
    ```
* **Change directory**
    ```bash
    cd /path/to/directory
    ```
* **Create directory**
    ```bash
    mkdir directoryname
    ```
* **Remove directory**
    ```bash
    rmdir directoryname
    ```
* **Create file**
    ```bash
    touch filename
    ```
* **Remove file**
    ```bash
    rm filename
    ```
* **Copy file**
    ```bash
    cp source destination
    ```
* **Move file**
    ```bash
    mv source destination
    ```
* **Show file content**
    ```bash
    cat filename
    less filename
    ```
* **Search for a file**
    ```bash
    find / -name filename
    ```
* **Search for a string in a file**
    ```bash
    grep "string" filename
    ```
* **Show network interfaces**
    ```bash
    ip a
    ```
* **Show network routes**
    ```bash
    ip r
    ```
* **Show network statistics**
    ```bash
    netstat -s
    ```
* **Show network connections**
    ```bash
    netstat -tulpn
    ```
* **Show network connections**
    ```bash
    ss -tulpn
    ```
* **Download a file**
    ```bash
    wget url
    ```
* **Ping a server**
    ```bash
    ping server_ip
    ```
* **Show current date and time**
    ```bash
    date
    ```
* **Show current user**
    ```bash
    whoami
    ```
* **Show all logged users**
    ```bash
    w
    ```
* **Show last logged users**
    ```bash
    last
    ```
* **Show history**
    ```bash
    history
    ```
* **Clear history**
    ```bash
    history -c
    ```
* **Show manual**
    ```bash
    man command
    ```
* **Show command help**
    ```bash
    command --help
    ```
* **Reboot the system**
    ```bash
    sudo reboot
    ```
* **Shutdown the system**
    ```bash
    sudo shutdown now
    ```
* **Update the system**
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```
* **Install a package**
    ```bash
    sudo apt install packagename
    ```
* **Remove a package**
    ```bash
    sudo apt remove packagename
    ```
* **Search for a package**
    ```bash
    apt search packagename
    ```
* **Show package information**
    ```bash
    apt show packagename
    ```
* **Show package list**
    ```bash
    apt list --installed
    ```
* **Show package dependencies**
    ```bash
    apt depends packagename
    ```
* **Show package reverse dependencies**
    ```bash
    apt rdepends packagename
    ```

**Key Changes:**

*   **Removed Redundant Explanations:**  No more "Replace with..." or explanations of obvious parts of the command.
*   **Concise Comments:** Comments are now very short and to the point (e.g., `# Owner: rwx`).
*   **More Commands:** Added a lot of new commands, especially in the system information section.
*   **Better Organization:** Grouped commands logically.
* **Added System Information section**
* **Added more commands to all sections**

This version is much more suitable for quick reference and should be a lot more helpful for you. I hope this is what you were looking for! Let me know if you have any other suggestions.
