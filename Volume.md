
# Disk Partitioning: Increase and Decrease Volume Size

**1. Create EC2 Instance**\
2\. Create volume > gp2 > 5 GiB > 100/3000 > Create > Name: data-disk\
3\. Select volume > Attach volume to the instance created > /dev/sdb\
4\. Open instance in terminal\
5\. `lsblk`\
6\. `df -h`\
7\. `mkfs.` `<tab> <tab>`\
8\. `mkfs.ext4 /dev/xvdb`\
9\. `mkdir /data`\
10\. `mount /dev/xvdb /data`\
11\. `blkid` (Copy UUID of xvdb and paste it below)\
12\. `vim /etc/fstab` {UUID=…. /data ext4 defaults 0 0}\
13\. `df -h`\
14\. `cd /data`\
15\. `touch mahek.txt`\
16\. `mount -a`\
17\. Reboot the instance and check if the data is mounted or not\
18\. `cd /data`\
19\. `ls`\
20\. `df -h`

## Increase Volume Size

21. Go to Volume > Actions > Modify Volume > Change size to **10 GiB**
22. `resize2fs /dev/xvdb`
23. `df -h`

## Decrease Volume Size

24. We cannot decrease volume size directly. Instead, create a new volume of desired decreased size, move the contents, and delete the old volume:
25. Create new volume with **4 GiB** > Attach volume > `/dev/sdc`
26. `lsblk`
27. `df -h`
28. `mkfs.ext4 /dev/xvdc`
29. `mkdir mahek-data`
30. `mount /dev/xvdc /mahek-data`
31. `cd /data`
32. `mv * /mahek-data/`
33. `blkid`
34. `vim /etc/fstab` (Paste UUID of xvdc and erase UUID of xvdb)
35. `umount /data`
36. Reboot and check

## Modify Root Volume (/dev/xvda)

37. Modify volume from AWS
38. `df -h`
39. `xfs_growfs -d /dev/xvda1`
40. `growpart /dev/xvda 1`
41. `xfs_growfs -d /dev/xvda1`
42. `df -h`

