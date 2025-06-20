# **Simple Samba Server Setup on Ubuntu Server**  
*A quick and secure way to set up a Samba file server for sharing files between Linux and Windows machines.*  

---

## **📌 Prerequisites**  
- Ubuntu Server (22.04 LTS or later recommended)  
- `sudo` access  
- A static IP (recommended for stable access)  

---

## **🚀 Installation & Setup**  

### **1. Install Samba**  
```bash
sudo apt update && sudo apt install samba -y
```

### **2. Create a Shared Directory**  
```bash
sudo mkdir -p /samba/share
sudo chown -R nobody:nogroup /samba/share  # Allow anonymous access (adjust permissions as needed)
sudo chmod -R 0777 /samba/share  # Full read/write (adjust for security)
```

### **3. Configure Samba (`smb.conf`)**  
Edit the Samba config file:  
```bash
sudo nano /etc/samba/smb.conf
```  

#### **Add this at the end of the file:**  
```ini
[share]
   comment = Shared Folder
   path = /samba/share
   browsable = yes
   read only = no
   guest ok = yes  # Allow guest access (no password)
   create mask = 0777
   directory mask = 0777
```  
*(For private shares, replace `guest ok = yes` with `valid users = youruser` and set a password.)*  

### **4. Restart Samba**  
```bash
sudo systemctl restart smbd nmbd
sudo systemctl enable smbd nmbd
```

### **5. Open Firewall (if using `ufw`)**  
```bash
sudo ufw allow samba
sudo ufw enable
```

---

## **🔐 Adding a Secure User (Optional)**  
If you want password-protected access:  

### **1. Create a Samba User**  
```bash
sudo smbpasswd -a yourusername
```
*(Replace `yourusername` with an existing Linux user.)*  

### **2. Update `smb.conf` for Private Access**  
```ini
[private]
   comment = Secure Share
   path = /samba/private
   valid users = yourusername
   read only = no
   browsable = yes
```

---

## **🔌 Accessing the Share**  

### **From Linux**  
```bash
smbclient //your-server-ip/share -U username
```
*(Or mount permanently in `/etc/fstab`.)*  

### **From Windows**  
- Open **File Explorer** → `\\your-server-ip\share`  
- Enter credentials (if private share)  

---

## **🔧 Troubleshooting**  
- **Can’t access share?**  
  - Check firewall: `sudo ufw status`  
  - Verify Samba is running: `sudo systemctl status smbd`  
  - Check logs: `sudo tail -f /var/log/samba/log.smbd`  

- **Permission issues?**  
  - Ensure folder permissions: `sudo chmod -R 775 /samba/share`  

---

## **🎉 Done!**  
Now your friends can access files easily! 🚀  
