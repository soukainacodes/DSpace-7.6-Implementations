# 🚀 Switching Between Nginx and Apache for DSpace 7 and 8 [Pending to be tested in DSpace 8] 

This guide helps you **disable Nginx and enable Apache**, or **disable Apache and enable Nginx**, depending on your needs.

---

## 🔄 Switching from Nginx to Apache
If you are currently using **Nginx** and want to switch to **Apache**, follow these steps:

### **1️⃣ Stop and Disable Nginx**
```bash
sudo systemctl stop nginx
sudo systemctl disable nginx
sudo systemctl mask nginx
```
🔹 **This stops Nginx and prevents it from starting at boot.**

---

### **2️⃣ Install Apache (if not installed)**
```bash
sudo apt update
sudo apt install apache2 -y
```
🔹 **If it's already installed, move to the next step.**

---

### **3️⃣ Enable and Start Apache**
```bash
sudo systemctl unmask apache2
sudo systemctl enable apache2
sudo systemctl start apache2
```
Check if Apache is running:
```bash
sudo systemctl status apache2
```
🔹 **Apache should now be active.**

---

### **4️⃣ Enable Required Apache Modules**
```bash
sudo a2enmod proxy proxy_http ssl headers rewrite
sudo systemctl restart apache2
```
🔹 **These modules are needed for DSpace.**

---

### **5️⃣ Configure Apache for DSpace**
Create a new Apache configuration file for DSpace:
```bash
sudo nano /etc/apache2/sites-available/dspace.conf
```
Add the following:
```apache
<VirtualHost *:80>
    ServerName your-domain.com
    Redirect / https://your-domain.com
</VirtualHost>

<VirtualHost *:443>
    ServerName your-domain.com
    SSLEngine on
    SSLCertificateFile full/path/to/your-fullchain.pem
    SSLCertificateKeyFile full/path/to/your-privkey.pem

    ProxyPreserveHost On
    RequestHeader set X-Forwarded-Proto https
    RequestHeader set X-Forwarded-Host your-domain.com

    ProxyPass /server http://127.0.0.1:8080/server
    ProxyPassReverse /server http://127.0.0.1:8080/server

    ProxyPass / http://127.0.0.1:4000/
    ProxyPassReverse / http://127.0.0.1:4000/

    ErrorLog ${APACHE_LOG_DIR}/dspace-ssl-error.log
    CustomLog ${APACHE_LOG_DIR}/dspace-ssl-access.log combined
</VirtualHost>
```
Save and exit (`CTRL + X`, `Y`, `ENTER`).

Enable the site and restart Apache:
```bash
sudo a2ensite dspace.conf
sudo systemctl restart apache2
```
🔹 **Now, Apache is fully configured for DSpace.**

---

### **6️⃣ Configure Tomcat for AJP Support**
Open the `server.xml` file for Tomcat:
```bash
sudo nano /etc/tomcat9/server.xml
```
Find the existing **AJP Connector** section or add the following line inside the `<Service name="Catalina">` block:
```xml
<Connector protocol="AJP/1.3" address="::1" port="8009"
           redirectPort="8443" secretRequired="false" />
```
*Parameter secretRequired="false" is necessary when using Apache.*

Save and exit (`CTRL + X`, `Y`, `ENTER`).
Restart Tomcat:
```bash
sudo systemctl restart tomcat9
```
🔹 **This enables AJP support in Tomcat, which Apache can use for proxying.**

---

### **7️⃣ Verify Everything Works**
```bash
curl -I https://your-domain.com/server/api
```
If it returns **200 OK**, Apache is successfully serving DSpace.

---

## 🔄 Switching from Apache to Nginx
If you are currently using **Apache** and want to switch to **Nginx**, follow these steps:

### **1️⃣ Stop and Disable Apache**
```bash
sudo systemctl stop apache2
sudo systemctl disable apache2
sudo systemctl mask apache2
```
🔹 **This stops Apache and prevents it from starting at boot.**

---

### **2️⃣ Install Nginx (if not installed)**
```bash
sudo apt update
sudo apt install nginx -y
```
🔹 **If it's already installed, move to the next step.**

---

### **3️⃣ Enable and Start Nginx**
```bash
sudo systemctl unmask nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```
Check if Nginx is running:
```bash
sudo systemctl status nginx
```
🔹 **Nginx should now be active.**

---

### **4️⃣ Verify Everything Works**
```bash
curl -I https://your-domain.com/server/api
```
If it returns **200 OK**, Nginx is successfully serving DSpace.

---

## 🎯 **Quick Reference**
| **Action** | **Nginx** | **Apache** |
|------------|----------|-----------|
| Stop Service | `sudo systemctl stop nginx` | `sudo systemctl stop apache2` |
| Disable Service | `sudo systemctl disable nginx` | `sudo systemctl disable apache2` |
| Enable Service | `sudo systemctl enable nginx` | `sudo systemctl enable apache2` |
| Start Service | `sudo systemctl start nginx` | `sudo systemctl start apache2` |
| Restart Service | `sudo systemctl restart nginx` | `sudo systemctl restart apache2` |
| Check Status | `sudo systemctl status nginx` | `sudo systemctl status apache2` |
| Configuration File | `/etc/nginx/sites-available/dspace` | `/etc/apache2/sites-available/dspace.conf` |

---

## 🙏 Acknowledgments
This guide is based on contributions from various experts in the DSpace community. Special thanks to:

- **Matus Formanek, PhD** (University of Zilina, Slovakia) for preparing and testing the original guide for Ubuntu Server 24.04 LTS and DSpace 7.4/8.0. https://repozitar.fhv.uniza.sk/server/api/core/bitstreams/5bb1b4a1-1422-4cd7-b878-b30d1eecd98d/content
- **Mohammad S. AlMutairi** for assisting with numerous DSpace configuration issues.
- **Elvi S. Nemiz** for highlighting the importance of setting `secretRequired="false"` in the AJP Connector within `server.xml` to ensure proper communication between Tomcat and Apache using `mod_proxy_ajp`. https://wiki.lyrasis.org/display/DSDOC7x/Installing+DSpace#InstallingDSpace-BackendInstallation:~:text=Elvi%20S.%20Nemiz,8%22%20secretRequired%3D%22false%22/%3E
