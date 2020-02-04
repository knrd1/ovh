## Installing CentOS 8 from custom image on OVH VPS
This should work with any other ISO, however I tested only with CentOS 8.

### 1) Reboot VPS in rescue mode

### 2) Connect to Rescue Mode via SSH, unmount /dev/sdb1 and remove all /dev/sdbX partitions:

```
lsblk
umount /dev/sdb1
fdisk -u /dev/sdb
> d
> d
> w
```

### 3) Install required packages:

```
# apt install qemu-kvm
```
### 4) Download your ISO - usually Rescue Mode has ver little space so you may want to download NetInst/Minial ISO:

```
# wget http://mirror.ox.ac.uk/sites/mirror.centos.org/8.0.1905/isos/x86_64/CentOS-8-x86_64-1905-boot.iso
```
### 5) Run qemu (replace ISO location and vdisk name if required):

```
# qemu-system-x86_64 -netdev type=user,id=mynet0 -device virtio-net-pci,netdev=mynet0 -m 2048 -localtime -enable-kvm -drive index=0,media=disk,if=virtio,file=/dev/sdb -vga qxl -spice port=5900,addr=127.0.0.1,disable-ticketing -daemonize -cdrom /tmp/CentOS-8-x86_64-1905-boot.iso -boot d
```
### 6) Assuming your workstation is running Linux, forward a port through an SSH tunnel to your VPS:

```
# ssh -4 -v root@<your_vps_ip> -L 5900:localhost:5900
```
### 7) On your localhost, connect to SPICE running on local port 5900:

```
# remote-viewer
> spice://127.0.0.1?port=5900
```
### 8) Perform installation as usual.

Note: Since you downloaded Minimal/NetInst image, you need to configure source installation.
For CentOS 8 I used:
> a) http
> b) repository URL
> c) mirror.centos.org/centos/8/BaseOS/x86_64/os/

### 9) Reboot server and exit Rescue Mode.

### 10) Connect to your new instance running CentOS 8:

```
# cat /etc/redhat-release 
CentOS Linux release 8.0.1905 (Core) 
```
