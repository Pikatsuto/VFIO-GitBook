---
description: Ici nous convertission votre debian en proxmox
---

# ↔ Convertion Debian Proxmox

## Ajout des dépot Proxmox&#x20;

```bash
echo "deb [arch=amd64] http://download.proxmox.com/debian/pve bullseye pve-no-subscription" > /etc/apt/sources.list.d/pve-install-repo.list
wget https://enterprise.proxmox.com/debian/proxmox-release-bullseye.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg 
sha512sum /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg 
```

## Mise a jours complète

ici nous faison une mise a jours forcant les nouveau packet proposer par proxmox

```bash
apt update && apt full-upgrade -y
```

## Puis on ajout ne noyeau PVE

ici on change le noyeau par celuis que proxmox nous offre car il intègre KVM(Karnel Virtual Machine) en sont centre ce qui est beaucoup plus performant qu'une machine vurtuel classique et indispensable pour proxmox

```bash
apt install pve-kernel-5.15 -y
reboot
```

{% hint style="danger" %}
attention si le karnel ne s'est pas installer coréctement ne redémarer sous auquin prètext car la machine ne se lencera pas. il faudra revérifier toute les étape passé et relancer l'installation en vous assurent qu'elle sois valide
{% endhint %}

## Installation de l'environement de virtualisation

{% hint style="info" %}
Vous aurez une question que la configuration du server mail pendent l'installation vous pourez choisire "pas de configuration" car cette fonctionalité ne vous cera d'aucune utiliter pour un pc utilisateur
{% endhint %}

```bash
apt install proxmox-ve postfix open-iscsi -y
```

{% hint style="danger" %}
Même avertissement qu'au dessu sacher juste que l'éreur courante est sur l'ineterface réseaux car c'est ici que proxmox remplace les packet réseaux il est donc conceiller de faire cette command en physique et non en ssh au cas ou vous cerez déconécter
{% endhint %}

## Supréssion des ancien noyeaux

```bash
apt remove linux-image-amd64 'linux-image-5.10*' -y
apt remove os-prober -y
update-grub

reboot
```

## Puis la mise au propre des dépot

{% hint style="info" %}
cette étape est important car proxmox propose par défaut un dépaut payant dont vous ne voulez pas donc sa ne saire a rien de le laisser trainer
{% endhint %}

```bash
rm /etc/apt/sources.list.d/pve-install-repo.list
rm /etc/apt/sources.list.d/pve-enterprise.list

echo "deb http://download.proxmox.com/debian/pve bullseye pve-no-subscription" > /etc/apt/sources.list.d/pve-no-subscription.list
```

## Et configuration final du réseaux

{% hint style="danger" %}
Attiention il est indispensable d'avoir défini votre PC en address IP static sur le DHCP de votre box car proxmox n'aime pas du tout changer d'IP
{% endhint %}

ici on repace proxmox en dhcp mais dans une carte réseaux VMBR (un bridge) qui est détécter par proxmox comme utilisable pour des VM

```bash
serverInterface=$(ip -br a | grep $(ip route get 8.8.8.8 | awk -F"src " 'NR==1{split($2,a," ");print a[1]}') | cut -d " " -f 1)

echo "auto lo
iface lo inet loopback
# Interface Loopback

auto ${serverInterface}
iface ${serverInterface} inet manual
# Interface Physique

auto vmbr0
iface vmbr0 inet dhcp
        bridge-ports    ${serverInterface}
        bridge-stp      off
        bridge-fd       0
# Interface VMBR0" > /etc/network/interfaces

systemctl restart networking.service
```
