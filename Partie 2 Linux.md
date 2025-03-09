# 🐧 Administration Système Linux

## Table des matières
- [1. Gestion des utilisateurs](#1-gestion-des-utilisateurs)
- [2. Configuration SSH](#2-configuration-ssh)
- [3. Analyse du stockage](#3-analyse-du-stockage)
- [4. Sauvegardes](#4-sauvegardes)
- [5. Filtrage et analyse réseau](#5-filtrage-et-analyse-réseau)
- [6. Analyse des logs](#6-analyse-des-logs)

---

## 🪪 1. Gestion des utilisateurs

### Q.2.1.1 Création d'un compte personnel
```bash
sudo adduser julien
```
![Capture d’écran 2025-03-07 105228](https://github.com/user-attachments/assets/52c396d9-c5be-4791-afd8-94e2acb87f85)
- Suivre les instructions pour définir le mot de passe
- Remplir les informations demandées (nom complet, etc.)

### Q.2.1.2 Préconisations de sécurité
- Utiliser un mot de passe fort (12+ caractères)
- Mélanger majuscules, minuscules, chiffres et caractères spéciaux
- Attribuer les droits sudo uniquement si nécessaire :
```bash
sudo usermod -aG sudo julien
```

---

## 🔑 2. Configuration SSH

### Q.2.2.1 Désactivation de l'accès root
Éditer `/etc/ssh/sshd_config` :
```bash
PermitRootLogin no
```

![Capture d’écran 2025-03-07 111851](https://github.com/user-attachments/assets/55f9ea78-6d44-4443-bb58-54562bdc6097)


### Q.2.2.2 Restriction d'accès au compte personnel
Dans `/etc/ssh/sshd_config` :
```bash
AllowUsers julien
```

![Capture d’écran 2025-03-07 111959](https://github.com/user-attachments/assets/67f156c4-f281-4796-9da5-576c2443c4be)


### Q.2.2.3 Configuration de l'authentification par clé
1. Générer la paire de clés (sur la machine locale) :
```bash
ssh-keygen -t rsa -b 4096
```

![Capture d’écran 2025-03-07 113047](https://github.com/user-attachments/assets/d79ed937-5d14-4e3f-95dc-b693a9d48c90)


2. Copier la clé publique :
```bash
type $env:USERPROFILE\.ssh\id_rsa.pub | ssh user@server "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

![Capture d’écran 2025-03-07 113548](https://github.com/user-attachments/assets/cf052d3f-8147-4db5-97dc-eb6fb03dad56)

![Capture d’écran 2025-03-07 113712](https://github.com/user-attachments/assets/c903d869-4c5e-48c6-8ba8-a3e30ddf57ec)

Explications des différentes parties :

- type $env:USERPROFILE\.ssh\id_rsa.pub : lit le contenu de votre clé publique SSH (équivalent de cat sous Linux)
- $env:USERPROFILE : représente le chemin vers votre dossier utilisateur Windows
- ssh user@server : connexion SSH vers le serveur distant (remplacez user par votre nom d'utilisateur et server par l'adresse IP ou le nom d'hôte)
- mkdir -p ~/.ssh : crée le dossier .ssh s'il n'existe pas
- cat >> ~/.ssh/authorized_keys : ajoute la clé au fichier authorized_keys

3. Désactiver l'authentification par mot de passe dans `/etc/ssh/sshd_config` :
```bash
PasswordAuthentication no
PubkeyAuthentication yes
```

![Capture d’écran 2025-03-07 114925](https://github.com/user-attachments/assets/958102b9-7409-479e-9777-ac1192974204)


---

## 💽 3. Analyse du stockage

### Q.2.3.1 Systèmes de fichiers montés
```bash
df -hT
```
```bash
lsblk
```

![Capture d’écran 2025-03-07 120007](https://github.com/user-attachments/assets/c65c4e01-4da5-4f85-97b7-3b0040edbf63)

Résultats :
- udev (devtmpfs) sur /dev
- tmpfs sur /run
- /dev/mapper/cp3--vg-root (ext4) sur /
- tmpfs sur /dev/shm
- /dev/md0p1 (ext2) sur /boot

### Q.2.3.2 Types de stockage utilisés
- LVM pour le système principal
- RAID pour /boot
- Systèmes temporaires (tmpfs, devtmpfs)

### Q.2.3.3 Ajouter un nouveau disque de 8,00 Gio au serveur et réparer le volume RAID
-1. **Connecter un nouveau disque** :
   Ajoutez physiquement un nouveau disque au serveur

![Capture d’écran 2025-03-07 122033](https://github.com/user-attachments/assets/dcd869b1-0760-41fe-9180-208be65d4157)


2. **Créer une partition sur le nouveau disque** :
   Utilisez `fdisk` ou `parted` pour créer une partition sur le nouveau disque :
   ```bash
   sudo fdisk /dev/sdb
   ```
   - Créez une nouvelle partition primaire (type `Linux RAID`).
   - Sauvegardez et quittez.

![Capture d’écran 2025-03-07 163052](https://github.com/user-attachments/assets/b922b802-216b-4689-b334-680c419c79f9)

![Capture d’écran 2025-03-07 163153](https://github.com/user-attachments/assets/47eac164-e0c8-4f2e-8d8d-b36b011f2224)


3. **Ajouter le nouveau disque au RAID** :
   Ajoutez la nouvelle partition au RAID existant :
   ```bash
   sudo mdadm --add /dev/md0 /dev/sdb1
   ```

![Capture d’écran 2025-03-07 163304](https://github.com/user-attachments/assets/f0857208-2e3d-41bc-9f57-fcfa00f0b0c2)

4. **Vérifier la reconstruction** :
   Surveillez la reconstruction avec :
   ```bash
   cat /proc/mdstat
   ```


![Capture d’écran 2025-03-07 163356](https://github.com/user-attachments/assets/e1d932dd-6b2c-41bc-96a8-b1cb1a59f0a4)



---


### Q.2.3.4 Ajout volume logique LVM

#### Identifiez l'espace disponible dans le groupe de volumes LVM avec :
   ```bash
   vgdisplay
   ```

![Capture d’écran 2025-03-07 124917](https://github.com/user-attachments/assets/54a7c011-ed39-49f7-b00b-fa28ce9a2f28)


#### Création du volume logique
```bash
sudo lvcreate -L 2G -n storage cp3-vg
```

![Capture d’écran 2025-03-07 164004](https://github.com/user-attachments/assets/53eaba59-7bf1-40a7-a7ef-99a2f6d594e1)


#### Formatage
```bash
sudo mkfs.ext4 /dev/cp3-vg/storage
```

![Capture d’écran 2025-03-07 164103](https://github.com/user-attachments/assets/23b081f7-c2ae-4aa4-8bab-b7fd1064f027)

#### Montage
```bash
sudo mkdir -p /var/lib/bareos/storage
echo "/dev/cp3-vg/sauvegardes /var/lib/bareos/storage ext4 defaults 0 2" | sudo tee -a /etc/fstab
sudo mount -a
```
![Capture d’écran 2025-03-07 164621](https://github.com/user-attachments/assets/4f4b581b-fc9e-45c3-b378-b98fa3808c1e)

### Q.2.3.5 Espace disponible
Vérification avec :
```bash
sudo vgdisplay cp3-vg
```

![Capture d’écran 2025-03-07 124844](https://github.com/user-attachments/assets/7c69771b-26bb-4b26-bd1d-dfed6719dbd1)


---

## 🗃️ 4. Sauvegardes

### Q.2.4.1 Composants Bareos
- **bareos-dir** (Director) : Coordination des sauvegardes
- **bareos-sd** (Storage Daemon) : Gestion du stockage
- **bareos-fd** (File Daemon) : Client de sauvegarde

---

## 📡5. Filtrage et analyse réseau

### Q.2.5.1 Configuration nftables actuelle
```bash
sudo nft list ruleset
```
```nft
table inet inet_filter_table {
    chain in_chain {
        type filter hook input priority filter; policy drop;
        ct state established,related accept
        ct state invalid drop
        iifname "lo" accept
        tcp dport 22 accept
        ip protocol icmp accept
        ip6 nexthdr ipv6-icmp accept
    }
}
```

![Capture d’écran 2025-03-07 132855](https://github.com/user-attachments/assets/ce54cca2-d9cb-43da-a47a-2bac27e078c9)


### Q.2.5.2 Communications autorisées
- SSH (port 22)
- ICMP (ping)
- Interface loopback
- Connexions établies et liées

### Q.2.5.3 Communications interdites
- Tout autre trafic (policy drop)

### Q.2.5.4 Configuration Bareos
Configuration nftables complète :
```nft
#!/usr/sbin/nft -f

flush ruleset

table inet inet_filter_table {
    chain in_chain {
        type filter hook input priority filter; policy drop;
        
        # Connexions établies et liées
        ct state established,related accept
        ct state invalid drop
        
        # Loopback
        iifname "lo" accept
        
        # SSH
        tcp dport 22 accept
        
        # ICMP
        ip protocol icmp accept
        ip6 nexthdr ipv6-icmp accept
        
        # Bareos
        tcp dport { 9101-9103 } accept
    }
}
```

![Capture d’écran 2025-03-07 132801](https://github.com/user-attachments/assets/25f9488a-3673-4356-9013-6dd41b450723)


## 📰 6. Analyse des logs

### Q.2.6.1 Analyse des échecs de connexion
Commande utilisée :
```bash
sudo grep "Failed password" /var/log/auth.log | tail -n 10
```

![Capture d’écran 2025-03-07 134356](https://github.com/user-attachments/assets/919652bf-5d78-4bd2-b003-e078b0c1340b)


#### Résultats d'analyse
- **Source des connections** : IP 192.168.1.138
- **Utilisateurs ciblés** : 
  - julie (nom d'utilisateur invalide)
  - root (bloqué dans le fichier de conf)
  - wilder (bloqué par AllowUsers)
