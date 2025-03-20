# Linux Administration

## Users

### Creating Users

*   **`useradd`:** The primary command for creating new user accounts.
    *   `useradd username`: Creates a basic user account.
    *   `useradd -m username`: Creates a user account and their home directory.
    *   `useradd -c "Full Name" username`: Adds a comment (full name) to the user account.
    *   `useradd -s /bin/bash username`: Specifies the user's login shell (e.g., Bash).
    *   `useradd -g groupname username`: Adds the user to a specific primary group.
    *   `useradd -G group1,group2 username`: Adds the user to multiple supplementary groups.
    *   `useradd -u 1001 username`: Assigns a specific UID to the user.
*   **`adduser`:** A more user-friendly interactive utility for creating users. It prompts for information like the user's full name and password.
    * `adduser username`: Creates a user and prompts for information.

### Modifying Users

*   **`usermod`:** Used to modify existing user accounts.
    *   `usermod -a -G groupname username`: Adds a user to a supplementary group without removing them from other groups.
    *   `usermod -g groupname username`: Changes the user's primary group.
    *   `usermod -l newusername oldusername`: Renames the user.
    *   `usermod -c "New Full Name" username`: Changes the user's comment.
    *   `usermod -s /bin/zsh username`: Changes the user's login shell.
    *   `usermod -L username`: Locks the user account (disables login).
    *   `usermod -U username`: Unlocks the user account.
    *   `usermod -d /new/home/directory -m username`: Changes the user's home directory and moves the contents.
* **`chage`**: Change user password expiry information
    * `chage -l username`: Lists the password expiry information
    * `chage -E YYYY-MM-DD username`: Sets the account expiry date
    * `chage -I days username`: Sets the number of days of inactivity before the account is locked.
    * `chage -m days username`: Sets the minimum number of days between password changes.
    * `chage -M days username`: Sets the maximum number of days a password is valid.
    * `chage -W days username`: Sets the number of days before password expiry that the user is warned.

### Deleting Users

*   **`userdel`:** Deletes user accounts.
    *   `userdel username`: Deletes the user account.
    *   `userdel -r username`: Deletes the user account and their home directory.

### Listing Users

* **`/etc/passwd`**: This file contains the list of users.
* **`id username`**: Displays the user's UID, GID, and groups.
* **`who`**: Displays who is currently logged in.
* **`w`**: Displays who is currently logged in and what they are doing.

## Groups

### Creating Groups

*   **`groupadd`:** Creates new groups.
    *   `groupadd groupname`: Creates a new group.
    *   `groupadd -g 1001 groupname`: Assigns a specific GID to the group.

### Modifying Groups

*   **`groupmod`:** Modifies existing groups.
    *   `groupmod -n newgroupname oldgroupname`: Renames a group.
    *   `groupmod -g 1002 groupname`: Changes the GID of a group.
* **`gpasswd`**: Administer /etc/group and /etc/gshadow
    * `gpasswd -a username groupname`: Adds a user to a group.
    * `gpasswd -d username groupname`: Removes a user from a group.
    * `gpasswd -M user1,user2,user3 groupname`: Sets the group membership to the specified users.

### Deleting Groups

*   **`groupdel`:** Deletes groups.
    *   `groupdel groupname`: Deletes a group.

### Listing Groups

* **`/etc/group`**: This file contains the list of groups.
* **`groups username`**: Displays the groups a user belongs to.

## UID and GID

### Understanding UID and GID

*   **UID (User ID):** A unique numerical identifier for each user.
    *   `0`: Reserved for the root user.
    *   `1-999`: Typically reserved for system users and services.
    *   `1000+`: Usually used for regular user accounts.
*   **GID (Group ID):** A unique numerical identifier for each group.
    *   `0`: Reserved for the root group.
    *   `1-999`: Typically reserved for system groups.
    *   `1000+`: Usually used for regular groups.

### Finding UID and GID

*   **`id`:** Displays the UID, GID, and groups of a user.
    *   `id username`: Shows the UID, GID, and groups for a specific user.
    *   `id`: Shows the UID, GID, and groups for the current user.
* **`/etc/passwd`**: This file contains the UID of each user.
* **`/etc/group`**: This file contains the GID of each group.

## User file permissions

### Understanding User Permissions

*   **Read (r):** Allows a user to read the contents of a file or list the contents of a directory.
*   **Write (w):** Allows a user to modify the contents of a file or add/remove files in a directory.
*   **Execute (x):** Allows a user to execute a file (if it's a program) or access a directory.

### Viewing Permissions

*   **`ls -l`:** Lists files and directories with detailed information, including permissions.
    *   The output shows permissions in the format: `drwxr-xr-x` (directory) or `-rw-r--r--` (file).
    *   The first character indicates the file type (`-` for file, `d` for directory, `l` for link).
    *   The next nine characters represent permissions for:
        *   **User (owner):** `rwx`
        *   **Group:** `r-x`
        *   **Others:** `r-x`

### Changing Permissions

*   **`chmod`:** Changes file permissions.
    *   **Symbolic mode:**
        *   `chmod u+x filename`: Adds execute permission for the user (owner).
        *   `chmod g-w filename`: Removes write permission for the group.
        *   `chmod o+r filename`: Adds read permission for others.
        *   `chmod a+r filename`: Adds read permission for all (user, group, others).
        *   `chmod u=rwx,g=rx,o=rx filename`: Sets specific permissions for user, group, and others.
    *   **Numeric mode:**
        *   `chmod 755 filename`: Sets permissions to `rwxr-xr-x` (user: rwx, group: rx, others: rx).
        *   `chmod 644 filename`: Sets permissions to `rw-r--r--` (user: rw, group: r, others: r).
        *   `chmod 700 filename`: Sets permissions to `rwx------` (user: rwx, group: none, others: none).
        *   `chmod 000 filename`: Sets permissions to `---------` (user: none, group: none, others: none).
        *   `4`: read
        * `2`: write
        * `1`: execute
        * `0`: no permission
        * Add them together to get the desired permission.

### Changing Ownership

*   **`chown`:** Changes the owner of a file or directory.
    *   `chown username filename`: Changes the owner to `username`.
    *   `chown username:groupname filename`: Changes the owner to `username` and the group to `groupname`.
    *   `chown -R username directory`: Recursively changes the owner of a directory and its contents.

## Group file permissions

### Understanding Group Permissions

*   Group permissions are the same as user permissions (read, write, execute) but apply to the group associated with the file or directory.

### Viewing Group Permissions

*   **`ls -l`:** The same command used to view user permissions also shows group permissions.
    *   The middle three characters in the permission string represent the group permissions.

### Changing Group Permissions

*   **`chmod`:** The same command used to change user permissions can also change group permissions.
    *   `chmod g+w filename`: Adds write permission for the group.
    *   `chmod g-x filename`: Removes execute permission for the group.
    *   `chmod 770 filename`: Sets permissions to `rwxrwx---` (user: rwx, group: rwx, others: none).

### Changing Group Ownership

*   **`chgrp`:** Changes the group ownership of a file or directory.
    *   `chgrp groupname filename`: Changes the group to `groupname`.
    *   `chgrp -R groupname directory`: Recursively changes the group of a directory and its contents.

## Inheritance

### Understanding Inheritance

*   **Default Permissions:** When a new file or directory is created, it inherits default permissions based on the user's `umask` setting.
*   **`umask`:** A command that sets the default permissions for newly created files and directories.
    *   `umask`: Displays the current umask value.
    *   `umask 022`: Sets the umask to 022 (default for many systems).
    *   `umask 007`: Sets the umask to 007 (more restrictive).
    * The umask is subtracted from 777 for directories and 666 for files.
    * `777 - 022 = 755` for directories.
    * `666 - 022 = 644` for files.
* **SetUID (SUID)**: If set on an executable, it will run with the permissions of the owner.
* **SetGID (SGID)**: If set on an executable, it will run with the permissions of the group. If set on a directory, new files will inherit the group of the directory.
* **Sticky Bit**: If set on a directory, only the owner of the file, the owner of the directory or root can delete or rename files in the directory.

### Special Permissions

*   **SetUID (SUID):**
    *   `chmod u+s filename`: Sets the SUID bit.
    *   `chmod u-s filename`: Removes the SUID bit.
    *   `chmod 4755 filename`: Sets the SUID bit and permissions to `rwxr-xr-x`.
*   **SetGID (SGID):**
    *   `chmod g+s filename`: Sets the SGID bit.
    *   `chmod g-s filename`: Removes the SGID bit.
    *   `chmod 2755 filename`: Sets the SGID bit and permissions to `rwxr-xr-x`.
*   **Sticky Bit:**
    *   `chmod +t directory`: Sets the sticky bit.
    *   `chmod -t directory`: Removes the sticky bit.
    *   `chmod 1755 directory`: Sets the sticky bit and permissions to `rwxr-xr-x`.

### Finding Files with Special Permissions

* **`find / -perm /4000`**: Find files with the SUID bit set.
* **`find / -perm /2000`**: Find files with the SGID bit set.
* **`find / -perm /1000`**: Find files with the sticky bit set.


