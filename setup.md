# 🚀 **NFS Server Setup on Ubuntu Server**  
*A simple, memorable guide to setting up an NFS server for sharing files across your network.*  

---

## **📌 Prerequisites**  
Before we start, ensure you have:  
✅ **Ubuntu Server** (22.04 LTS recommended)  
✅ **Root or sudo access**  
✅ **A stable network connection**  
✅ **At least two machines** (server + client)  

---

## **🔧 Step 1: Install NFS Server**  
Update your system and install the NFS kernel server:  

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install nfs-kernel-server -y
```

---

## **📂 Step 2: Create & Export a Shared Directory**  
Choose or create a directory to share (e.g., `/srv/nfs`):  

```bash
sudo mkdir -p /srv/nfs/share
sudo chown nobody:nogroup /srv/nfs/share  # Optional: Set permissions
```

Now, edit `/etc/exports` to define the shared directory and allowed clients:  

```bash
sudo nano /etc/exports
```
Add this line (replace `client_ip` with the client’s IP or subnet):  
```
/srv/nfs/share client_ip(rw,sync,no_subtree_check)
```
Example (allow all in subnet `192.168.1.0/24`):  
```
/srv/nfs/share 192.168.1.0/24(rw,sync,no_subtree_check)
```

---

## **⚡ Step 3: Apply NFS Exports & Start Services**  
Apply the changes and restart NFS:  

```bash
sudo exportfs -a  # Update exports
sudo systemctl restart nfs-kernel-server
```

Enable NFS on boot:  
```bash
sudo systemctl enable nfs-kernel-server
```

---

## **🔒 Step 4: Configure Firewall (Optional)**  
If using `ufw`, allow NFS traffic:  

```bash
sudo ufw allow from client_ip to any port nfs
sudo ufw enable
```

---

## **🖥️ Step 5: Client-Side Setup**  
On the **client machine**, install NFS client tools:  

```bash
sudo apt update && sudo apt install nfs-common -y
```

Create a mount point and mount the NFS share:  

```bash
sudo mkdir -p /mnt/nfs_share
sudo mount server_ip:/srv/nfs/share /mnt/nfs_share
```

Verify it worked:  
```bash
df -h | grep nfs
```

---

## **🔄 Auto-Mount at Boot (Client)**  
Edit `/etc/fstab` for persistent mounting:  

```bash
sudo nano /etc/fstab
```
Add this line (replace `server_ip`):  
```
server_ip:/srv/nfs/share  /mnt/nfs_share  nfs  defaults  0  0
```

Test with:  
```bash
sudo mount -a
```

---

## **🎉 Done!**  
Now you have a working NFS share between your server and client!  
