# 🚀 **Samba Server Setup on Ubuntu Server**  
---

## **📌 Prerequisites**  
✅ **Ubuntu Server** (22.04 LTS or 24.04 LTS recommended)  
✅ **Root or sudo access**  
✅ **Stable network connection**  
✅ **List of users & groups** (for access control)  

---

## **🔧 Step 1: Install Samba**  
Update & install Samba:  

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install samba -y
```

Verify installation:  
```bash
sudo systemctl status smbd --no-pager
```

---

## **📂 Step 2: Configure Samba Shares**  

### **A. Create a Shared Directory**  
```bash
sudo mkdir -p /srv/company_shares/{hr,finance,projects}
sudo chmod -R 770 /srv/company_shares
sudo chown -R root:"company_users" /srv/company_shares  # Replace "company_users" with your group
```

### **B. Edit Samba Config (`/etc/samba/smb.conf`)**  
```bash
sudo nano /etc/samba/smb.conf
```

#### **Example Config (Add to the END of the file):**  
```ini
[global]
   workgroup = COMPANY
   server string = %h (Samba Server)
   security = user
   encrypt passwords = yes
   passdb backend = tdbsam

[HR]
   path = /srv/company_shares/hr
   valid users = @hr_team
   browseable = yes
   writable = yes
   create mask = 0770
   directory mask = 0770

[Finance]
   path = /srv/company_shares/finance
   valid users = @finance_team
   browseable = yes
   writable = yes
   read only = no

[Projects]
   path = /srv/company_shares/projects
   valid users = @tech_team
   browseable = yes
   writable = yes
```

### **C. Restart Samba**  
```bash
sudo systemctl restart smbd nmbd
sudo systemctl enable smbd nmbd
```

---

## **👥 Step 3: Manage Users & Permissions**  

### **A. Create Linux Users**  
```bash
sudo useradd -M -s /usr/sbin/nologin alice
sudo useradd -M -s /usr/sbin/nologin bob
```

### **B. Create Samba Passwords**  
```bash
sudo smbpasswd -a alice
sudo smbpasswd -a bob
```

### **C. Assign Users to Groups**  
```bash
sudo groupadd hr_team
sudo groupadd finance_team
sudo groupadd tech_team

sudo usermod -aG hr_team alice
sudo usermod -aG finance_team bob
```

### **D. Set Directory Permissions**  
```bash
sudo chgrp hr_team /srv/company_shares/hr
sudo chgrp finance_team /srv/company_shares/finance
sudo chmod 770 /srv/company_shares/*
```

---

## **🖥️ Step 4: Client Access (Windows/Linux/macOS)**  

### **A. Linux/macOS Clients**  
Install `cifs-utils`:  
```bash
sudo apt install cifs-utils -y  # Debian/Ubuntu
brew install cifs-utils         # macOS (if needed)
```

Mount the share:  
```bash
sudo mkdir -p /mnt/company_hr
sudo mount -t cifs //server_ip/HR /mnt/company_hr -o username=alice,password=yourpassword
```

### **B. Windows Clients**  
1. Open **File Explorer** → `\\server_ip\HR`  
2. Enter **Samba username & password**  

---

## **🔒 Step 5: Advanced Security (Optional)**  

### **A. Firewall Rules**  
```bash
sudo ufw allow samba
sudo ufw enable
```

### **B. Enable Samba Logs**  
Edit `/etc/samba/smb.conf`:  
```ini
[global]
   log file = /var/log/samba/log.%m
   max log size = 1000
```

Restart Samba:  
```bash
sudo systemctl restart smbd
```

---

## **📜 Step 6: Automount Shares at Boot (Linux Clients)**  
Edit `/etc/fstab`:  
```
//server_ip/HR  /mnt/company_hr  cifs  credentials=/etc/samba/creds_alice,uid=1000,gid=1000  0  0
```

Create credentials file (`/etc/samba/creds_alice`):  
```ini
username=alice
password=yourpassword
```

Set secure permissions:  
```bash
sudo chmod 600 /etc/samba/creds_alice
```

---

## **🎯 Best Practices for Company Use**  
✔ **Use Groups for Access Control** (easier management)  
✔ **Regularly Backup Samba Config** (`sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak`)  
✔ **Monitor Logs** (`tail -f /var/log/samba/log.*`)  
✔ **Disable Guest Access** (`map to guest = bad user` in `smb.conf`)  

---

## **🚨 Troubleshooting**  
🔹 **"Permission Denied"?** → Check `chmod` & `chown`  
🔹 **Can’t Connect?** → Verify `smbstatus` and firewall  
🔹 **Password Issues?** → Reset with `sudo smbpasswd username`  

---

