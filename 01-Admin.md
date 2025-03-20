# Linux Administration

## Users

### Creating Users
```bash
sudo useradd newuser # Create user 'newuser'
sudo useradd -m newuser # Create user with home directory
sudo useradd -s /bin/bash newuser # Create user with bash shell
sudo adduser newuser # Interactive user creation (Debian/Ubuntu)
```
### Modifying Users
```bash
sudo usermod -aG groupname username  # Add user to a group
sudo usermod -l newusername oldusername # Rename user
sudo usermod -d /new/home/dir -m username # Change home directory (and move contents)
sudo usermod -s /bin/zsh username # Change user's default shell
sudo usermod -e YYYY-MM-DD username # Set account expiration date
sudo passwd username # Change password for user
sudo passwd -l username # Lock user account
sudo passwd -u username # Unlock user account
```
### Deleting Users
```bash
sudo userdel username        # Delete user 'username'
sudo userdel -r username     # Delete user and their home directory
```
### Listing Users
```bash
cat /etc/passwd              # List all users (and system users)
getent passwd                # List all users (and system users)
compgen -u                   # List all users
id username                  # Show user's UID, GID, and groups
whoami                       # Show current user
w                            # Show who is logged on
```

## Groups

### Creating Groups
```bash
sudo groupadd newgroup       # Create group 'newgroup'
```
### Modifying Groups
```bash
sudo groupmod -n newgroupname oldgroupname # Rename group
sudo groupmod -g 1001 groupname # Change GID
```
### Deleting Groups
```bash
sudo groupdel groupname      # Delete group 'groupname'
```
### Listing Groups
```bash
cat /etc/group               # List all groups
getent group                 # List all groups
groups                       # List groups the current user belongs to
groups username              # List groups a specific user belongs to
```

## UID and GID

### Understanding UID and GID
```bash
# UID (User ID): Unique number identifying a user.
# GID (Group ID): Unique number identifying a group.
# 0: root
# 1-999: System users and groups
# 1000+: Regular users and groups (typically)
```
### Finding UID and GID
```bash
id username                  # Show UID, GID, and groups for 'username'
id                           # Show UID, GID, and groups for current user
grep username /etc/passwd    # Find UID for 'username'
grep groupname /etc/group    # Find GID for 'groupname'
```

## User file permissions

### Understanding User Permissions
```bash
# r (read): Allows viewing file content or listing directory contents.
# w (write): Allows modifying file content or creating/deleting files in a directory.
# x (execute): Allows running a file as a program or accessing a directory.
```
### Viewing Permissions
```bash
ls -l                        # Long listing, shows permissions
stat filename                # Detailed file information, including permissions
```
### Changing Permissions
```bash
chmod u+r filename           # Add read permission for the user
chmod u-w filename           # Remove write permission for the user
chmod u+x filename           # Add execute permission for the user
chmod g+w filename           # Add write permission for the group
chmod o-r filename           # Remove read permission for others
chmod 755 filename           # Set permissions: rwxr-xr-x (user: rwx, group: rx, others: rx)
chmod 644 filename           # Set permissions: rw-r--r-- (user: rw, group: r, others: r)
chmod +x filename            # Add execute permission for all
```
### Changing Ownership
```bash
sudo chown newuser filename  # Change file owner to 'newuser'
sudo chown newuser:newgroup filename # Change owner and group
sudo chown :newgroup filename # Change group only
sudo chown -R newuser:newgroup directory # Change owner and group recursively
```

## Group file permissions

### Understanding Group Permissions
```bash
# Group permissions apply to users who are members of the file's group.
# Same rwx meanings as user permissions.
```
### Viewing Group Permissions
```bash
ls -l                        # Long listing, shows group permissions
```
### Changing Group Permissions
```bash
chmod g+r filename           # Add read permission for the group
chmod g-w filename           # Remove write permission for the group
chmod g+x filename           # Add execute permission for the group
```
### Changing Group Ownership
```bash
sudo chgrp newgroup filename # Change file group to 'newgroup'
sudo chgrp -R newgroup directory # Change group recursively
```

## Inheritance

### Understanding Inheritance
```
# When a file or directory is created within a directory, it inherits
# the group ownership and permissions from the parent directory.
# Special permissions (setuid, setgid, sticky bit) can modify this behavior.
```

### Special Permissions
```
# setuid (s): If set on an executable, it runs with the owner's privileges.
# setgid (s): If set on an executable, it runs with the group's privileges.
#            If set on a directory, new files inherit the directory's group.
# sticky bit (t): If set on a directory, only the owner, root, or file owner can delete files.
```

### Finding Files with Special Permissions
```bash
find / -perm /u=s -type f 2>/dev/null # Find setuid files
find / -perm /g=s -type f 2>/dev/null # Find setgid files
find / -perm /o=t -type d 2>/dev/null # Find sticky bit directories
```

