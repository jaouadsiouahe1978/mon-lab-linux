# 🚀 Mon Voyage Linux - 365 Jours pour Devenir Admin Système

> **Mission :** Transformer 365 jours de pratique quotidienne sur infrastructure réelle en compétences DevOps solides et vérifiables.

---

## 👋 Salut !

Je m'appelle **Jaouad Siouahe** et voici mon journal d'apprentissage Linux. Je vais documenter chaque jour pendant un an tout ce que j'apprends sur l'administration système Linux.

📍 **Localisation :** Grenoble, France  
🎯 **Objectif :** Maîtriser la stack DevOps complète  
💼 **Parcours :** Admin Système → DevOps Engineer  

---

## 🎯 Pourquoi ce projet ?

J'ai un serveur **Dell PowerEdge T310** qui prend la poussière. Je veux apprendre à vraiment maîtriser Linux et construire une vraie infrastructure comme en production. 

Au lieu de juste lire des tutos, je vais **FAIRE** et **DOCUMENTER** tout, publiquement.

### Ce qui rend ce projet différent

✅ **Infrastructure physique réelle** - Pas de simulations, de vrais serveurs  
✅ **Documentation publique** - Transparence totale sur ma progression  
✅ **Apprentissage en action** - Je construis, je casse, je répare  
✅ **Portfolio vivant** - Preuves concrètes de mes compétences  

---

## 💻 Mon Matériel - Dell PowerEdge T310

| Composant | Spécifications | Utilisation Prévue |
|-----------|----------------|-------------------|
| **Serveur** | Dell PowerEdge T310 (Gen 11) | Lab production-ready |
| **CPU** | Intel Xeon | Calcul pour VMs/conteneurs |
| **RAM** | 16 GB DDR3 ECC | ~10-15 VMs ou conteneurs |
| **Réseau** | 2× Gigabit Ethernet | Bonding/Failover |
| **Stockage** | **Total : 1.7 TB** | Configuration détaillée ⬇️ |

### Configuration Disques Détaillée

```
💿 sda : 931 GB     → LVM pour machines virtuelles (prévu)
💿 sdb : 279 GB     → Système Ubuntu 24.04 actuel
   ├─ sdb1 : 1 GB   → Boot EFI
   └─ sdb2 : 278 GB → Système (250 GB libres !)
💿 sdc : 279 GB     → Données persistantes (bases de données)
💿 sdd : 279 GB     → Backups automatiques et snapshots
```

**État actuel :**  
✅ sdb installé et fonctionnel (4% utilisé)  
🔜 sda/sdc/sdd vierges, prêts pour LVM/RAID

### OS et Configuration Réseau

**Système d'exploitation :** Ubuntu 24.04.1 LTS (Noble Numbat)  
**Kernel :** Linux (version récente)  
**Réseau :**
- eno1 : 192.168.1.135/24 (interface primaire)
- eno2 : 192.168.1.136/24 (interface secondaire)

**Décision à prendre :** Migrer vers Rocky Linux ou rester sur Ubuntu ? (Jour 2)

---

## 🧠 Mon Niveau de Départ (Honnêtement)

Je ne suis **pas débutant total**, mais je ne maîtrise **pas tout non plus**.

### ✅ Ce que je connais déjà
- Commandes Linux de base (ls, cd, cp, mv, grep)
- Installation d'Ubuntu Server
- Connexion SSH
- apt-get pour installer des packages
- Notions de réseau (IP, masque, gateway)

### ❌ Ce qui me manque (et que je vais apprendre)
- LVM et gestion avancée des disques
- Sécurisation complète d'un serveur
- Ansible et automatisation
- Docker et Kubernetes en profondeur
- Monitoring avec Prometheus/Grafana
- CI/CD avec GitLab
- Haute disponibilité et load balancing

**Ce projet comble ces trous, un jour à la fois.**

---

## 🗓️ Planning Détaillé - 12 Mois

### 📅 Mois 1-2 : Les Fondations Linux Solides

**Objectif :** Maîtriser les bases indispensables avant d'aller plus loin.

**Semaine 1 : Audit & Préparation**
- [x] Jour 1 : Audit complet du serveur (30 novembre 2025) ✅
- [ ] Jour 2-3 : Installation Rocky Linux + LVM
- [ ] Jour 4-5 : Configuration RAID logiciel
- [ ] Jour 6-7 : Documentation infrastructure complète

**Semaine 2 : Sécurité & Réseau**
- Hardening SSH (clés, 2FA, fail2ban)
- Configuration firewall (iptables/nftables)
- VLANs et isolation réseau
- VPN avec WireGuard

**Semaine 3 : Scripting & Automatisation**
- Bash scripting avancé
- Scripts de monitoring
- Gestion des logs (rsyslog)
- Rotation et archivage automatiques

**Semaine 4 : Services Système**
- Systemd approfondi (units, timers)
- Cron jobs intelligents
- Gestion des packages avancée
- Compilation depuis les sources

---

### 📅 Mois 3-4 : Les Services de Base

**Objectif :** Monter une infrastructure web complète.

**Services Web**
- Nginx : configuration avancée
- Apache : comparaison et use cases
- Reverse proxy et load balancing
- SSL/TLS avec Let's Encrypt

**Bases de Données**
- PostgreSQL : installation et tuning
- MariaDB/MySQL : réplication
- Redis : cache et sessions
- Backups automatiques

**Autres Services**
- Serveur DNS (Bind9 ou dnsmasq)
- Serveur Mail (Postfix + Dovecot)
- FTP/SFTP sécurisé
- NFS pour partage de fichiers

---

### 📅 Mois 5-6 : Monitoring & Observabilité

**Objectif :** Surveiller tout mon infrastructure.

**Stack Prometheus + Grafana**
- Installation et configuration
- Collecte de métriques système
- Dashboards personnalisés
- Alertes intelligentes avec AlertManager

**Stack ELK (Logging)**
- Elasticsearch pour stockage
- Logstash pour parsing
- Kibana pour visualisation
- Filebeat pour collecte

**Autres Outils**
- Nagios/Icinga pour checks classiques
- Netdata pour monitoring temps réel
- Uptime Kuma pour status pages

---

### 📅 Mois 7-8 : Automatisation Infrastructure

**Objectif :** "Infrastructure as Code" complet.

**Ansible**
- Playbooks : syntaxe et structure
- Roles et Galaxy
- Templates Jinja2
- Ansible Vault pour secrets
- Automatisation complète du lab

**Terraform**
- Providers et
