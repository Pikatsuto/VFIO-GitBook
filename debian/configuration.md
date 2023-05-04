---
description: >-
  Ici nous configurons votre Debian pour avoir un Proxmox propre
---

# ⚙ Configuration

## Network

### sources.list

Pour commencer comme dans toute bonne installation Linux, nous configurons les dépôts APT et appliquons les mises à jour.

Pour commencer nous passons sur le compte root car sudo n'est pas présent par défaut sur Debian.

```bash
su -
```

Mise à jour de la liste des dépôts :

```bash
echo "deb http://deb.debian.org/debian bullseye main contrib non-free
deb-src http://deb.debian.org/debian bullseye main contrib non-free

deb http://deb.debian.org/debian-security/ bullseye-security main contrib non-free
deb-src http://deb.debian.org/debian-security/ bullseye-security main contrib non-free

deb http://deb.debian.org/debian bullseye-updates main contrib non-free
deb-src http://deb.debian.org/debian bullseye-updates main contrib non-free

deb http://deb.debian.org/debian  bullseye-backports main contrib non-free
deb-src http://deb.debian.org/debian  bullseye-backports main contrib non-free" > /etc/apt/sources.list
```

Mise à jour du système :

```bash
apt update && apt upgrade -y
```

Nous ajoutons en premier le package `sudo` et nous y ajoutons notre compte utilisateur.

```bash
UTILISATEUR=gabriel

apt install -y sudo
echo "$UTILISATEUR ALL=(ALL) PASSWD: ALL" > /etc/sudoers.d/$UTILISATEUR
```

### Networking

Ici nous allons configurer les cartes réseaux de manière temporaire pour ne pas perdre la connexion durant l'installation.

{% hint style="danger" %}
**Attention**, cette étape est indispensable. Si cette dernière est ignorée l'installation de Proxmox endommagera de manière irréversible le système d'exploitation et une réinstallation complète sera alors nécessaire.
{% endhint %}

```bash
serverInterface=$(ip -br a | grep $(ip route get 8.8.8.8 | awk -F"src " 'NR==1{split($2,a," ");print a[1]}') | cut -d " " -f 1)

echo "auto lo
iface lo inet loopback
# Interface Loopback

auto ${serverInterface}
iface ${serverInterface} inet dhcp
# Interface Physique
" > /etc/network/interfaces
```

### Changement du domaine local

{% hint style="warning" %}
**Important** : C'est une dépendance de Proxmox.
{% endhint %}

```bash
serverIp=$(ip route get 8.8.8.8 | awk -F"src " 'NR==1{split($2,a," ");print a[1]}')
hostname=$(cat /etc/hostname)

sed -i "s/127.0.1.1/${serverIp}/g" /etc/hosts
sed -i "s/${hostname}/${hostname}.local ${hostname}/g" /etc/hosts
hostname --ip-address
```

Redémarrage

```bash
sudo reboot
```
