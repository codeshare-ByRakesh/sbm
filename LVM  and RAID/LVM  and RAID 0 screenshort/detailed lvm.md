Here is your detailed guide on setting up Logical Volume Management (LVM) on a Linux system, now enhanced with verification commands for each step:

---

### **Creating a Logical Volume Management (LVM) Setup**

LVM provides flexibility in managing disk storage by allowing you to create logical volumes from multiple physical disks. Below are the steps to create an LVM setup with verification for each action.

### **Pre-requisites:**
- A system with Linux installed (Ubuntu, CentOS, etc.)
- A root or sudo user to execute commands.

### **Steps to Create an LVM:**

#### 1. **Install LVM Packages (if not already installed):**

If the LVM package is not installed, you can install it using the following commands:

**On Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install lvm2
```

**On CentOS/RHEL:**
```bash
sudo yum install lvm2
```

#### 2. **Prepare the Disks:**
Ensure you have at least one unformatted disk or partition to create the LVM. To list available disks, use:

```bash
sudo lsblk
```

#### 3. **Create a Physical Volume (PV):**

To prepare a physical device (e.g., `/dev/sdb`) as a Physical Volume, use:

```bash
sudo pvcreate /dev/sdb
```

**Verify PV Creation:**
```bash
sudo pvdisplay
```
This command displays details about the physical volume, including the volume group it belongs to.

#### 4. **Create a Volume Group (VG):**

Now, create a Volume Group (VG) that pools together one or more physical volumes:

```bash
sudo vgcreate my_volume_group /dev/sdb
```

**Verify VG Creation:**
```bash
sudo vgdisplay
```
This will show the total space in the volume group, free space, and associated physical volumes.

#### 5. **Create a Logical Volume (LV):**

Create a Logical Volume (LV) of a specific size (e.g., 10GB) inside the volume group:

```bash
sudo lvcreate -n my_logical_volume -L 10G my_volume_group
```

**Verify LV Creation:**
```bash
sudo lvdisplay
```
This command shows details about the logical volume, including its size and associated volume group.

#### 6. **Create a Filesystem on the Logical Volume:**

Create a filesystem (e.g., `ext4`) on the logical volume:

```bash
sudo mkfs.ext4 /dev/my_volume_group/my_logical_volume
```

**Verify Filesystem Creation:**
```bash
sudo blkid /dev/my_volume_group/my_logical_volume
```
This command will show the filesystem type on the logical volume (e.g., `ext4`).

#### 7. **Mount the Logical Volume:**

To use the logical volume, you need to mount it to a directory. First, create a mount point:

```bash
sudo mkdir /mnt/my_lvm
```

Now, mount the logical volume:

```bash
sudo mount /dev/my_volume_group/my_logical_volume /mnt/my_lvm
```

**Verify Mount:**
```bash
df -h
```
This will display disk usage for mounted filesystems, confirming that your logical volume is successfully mounted.

Alternatively, use:

```bash
mount | grep /mnt/my_lvm
```
This checks if the logical volume is mounted at the desired point.

#### 8. **Make the Mount Persistent:**

To automatically mount the logical volume after a reboot, add an entry to the `/etc/fstab` file:

```bash
sudo nano /etc/fstab
```

Add the following line:

```bash
/dev/my_volume_group/my_logical_volume  /mnt/my_lvm  ext4  defaults  0  0
```

**Verify fstab Entry:**
```bash
cat /etc/fstab
```
Ensure the logical volume is listed in `/etc/fstab` for automatic mounting.

#### 9. **(Optional) Extend the Logical Volume:**

If you need to increase the size of the logical volume (e.g., to 20GB), use:

```bash
sudo lvextend -L 20G /dev/my_volume_group/my_logical_volume
```

After extending, resize the filesystem:

```bash
sudo resize2fs /dev/my_volume_group/my_logical_volume
```

**Verify LV Size Change:**
```bash
sudo lvdisplay /dev/my_volume_group/my_logical_volume
```

**Verify Filesystem Size:**
```bash
df -h
```

This will show the new logical volume size and filesystem size.

#### 10. **(Optional) Remove the Logical Volume:**

If you want to remove the logical volume, unmount it first:

```bash
sudo umount /mnt/my_lvm
```

Then, remove the logical volume:

```bash
sudo lvremove /dev/my_volume_group/my_logical_volume
```

You will be prompted for confirmation.

#### 11. **(Optional) Remove the Volume Group:**

To remove the volume group, use:

```bash
sudo vgremove my_volume_group
```

#### 12. **(Optional) Remove the Physical Volume:**

Finally, you can remove the physical volume:

```bash
sudo pvremove /dev/sdb
```

---

### **Summary of Commands:**

Here’s a summary of the key commands used throughout the process:

1. `sudo pvcreate /dev/sdb` — Create a physical volume.
2. `sudo vgcreate my_volume_group /dev/sdb` — Create a volume group.
3. `sudo lvcreate -n my_logical_volume -L 10G my_volume_group` — Create a logical volume.
4. `sudo mkfs.ext4 /dev/my_volume_group/my_logical_volume` — Create a filesystem.
5. `sudo mount /dev/my_volume_group/my_logical_volume /mnt/my_lvm` — Mount the logical volume.
6. `sudo nano /etc/fstab` — Edit fstab for automatic mount.

---

### **Example Workflow with Verification:**

1. **Create Physical Volume:**
   ```bash
   sudo pvcreate /dev/sdb
   sudo pvdisplay  # Verify PV creation
   ```

2. **Create Volume Group:**
   ```bash
   sudo vgcreate my_volume_group /dev/sdb
   sudo vgdisplay  # Verify VG creation
   ```

3. **Create Logical Volume:**
   ```bash
   sudo lvcreate -n my_logical_volume -L 10G my_volume_group
   sudo lvdisplay  # Verify LV creation
   ```

4. **Create Filesystem:**
   ```bash
   sudo mkfs.ext4 /dev/my_volume_group/my_logical_volume
   sudo blkid /dev/my_volume_group/my_logical_volume  # Verify filesystem type
   ```

5. **Mount Logical Volume:**
   ```bash
   sudo mount /dev/my_volume_group/my_logical_volume /mnt/my_lvm
   sudo df -h  # Verify mount
   ```

6. **Make Mount Persistent (Verify fstab):**
   ```bash
   sudo nano /etc/fstab  # Verify fstab entry for persistent mount
   ```

7. **Extend Logical Volume (Optional):**
   ```bash
   sudo lvextend -L 20G /dev/my_volume_group/my_logical_volume
   sudo lvdisplay /dev/my_volume_group/my_logical_volume  # Verify LV size change
   sudo resize2fs /dev/my_volume_group/my_logical_volume  # Resize filesystem
   sudo df -h  # Verify filesystem size
   ```

8. **Unmount and Remove Logical Volume (Optional):**
   ```bash
   sudo umount /mnt/my_lvm
   sudo lvremove /dev/my_volume_group/my_logical_volume  # Remove LV
   sudo vgremove my_volume_group  # Remove VG
   sudo pvremove /dev/sdb  # Remove PV
   ```

These steps will help ensure the proper setup of LVM with verification after each action to confirm that everything works as expected.