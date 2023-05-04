---
description: >-
  Ici nous configuron votre debian car il nous manque pas mal de chose pour
  avoir un proxmox propre
---

# ⚙ Configuration

## Network

### source.list

Pour commancer comme dans toute bonne installation linux nous configuron les dépot et appliquon les mis a jours

Pour commencer on pass en root car sudo n'est pas présent par défaut sur debian

```bash
su -
```

on mets a jours les dépot

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

puis les mis a jours du system

```bash
apt update && apt upgrade -y
```

on ajoute d'aufice sudo avant de passer au reste

```bash
UTILISATEUR=gabriel

apt install -y sudo
echo "$UTILISATEUR ALL=(ALL) PASSWD: ALL" > /etc/sudoers.d/$UTILISATEUR
```

### Networking

Ici nous allons configuré les carte réseaux de magnière temporaire pour ne pas pérdre la connexion durent l'installation car proxmox change les packet réseaux

{% hint style="danger" %}
Attention cette étape est indispensable si vous ne la faite pas l'installation de votre proxmox vas casser votre system donc vous cerez obliger de repasser par la case formatage
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
importance ici c'est une dépendence de proxmox
{% endhint %}

```bash
serverIp=$(ip route get 8.8.8.8 | awk -F"src " 'NR==1{split($2,a," ");print a[1]}')
hostname=$(cat /etc/hostname)

sed -i "s/127.0.1.1/${serverIp}/g" /etc/hosts
sed -i "s/${hostname}/${hostname}.local ${hostname}/g" /etc/hosts
hostname --ip-address
```

puis on reboot

```bash
sudo reboot
```
