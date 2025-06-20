To **uninstall** or **remove** the NFS (Network File System) server from your Ubuntu server, follow these steps:

---

### **1. Stop NFS Services**
First, stop the running NFS services:
```bash
sudo systemctl stop nfs-server nfs-kernel-server rpcbind
sudo systemctl disable nfs-server nfs-kernel-server rpcbind
```

---

### **2. Uninstall NFS Packages**
Remove the NFS server and related packages:
```bash
sudo apt purge nfs-kernel-server nfs-common rpcbind
```

---

### **3. Remove NFS Configuration (Optional)**
If you want to delete NFS configuration files (e.g., `/etc/exports`):
```bash
sudo rm -f /etc/exports
```

---

### **4. Clean Up Dependencies**
Remove unnecessary dependencies:
```bash
sudo apt autoremove
```

---

### **5. Verify NFS is Removed**
Check if NFS services are no longer running:
```bash
sudo systemctl status nfs-server nfs-kernel-server rpcbind
```
(Should show `inactive (dead)` or `not found`.)

Also, check if packages are uninstalled:
```bash
dpkg -l | grep -E "nfs|rpcbind"
```
(No output means NFS is fully removed.)

---

### **6. Restart the Server (Optional)**
If needed, reboot to ensure all changes take effect:
```bash
sudo reboot
```

---

### **Additional Notes**
- If you manually added NFS shares in `/etc/exports`, they will be removed when you delete the file.
- If you used `ufw` (firewall), you may want to remove NFS-related rules:
  ```bash
  sudo ufw delete allow from <IP_RANGE> to any port nfs
  sudo ufw reload
  ```

This will completely remove the NFS server from your Ubuntu system.
