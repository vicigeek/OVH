# 🔐 OVHcloud VPS  – Root Password Reset via Rescue Mode

This guide explains how to safely reset the **root password** of your dedicated server using **Rescue Mode** in OVHcloud.

---

## ✅ Summary Table – Step-by-Step

| **Step** | **Command / Action** | **Purpose / Explanation** |
|---------|-----------------------|----------------------------|
| 1️⃣ | Boot into **Rescue Mode** from OVHcloud panel | Starts a minimal OS to fix your real system |
| 2️⃣ | Receive credentials from email | Gives temporary SSH access to Rescue Mode |
| 3️⃣ | `ssh root@<server-ip>` | Connect to your server in Rescue Mode |
| 4️⃣ | `lsblk` | View attached disks and find your real OS partition (`/dev/sdb1`) |
| 5️⃣ | `mkdir /mnt/vm` | Create mount point for your real OS |
| 6️⃣ | `mount /dev/sdb1 /mnt/vm` | Mount the real Linux system's root partition |
| 7️⃣ | `mount --bind /dev /mnt/vm/dev` | Link device files (e.g., disk, tty) into your real system |
| 8️⃣ | `mount --bind /proc /mnt/vm/proc` | Link running process info into your real system |
| 9️⃣ | `mount --bind /sys /mnt/vm/sys` | Link system/kernel info into your real system |
| 🔟 | `chroot /mnt/vm` | Enter the real system as if it were booted |
| 1️⃣1️⃣ | `passwd` | Change the root password inside your real system |
| 1️⃣2️⃣ | `exit` | Exit the chroot (return to Rescue environment) |
| 1️⃣3️⃣ | `umount /mnt/vm/dev` | Unmount device bindings |
| 1️⃣4️⃣ | `umount /mnt/vm/proc` | Unmount process bindings |
| 1️⃣5️⃣ | `umount /mnt/vm/sys` | Unmount system bindings |
| 1️⃣6️⃣ | `umount /mnt/vm` | Unmount your real OS disk |
| 1️⃣7️⃣ | Reboot via OVHcloud panel | Boot back into your real system |
| 1️⃣8️⃣ | `ssh root@<server-ip>` (with new password) | Log in and confirm that the new root password works |

---

## 🛡️ Notes
- Always unmount safely to ensure all changes (like the new password) are flushed to disk.
- Use strong passwords.
- If `umount` fails, make sure you exited chroot, and use `umount -lf` if necessary.

---

## 🧰 Optional Script (for Rescue Mode)
```bash
# Run in rescue mode after identifying /dev/sdb1 as your real root partition
mkdir /mnt/vm
mount /dev/sdb1 /mnt/vm
mount --bind /dev /mnt/vm/dev
mount --bind /proc /mnt/vm/proc
mount --bind /sys /mnt/vm/sys
chroot /mnt/vm
passwd
# enter new password twice
exit
umount /mnt/vm/dev
umount /mnt/vm/proc
umount /mnt/vm/sys
umount /mnt/vm
