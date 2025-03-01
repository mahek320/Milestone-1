# AWS EBS Volume and Snapshot Management Guide

## Creating and Attaching an EBS Volume

1. **Create an Instance**
2. **Create a Volume**
   - Type: `gp2`
   - Size: `5 GiB`
   - IOPS: `100/3000`
   - Click **Create**
   - Name: `data-disk`
3. **Attach the Volume to the Instance**
   - Select the created volume
   - Click **Attach Volume**
   - Attach it to the instance at `/dev/sdb`

## Formatting and Mounting the Volume

1. Open the instance terminal and run:
   ```sh
   lsblk
   df -h
   ```
2. Format the volume:
   ```sh
   mkfs.<TAB><TAB>  # To check available filesystems
   mkfs.ext4 /dev/xvdb
   ```
3. Create a mount directory and mount the volume:
   ```sh
   mkdir /data
   mount /dev/xvdb /data
   ```
4. Make the mount persistent:
   ```sh
   blkid  # Copy the UUID of /dev/xvdb
   vim /etc/fstab
   ```
   Add the following entry:
   ```sh
   UUID=<copied-uuid> /data ext4 defaults 0 0
   ```
5. Verify:
   ```sh
   df -h
   ```
6. Create sample files:
   ```sh
   cd /data
   touch mahek.txt{1..5}
   ```

## Creating and Copying a Snapshot

1. **Create a Snapshot**
   - Go to **Volumes**
   - Select the volume
   - Click **Actions > Create Snapshot**
2. **Copy the Snapshot**
   - Go to **Snapshots**
   - Select the snapshot
   - Click **Actions > Copy Snapshot**
   - Choose the desired region

## Restoring from Snapshot in Another Region

1. Go to the selected region
2. **Create a Volume from the Copied Snapshot**
3. **Attach the Volume to a New Instance**
   - Attach as `/dev/sdb`
4. **Mount the Volume**
   ```sh
   mkdir /snap-data
   mount /dev/xvdb /snap-data
   ```
5. **Verify the Files**
   ```sh
   cd /snap-data
   ls
   ```

