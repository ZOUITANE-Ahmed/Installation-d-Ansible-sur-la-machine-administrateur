## **1. Installation d'Ansible sur la machine administrateur (Windows avec WSL ou Linux)**
### **Sur Linux (Ubuntu/Debian)**
```bash
sudo apt update
sudo apt install -y ansible
```
### **Sur Windows (avec WSL installé)**
1. Installe **WSL** avec Ubuntu :  
   ```powershell
   wsl --install -d Ubuntu
   ```
2. Ouvre **Ubuntu WSL** et installe Ansible :
   ```bash
   sudo apt update
   sudo apt install -y ansible
   ```

---

## **2. Configurer l'inventaire Ansible**
Dans le fichier `inventory`, ajoute ton serveur Ubuntu :

```ini
[dhcp_servers]
ServerUbuntu ansible_host=192.168.124.152 ansible_user=ubuntu1 ansible_ssh_private_key_file=~/.ssh/id_rsa ansible_become=True
```

**Vérifier la connexion SSH avec Ansible** :
```bash
ansible ServerUbuntu -m ping -i inventory --ask-become-pass
```
Si tout fonctionne, tu devrais voir `pong`.

---

## **3. Installer le serveur DHCP sur Ubuntu**
Crée un playbook `dhcp_setup.yml` :

```yaml
---
- name: Install and configure DHCP on Ubuntu
  hosts: dhcp_servers
  become: yes
  tasks:
    - name: Install ISC DHCP Server
      apt:
        name: isc-dhcp-server
        state: present

    - name: Configure DHCP server
      copy:
        dest: /etc/dhcp/dhcpd.conf
        content: |
          default-lease-time 600;
          max-lease-time 7200;
          authoritative;
          subnet 192.168.124.0 netmask 255.255.255.0 {
            range 192.168.124.100 192.168.124.200;
            option routers 192.168.124.1;
            option domain-name-servers 8.8.8.8, 8.8.4.4;
          }

    - name: Set DHCP server interface
      lineinfile:
        path: /etc/default/isc-dhcp-server
        regexp: '^INTERFACESv4='
        line: 'INTERFACESv4="eth0"'

    - name: Restart DHCP service
      systemd:
        name: isc-dhcp-server
        state: restarted
        enabled: yes
```

---

## **4. Exécuter le Playbook**
```bash
ansible-playbook -i inventory dhcp_setup.yml --ask-become-pass
```

---

## **5. Vérifier que le service DHCP fonctionne**
```bash
ansible ServerUbuntu -m command -a "systemctl status isc-dhcp-server" -i inventory --ask-become-pass
```

Si le service échoue, vérifie :
```bash
ansible ServerUbuntu -m command -a "journalctl -xeu isc-dhcp-server" -i inventory --ask-become-pass
```

---

## **6. Tester si des clients reçoivent une adresse IP**
```bash
ansible ServerUbuntu -m command -a "cat /var/lib/dhcp/dhcpd.leases" -i inventory --ask-become-pass
```

---

### **Si tu rencontres des erreurs, envoie-moi les logs `journalctl -xeu isc-dhcp-server` et la configuration actuelle pour que je puisse t'aider ! 🚀**
