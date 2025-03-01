create instance 
create vol > gp2 > 5gib > 100/3000 >create > name: data-disk 
select vol > attach vol to instance created > /dev/sdb
open terminal 
lsblk
df -h
mkfs. <tab><tab>
mkfs.ext4 /dev/xvdb
mkdir /data
mount /dev/xvdb /data
blkid (copy uuid =.. /data /ext4 defaults 0 0)
vim /etc/fstab
df -h
cd /data
touch glenn.txt{1..5}

go to volume > action > action snapshot 
go to snapshot > action > copy snapshot > add desire region
go to selected region > create instance , create vol from the copied snapshot > attach it to the instance > /dev/sdb

open the created instance in terminal
mkdir /snap-data
mount /dev/xvdb /data
cd /snap-data
ls 
