
## **Azure VM Disk Mounting and Storage Expansion**

1. **Open Instance in Terminal**  
2. **List Block Devices**  
   ```sh
   lsblk
   ```  
3. **Check Disk Space**  
   ```sh
   df -h
   ```  
4. **Format the Disk**  
   ```sh
   mkfs. <tab> <tab>
   mkfs.ext4 /dev/xvdb
   ```  
5. **Create and Mount Directory**  
   ```sh
   mkdir /data
   mount /dev/xvdb /data
   ```  
6. **Get UUID of the Disk**  
   ```sh
   blkid
   ```  
7. **Edit fstab File and Add Entry**  
   ```sh
   vim /etc/fstab
   ```  
   Inside Vim, add the following:
   ```sh
   UUID=<your-uuid> /data ext4 defaults 0 0
   ```  
8. **Verify Mount and Create Test File**  
   ```sh
   df -h
   cd /data
   touch mahek.txt
   mount -a
   ```  
9. **Reboot Instance and Verify Mount**  
   ```sh
   reboot
   cd /data
   ls
   df -h
   ```  

### **Increase Volume Size**

10. **Modify Volume**  
    - Go to **Volumes** > **Actions** > **Modify Volume**  
    - Change Size to **10 GiB**  

11. **Resize Filesystem**  
    ```sh
    resize2fs /dev/xvdb
    df -h
    ```

