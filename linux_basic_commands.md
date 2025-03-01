# Linux Basic Commands

## 1. Create AWS EC2 Instance
- Launch an Amazon Linux EC2 instance and connect via command prompt.

---

## 2. Basic Commands for Regular User

### Listing & Navigation
```sh
ls         # List files and directories
ls -a      # Show hidden files
ll         # Detailed list with permissions
pwd        # Present working directory
cd <directory_name>  # Change directory
```

### File & Directory Operations
```sh
mkdir <directory_name>    # Create directory
rmdir <directory_name>    # Remove directory (-r for recursive)
touch <file_name>         # Create a file
rm <file_name>            # Remove file
mv <old_name> <new_name>  # Move or rename file
df                        # Display disk filesystem info
```

### File Content Management
```sh
cat <file_name>           # Display contents of a file
cat > <file_name>         # Add content to file
cat >> <file_name>        # Append content to file
```

### System & User Info
```sh
whoami     # Shows current username
date       # Display current date and timestamp
cal        # Show calendar
history    # Show command history
exit       # Exit current user
clear      # Clear terminal
```

### Network & Download
```sh
wget <http_link>   # Download file from internet
```

---

## 3. Basic Commands for Root User

### User & Group Management
```sh
sudo su -                 # Switch to root user
useradd <user_name>       # Add new user
passwd <user_name>        # Set password for user
chgrp <user_name> <path>  # Change group of file/directory
```

### File Permissions
```sh
chmod <permission> <path>  # Change file permissions
# Permission Values: Read(4), Write(2), Execute(1)
chmod 740 file_name       # Owner: read/write/execute, Group: read, Others: none
```

### Process Management
```sh
ps          # Display active processes
kill <PID>  # Kill process by PID
```

### SSH Key Management
```sh
ssh-keygen  # Create SSH key pair (stored in ~/.ssh)
```

### Service Management
```sh
systemctl start/stop/enable/disable/status <service_name>
# Example: systemctl start sshd
```

---

## 4. Adding Sudoers
```sh
useradd virat
passwd virat
tail /etc/passwd

whereis useradd   # Copy first path
whereis passwd    # Copy first path

vim /etc/sudoers  # Edit sudoers file
```
**Add the following lines after root:**
```sh
virat   ALL=(ALL)       /usr/sbin/useradd, /usr/bin/passwd, ! /usr/bin/passwd root
```
**Testing sudo permissions:**
```sh
su virat
sudo useradd rohit
sudo passwd rohit
sudo passwd root  # Should display a permission denied message
exit
```

---

## 5. Group Commands

### Group & User Management
```sh
groupadd devops       # Add new group
useradd mahek         # Add user mahek
useradd snehal        # Add user snehal
usermod -G devops mahek   # Add mahek to devops group
usermod -G devops snehal
```
```sh
tail /etc/group       # Check group details
groupadd cloud
usermod -aG cloud mahek  # Append user to another group
```

### Directory Ownership & Permissions
```sh
mkdir devops-practice
ll  # Check ownership
chgrp devops devops-practice   # Change group to devops
chown mahek devops-practice    # Change owner to mahek
chmod 740 devops-practice      # Set permissions (rwx for owner, r for group, none for others)
chmod o+t devops-practice      # Add sticky-bit (restrict deletion)
```

### Finding User Information
```sh
cat /etc/passwd | grep mahek
# Output: mahek:x:1003:1004::/home/mahek:/bin/bash
