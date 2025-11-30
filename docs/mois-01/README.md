# Mois 1 - Mes Fondations Linux

**Mon objectif ce mois-ci :** Apprendre à installer et configurer un serveur Linux proprement. À la fin du mois, je veux être capable de tout refaire de zéro les yeux fermés.

---

## Semaine 1 : J'installe mon serveur proprement

### Jour 1 - Je regarde ce que j'ai

**Aujourd'hui je vais :**
- Regarder mon matériel
- Décider comment organiser mes 3 disques
- Faire un plan sur papier

**Mes commandes :**

\`\`\`bash
# Je regarde mon processeur
lscpu
# Ça me dit combien de cœurs j'ai, la vitesse, etc.

# Je regarde ma RAM
free -h
# Le -h c'est pour que ce soit lisible (en GB)

# Je liste mes disques
lsblk
# Je vois : sda (931GB), sdb (279GB), sdc (279GB), sdd (279GB)

# Je regarde les partitions en détail
fdisk -l

# Je regarde l'utilisation actuelle
df -h
\`\`\`

**Ma décision pour les disques :**
- **sdb (279GB)** → Le système et les services
- **sdc (279GB)** → Les données et applications  
- **sdd (279GB)** → Les backups et mes VMs
- **sda (931GB)** → Je garde pour plus tard

**Ce que je dois comprendre :**
- C'est quoi une partition ? (un morceau de disque)
- C'est quoi LVM ? (ça rend les partitions flexibles)
- Pourquoi LVM ? (je peux agrandir après sans galérer)

**À la fin de la journée :**
- ✅ J'ai un schéma clair sur papier
- ✅ J'ai créé un fichier \`architecture-disques.md\`

---

### Jour 2 - Je réinstalle Ubuntu Server

**Aujourd'hui je vais :**
Réinstaller Ubuntu Server proprement avec un bon partitionnement

**Mon plan de partitionnement sur sdb :**

\`\`\`
/boot/efi : 1GB (pour démarrer en UEFI)
/boot : 2GB (le noyau Linux)
Le reste en LVM (276GB) :
  ├─ / (root) : 50GB → le système
  ├─ /var : 80GB → les logs et bases de données
  ├─ /tmp : 10GB → les fichiers temporaires
  ├─ swap : 16GB → la RAM sur disque
  └─ Libre : 120GB → pour agrandir plus tard
\`\`\`

**Les commandes LVM pendant l'install :**

\`\`\`bash
# Je prépare le disque pour LVM
pvcreate /dev/sdb2
# Je dis à LVM : "ce disque est pour toi"

# Je crée un gros conteneur
vgcreate vg_system /dev/sdb2
# vg = volume group = le gros conteneur

# Je crée mes partitions dedans
lvcreate -L 50G -n lv_root vg_system
# -L 50G = 50 Giga
# -n lv_root = je l'appelle "lv_root"

lvcreate -L 80G -n lv_var vg_system
lvcreate -L 10G -n lv_tmp vg_system
lvcreate -L 16G -n lv_swap vg_system
\`\`\`

**Important :** Je note TOUTES mes commandes dans un fichier !

---

### Jour 3 - Je configure mon système

**Aujourd'hui je vais :**
Configurer mon serveur fraîchement installé

**1. Je mets tout à jour :**

\`\`\`bash
# Je passe en root
sudo -i

# Je mets à jour
apt update && apt upgrade -y
\`\`\`

**2. Je donne un nom à mon serveur :**

\`\`\`bash
hostnamectl set-hostname lab-prod-01
\`\`\`

**3. Je mets une IP fixe :**

\`\`\`bash
nano /etc/netplan/01-netcfg.yaml
\`\`\`

\`\`\`yaml
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
\`\`\`

\`\`\`bash
netplan apply
\`\`\`

**4. J'installe mes outils :**

\`\`\`bash
apt install -y vim git curl wget htop net-tools tree tmux
\`\`\`

---

### Jour 4 - Je sécurise SSH

\`\`\`bash
# Sur mon PC, je crée mes clés
ssh-keygen -t ed25519

# Je copie ma clé sur le serveur
ssh-copy-id jaouad@192.168.1.100

# Sur le serveur, je durcis SSH
sudo nano /etc/ssh/sshd_config
\`\`\`

Je change :
- Port 2222
- PermitRootLogin no
- PasswordAuthentication no

\`\`\`bash
sudo systemctl restart sshd
\`\`\`

---

### Jour 5 - Je monte mon firewall

\`\`\`bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2222/tcp comment 'SSH'
sudo ufw enable
\`\`\`

---

### Jour 6 - Je gère les utilisateurs

\`\`\`bash
sudo adduser devops
sudo usermod -aG sudo devops
sudo groupadd webdev
\`\`\`

---

### Jour 7 - Je révise et je documente

Aujourd'hui je fais le point sur ma semaine et je prépare la suite !

---

## Semaine 2-4 : À documenter au fur et à mesure

Je vais apprendre :
- Systemd et les services
- Les scripts shell
- Les tâches cron
- La gestion des logs
- LVM avancé
- Le monitoring de base

*(Je complète en avançant)*
