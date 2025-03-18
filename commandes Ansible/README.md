Voici quelques **commandes Ansible** essentielles que tu peux utiliser pour gérer tes serveurs :

---

### 🔹 **Tester la connexion SSH avec Ansible (Ping)**
```bash
ansible all -m ping -i inventaire.ini
```
📌 **Explication** :
- `all` : cible tous les hôtes définis dans l’inventaire.
- `-m ping` : utilise le module `ping` pour tester la connexion.
- `-i inventaire.ini` : spécifie le fichier d’inventaire.

---

### 🔹 **Exécuter une commande sur des hôtes distants**
```bash
ansible all -m command -a "uptime" -i inventaire.ini
```
📌 **Explication** :
- `-m command` : exécute une commande shell simple.
- `-a "uptime"` : affiche le temps de fonctionnement du système.

⚠️ **Attention** : Le module `command` ne prend pas en charge la redirection (`>`, `|`, `&&`, etc.). Utilise `shell` si nécessaire :
```bash
ansible all -m shell -a "echo hello > /tmp/test.txt" -i inventaire.ini
```

---

### 🔹 **Copier un fichier vers un hôte distant**
```bash
ansible all -m copy -a "src=/home/user/file.txt dest=/tmp/file.txt" -i inventaire.ini
```
📌 **Explication** :
- `-m copy` : copie un fichier local vers l’hôte distant.
- `src=/home/user/file.txt` : chemin du fichier source.
- `dest=/tmp/file.txt` : emplacement de destination.

---

### 🔹 **Installer un package avec `apt` (Debian/Ubuntu)**
```bash
ansible web_servers -m apt -a "name=nginx state=present" -i inventaire.ini --become
```
📌 **Explication** :
- `web_servers` : exécute la commande sur le groupe `web_servers`.
- `-m apt` : utilise le module `apt` pour gérer les paquets.
- `name=nginx state=present` : installe `nginx` s’il n’est pas déjà présent.
- `--become` : exécute la commande avec les privilèges `sudo`.

---

### 🔹 **Gérer un service (exemple : redémarrer Apache)**
```bash
ansible web_servers -m service -a "name=apache2 state=restarted" -i inventaire.ini --become
```
📌 **Explication** :
- `-m service` : module pour gérer les services.
- `name=apache2` : nom du service.
- `state=restarted` : redémarre le service.

---

### 🔹 **Exécuter un playbook**
```bash
ansible-playbook -i inventaire.ini playbook.yml
```
📌 **Explication** :
- `ansible-playbook` : exécute un fichier Playbook YAML.
- `-i inventaire.ini` : spécifie l’inventaire.
- `playbook.yml` : fichier contenant les tâches à exécuter.
