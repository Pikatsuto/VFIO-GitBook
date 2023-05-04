---
description: Ici nous convertirons votre Debian en Proxmox
---

# ↔ Conversion Debian Proxmox

## Ajout des dépôts APT Proxmox

```bash
echo "deb [arch=amd64] http://download.proxmox.com/debian/pve bullseye pve-no-subscription" > /etc/apt/sources.list.d/pve-install-repo.list
wget https://enterprise.proxmox.com/debian/proxmox-release-bullseye.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg 
sha512sum /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg 
```

## Mise à jour complète

Ici nous réalisons une mise à jour forçant l'installation les nouveaux packages proposés par Proxmox.

```bash
apt update && apt full-upgrade -y
```

## Puis on ajoute le noyau PVE

Ici on change le noyau d'origine de Debian par celui de Proxmox. Ce noyau intègre KVM (_Kernel Virtual Machine_) qui est beaucoup plus performant qu'une machine virtuelle classique (comme Virtualbox) et qui est indispensable pour Proxmox.

En effet, les logiciels qui proposent de créer des machines virtuelles comme Virtualbox sont appelées Hyperviseur de Type 2. Alors que KVM utilisé par Proxmox est un hyperviseur de Type 1. Ces derniers sont bien plus performants et stables.

```bash
apt install pve-kernel-5.15 -y
reboot
```

{% hint style="danger" %}
**Attention**, si le kernel ne s'est pas installé correctement, ne redémarrez sous aucun prétexte car après le redémarrage, la machine ne sera plus en mesure de se lancer (le kernel étant un composant indispensable au fonctionnement du système). Il faudra revérifier toutes les étapes précédentes et relancer l'installation en vous assurant que cette dernière se déroule sans problème.
{% endhint %}

## Installation de l'environnement de virtualisation

{% hint style="info" %}
Vous aurez une question pendant la configuration du server mail. Choisissez "pas de configuration" car cette fonctionnalité n'est pas utile pour un pc utilisateur.
{% endhint %}

```bash
apt install proxmox-ve postfix open-iscsi -y
```

{% hint style="danger" %}
Pendant l'installation, une erreur peut survenir car proxmox est entrain de réinstaller les pilotes réseaux. Il est donc vivement conseillé d'exécuter cette commande directement sur la machine et non via SSH, autrement vous serez déconnécté pendant le redémarrage des cartes réseaux.
{% endhint %}

## Suppression des anciens noyaux Linux

```bash
apt remove linux-image-amd64 'linux-image-5.10*' -y
apt remove os-prober -y
update-grub

reboot
```

## Puis la mise au propre des dépôts APT

{% hint style="info" %}
Cette étape est importante car Proxmox propose par défaut un dépôt payant qui n'est utile que si vous souscrivez à la licence payante de PVE.
{% endhint %}

```bash
rm /etc/apt/sources.list.d/pve-install-repo.list
rm /etc/apt/sources.list.d/pve-enterprise.list

echo "deb http://download.proxmox.com/debian/pve bullseye pve-no-subscription" > /etc/apt/sources.list.d/pve-no-subscription.list
```

## Et configuration finale du réseau

{% hint style="danger" %}
**Attention**, il est indispensable d'avoir défini votre PC en adresse IP statique sur le réseau de votre box (car Proxmox n'aime pas du tout changer d'IP).
{% endhint %}

Ici on ajoute une interface réseau en DHCP qui sera le réseau utilisé par Proxmox pour les VM (VMBR carte réseau bridge).

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
