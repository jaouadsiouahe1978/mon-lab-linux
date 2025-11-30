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

```bash
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
```

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
- ✅ J'ai créé un fichier `architecture-disques.md`

---

### Jour 2 - Je réinstalle Ubuntu Server

**Aujourd'hui je vais :**
Réinstaller Ubuntu Server proprement avec un bon partitionnement

**Mon plan de partitionnement sur sdb :**

```
/boot/efi : 1GB (pour démarrer en UEFI)
/boot : 2GB (le noyau Linux)
Le reste en LVM (276GB) :
  ├─ / (root) : 50GB → le système
  ├─ /var : 80GB → les logs et bases de données
  ├─ /tmp : 10GB → les fichiers temporaires
  ├─ swap : 16GB → la RAM sur disque
  └─ Libre : 120GB → pour agrandir plus tard
```

**Les commandes LVM pendant l'install :**

```bash
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
```

**Important :** Je note TOUTES mes commandes dans un fichier !

**Ce que je dois apprendre :**
- Comment marche GRUB (le bootloader)
- Pourquoi je sépare /var du reste

---

### Jour 3 - Je configure mon système

**Aujourd'hui je vais :**
Configurer mon serveur fraîchement installé

**1. Je mets tout à jour :**

```bash
# Je passe en root
sudo -i
# sudo = faire en super utilisateur
# -i = ouvrir un vrai shell root

# Je mets à jour la liste des paquets
apt update
# apt = gestionnaire de paquets Ubuntu

# Je mets à jour les logiciels
apt upgrade -y
# -y = dire oui automatiquement

# Ou les deux d'un coup
apt update && apt upgrade -y
# && = "et après tu fais ça"
```

**2. Je donne un nom à mon serveur :**

```bash
# Je regarde le nom actuel
hostname

# Je le change
hostnamectl set-hostname lab-prod-01
# lab-prod-01 = le nouveau nom que je choisis

# Je vérifie
hostname
```

**3. Je mets une IP fixe :**

```bash
# Je regarde mes interfaces réseau
ip addr show
# Je vais voir ens33 ou eth0 ou un truc comme ça

# J'édite la config réseau
nano /etc/netplan/01-netcfg.yaml
```

**Dans le fichier je mets :**

```yaml
network:
  version: 2
  ethernets:
    ens33:  # Je remplace par MON interface
      dhcp4: no  # Je désactive l'IP automatique
      addresses:
        - 192.168.1.100/24  # Mon IP fixe
      routes:
        - to: default
          via: 192.168.1.1  # Ma box
      nameservers:
        addresses:
          - 8.8.8.8  # DNS Google
          - 1.1.1.1  # DNS Cloudflare
```

**J'applique :**

```bash
# Je teste d'abord (pendant 120 secondes)
netplan try
# Si ça marche pas, ça revient en arrière tout seul

# C'est bon ? J'applique !
netplan apply

# Je vérifie
ip addr show
```

**4. J'installe mes outils de base :**

```bash
apt install -y \
  vim \           # Éditeur de texte costaud
  git \           # Pour versionner mon code
  curl \          # Télécharger des trucs
  wget \          # Pareil que curl
  htop \          # Voir mes processus (mieux que top)
  net-tools \     # Outils réseau
  tree \          # Voir l'arborescence des dossiers
  tmux            # Avoir plusieurs terminaux en un
```

**À la fin :**
- ✅ Mon système est à jour
- ✅ J'ai un nom de serveur
- ✅ J'ai une IP fixe qui marche
- ✅ J'ai mes outils installés

---

### Jour 4 - Je sécurise SSH

**Aujourd'hui je vais :**
Configurer SSH pour me connecter en sécurité

**1. Je crée mes clés SSH (sur mon PC, pas le serveur) :**

```bash
# Sur mon PC perso
ssh-keygen -t ed25519 -C "mon-email@exemple.com"
# ed25519 = type de clé super sécurisé
# Je laisse le chemin par défaut
# Je mets un BON mot de passe !
```

**2. Je copie ma clé sur le serveur :**

```bash
# Toujours sur mon PC
ssh-copy-id jaouad@192.168.1.100
# Ça copie ma clé publique sur le serveur
# Une dernière fois il me demande le mot de passe
```

**3. Je teste :**

```bash
ssh jaouad@192.168.1.100
# Si ça demande juste la passphrase de ma clé, c'est gagné !
```

**4. Je durcis SSH (sur le serveur) :**

```bash
sudo nano /etc/ssh/sshd_config
```

**Ce que je change :**

```bash
# Je change le port (optionnel)
Port 2222
# Pour éviter les robots qui scannent le port 22

# Je bloque root
PermitRootLogin no
# Personne ne peut se connecter en root direct

# Juste moi qui peux me connecter
AllowUsers jaouad

# Je force les clés SSH
PasswordAuthentication no
# Fini les mots de passe !

# Pas de mot de passe vide
PermitEmptyPasswords no

# 3 essais max
MaxAuthTries 3

# 30 secondes pour se connecter
LoginGraceTime 30
```

**5. Je redémarre SSH :**

```bash
# Je redémarre le service
sudo systemctl restart sshd

# Je vérifie que c'est actif
sudo systemctl status sshd

# IMPORTANT : J'ouvre un NOUVEAU terminal pour tester
# AVANT de fermer celui-ci !
# Si j'ai cassé un truc, j'ai encore une session ouverte
```

**Ce que j'ai appris :**
- Comment marche l'authentification par clé
- Pourquoi c'est mieux qu'un mot de passe

---

### Jour 5 - Je monte mon firewall

**Aujourd'hui je vais :**
Bloquer tout ce qui n'est pas nécessaire

**1. J'installe UFW :**

```bash
# Normalement déjà installé sur Ubuntu
sudo apt install ufw -y

# Je regarde le statut
sudo ufw status
# Normalement désactivé pour le moment
```

**2. Mes règles de base :**

```bash
# Par défaut : je BLOQUE tout ce qui arrive
sudo ufw default deny incoming

# Par défaut : j'AUTORISE tout ce qui part
sudo ufw default allow outgoing
```

**3. J'autorise SSH (AVANT d'activer !) :**

```bash
# Si j'ai changé le port en 2222
sudo ufw allow 2222/tcp comment 'SSH'

# Ou si je suis resté en 22
sudo ufw allow 22/tcp comment 'SSH'

# Je vérifie avant d'activer
sudo ufw show added
```

**4. J'active :**

```bash
# J'active le firewall
sudo ufw enable
# Il me demande confirmation

# Je vérifie
sudo ufw status verbose
```

**5. Pour plus tard, j'autorise HTTP et HTTPS :**

```bash
# HTTP
sudo ufw allow 80/tcp comment 'HTTP'

# HTTPS
sudo ufw allow 443/tcp comment 'HTTPS'

# Je recharge
sudo ufw reload

# Je vois toutes mes règles
sudo ufw status numbered
```

**Commandes utiles :**

```bash
# Supprimer une règle
sudo ufw delete 3

# Désactiver temporairement
sudo ufw disable

# Tout reset
sudo ufw reset
```

**Ce que j'ai appris :**
- Pourquoi un firewall c'est important
- TCP vs UDP

---

### Jour 6 - Je gère les utilisateurs

**Aujourd'hui je vais :**
Apprendre à créer des utilisateurs proprement

**1. Je crée un user :**

```bash
# Méthode simple
sudo adduser devops
# Ça demande un mot de passe et des infos

# Méthode rapide
sudo useradd -m -s /bin/bash devops
# -m = créer son dossier home
# -s /bin/bash = son shell par défaut

# Je lui mets un mot de passe
sudo passwd devops
```

**2. Je lui donne le droit sudo :**

```bash
# Je l'ajoute au groupe sudo
sudo usermod -aG sudo devops
# -aG = ajouter au groupe (sans enlever des autres groupes)

# Je vérifie
groups devops
# Je dois voir : devops sudo
```

**3. Je crée un groupe pour mon projet :**

```bash
# Je crée le groupe "webdev"
sudo groupadd webdev

# J'ajoute des users dedans
sudo usermod -aG webdev devops
sudo usermod -aG webdev jaouad
```

**4. Les commandes utiles :**

```bash
# Voir tous les users
cat /etc/passwd

# Voir tous les groupes
cat /etc/group

# Supprimer un user
sudo userdel devops

# Supprimer un user ET son home
sudo userdel -r devops

# Changer le shell d'un user
sudo chsh -s /bin/zsh devops
```

**Ce que j'ai appris :**
- Créer des users
- Les groupes et pourquoi c'est pratique

---

### Jour 7 - Je révise et je documente

**Aujourd'hui c'est dimanche, je fais le point :**

**Ce que j'ai fait cette semaine :**
1. ✅ Analysé mon matériel
2. ✅ Réinstallé Ubuntu avec LVM
3. ✅ Configuré réseau et hostname
4. ✅ Sécurisé SSH
5. ✅ Monté mon firewall
6. ✅ Appris les users et groupes

**Ce que je vais faire :**
- Relire toutes mes notes
- Tester que tout marche bien
- Mettre à jour mon GitHub
- Préparer la semaine 2

**Questions que je me pose encore :**
- Comment agrandir une partition LVM ?
- Comment monitorer mon serveur ?
- C'est quoi exactement systemd ?

**Ma to-do pour la semaine 2 :**
- Apprendre à gérer les services avec systemd
- Automatiser mes tâches
- Commencer les scripts shell

---

## Semaine 2 : À venir...

Je vais apprendre :
- Systemd en détail
- Mes premiers scripts
- Les tâches cron
- Les logs système

*(Je documente au fur et à mesure)*

---

**Note personnelle :** Cette première semaine était dense ! Mais je sens que je comprends mieux comment Linux fonctionne. La suite va être fun 🚀
