### **Fichiers `ansible.cfg` et `hosts` dans Ansible**  

Dans **Ansible**, les fichiers **`ansible.cfg`** et **`hosts`** sont essentiels pour configurer l'environnement et gérer l'**inventaire** des machines cibles. Voici une explication détaillée de leur rôle et de leur utilisation.

---

## **1. Fichier `ansible.cfg`**  
Le fichier **`ansible.cfg`** est le **fichier de configuration principal** d'Ansible. Il permet de définir des paramètres globaux qui affectent son fonctionnement. Ce fichier peut être placé :  
- **Dans le projet** (configuration spécifique au projet).  
- **Dans le répertoire personnel de l'utilisateur** (`~/.ansible.cfg`).  
- **Dans `/etc/ansible/ansible.cfg`** (configuration globale du système).  

### **Exemple de configuration `ansible.cfg`**  
```ini
[defaults]
inventory = ./hosts
host_key_checking = False
remote_user = root
become = true
become_method = sudo
```
### **Explication des paramètres courants** :  
- **`inventory`** : Définit l’emplacement du fichier contenant la liste des machines (par défaut `hosts`).  
- **`host_key_checking`** : Désactive la vérification de clé SSH pour éviter les avertissements (`False`).  
- **`remote_user`** : Spécifie l’utilisateur distant utilisé par Ansible.  
- **`become`** : Active l’élévation des privilèges (`sudo`).  
- **`become_method`** : Définit la méthode d’élévation (`sudo`, `su`, etc.).  

---

## **2. Fichier `hosts` (Inventaire Ansible)**  
Le fichier **`hosts`** (ou `inventory`) contient la liste des machines gérées par Ansible. Il est structuré en **groupes de machines** et permet d’attribuer des variables spécifiques à chaque hôte.

### **Exemple d’un inventaire `hosts`**  
```ini
[web_servers]
centos1 ansible_host=192.168.1.10 ansible_user=root
centos2 ansible_host=192.168.1.11 ansible_user=root

[db_servers]
ubuntu1 ansible_host=192.168.2.10 ansible_user=ubuntu ansible_become=true ansible_become_method=sudo
```
### **Explication des paramètres** :  
- **`ansible_host`** : Adresse IP ou nom DNS de la machine.  
- **`ansible_user`** : Utilisateur SSH utilisé pour la connexion.  
- **`ansible_become`** : Permet d’exécuter les commandes en tant qu’utilisateur privilégié (`sudo`).  
- **`ansible_become_method`** : Spécifie la méthode d’élévation (`sudo`, `su`, etc.).  

---

## **3. Tester l'inventaire avec Ansible**  
Une fois l’inventaire configuré, vous pouvez tester la connectivité avec les machines cibles via la commande :  
```bash
ansible all -m ping
```
Si tout est bien configuré, vous devriez voir une réponse `pong` de chaque machine.

---

## **4. Organisation avancée de l’inventaire**  
### **Utiliser des plages d’hôtes**  
Ansible permet d’utiliser des **plages d’hôtes** pour simplifier la gestion de plusieurs machines :  
```ini
[web_servers]
centos[1:3]  # Équivaut à centos1, centos2, centos3
```

### **Définir des variables globales pour un groupe**  
Il est possible de définir des variables pour un groupe d’hôtes en utilisant `[group_name:vars]` :  
```ini
[db_servers]
ubuntu1
ubuntu2

[db_servers:vars]
ansible_user=ubuntu
ansible_become=true
ansible_become_method=sudo
```
Cela évite de répéter les mêmes variables pour chaque machine.

---

## **5. Personnalisation de `ansible.cfg` dans un projet**  
Si vous avez plusieurs projets Ansible, vous pouvez **définir un `ansible.cfg` spécifique à chaque projet** dans son répertoire. Ansible utilisera ce fichier au lieu des paramètres globaux, ce qui permet une configuration plus flexible.
