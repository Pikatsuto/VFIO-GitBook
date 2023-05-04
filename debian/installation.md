---
description: >-
  Ici nous installons d'abord un Debian plutôt qu'un Proxmox pour des raisons de
  stabilité et de propreté.
---

# 💿 Installation

{% hint style="info" %}
Ici, je pars du principe que vous savez installer Debian sur une machine physique.
{% endhint %}

{% hint style="danger" %}
Vous aurez obligatoirement besoin d'un autre ordinateur pour pouvoir vous y connecter en SSH sur la machine Debian nouvellement installée.
{% endhint %}

## Partition

{% tabs %}
{% tab title="Disque Principal" %}
| Nom    | Stockage | Montage   |
| ------ | -------- | --------- |
| EFI    | 512M     | /boot/efi |
| SWAP   | 8G       | None      |
| SYSTEM | 16G      | /         |
| HOME   | 32G      | /home     |
| VAR    | 100%     | /var      |
{% endtab %}

{% tab title="Disque Secondaire" %}
{% hint style="warning" %}
**Attention** : Le formatage de ce disque se fera depuis Proxmox.
{% endhint %}

{% hint style="danger" %}
Si vous faites une configuration avec plusieurs disque (RAID), il faudra que les disques présent dans la même grappe aient la même taille (et la même vitesse).
{% endhint %}

| Partitionement | Nombre de Disque | Type         |
|----------------|------------------|--------------|
| ZFS            | 2-3              | RAID Mirroir |
| ZFS            | 4-6              | RAID 5       |
| LVM            | 1                | NORAID       |
{% endtab %}
{% endtabs %}

### Choix des fonctionnalités en fin d'installation

* SSH Server
* Utilitaires Usuels

{% hint style="warning" %}
Ne choisissez rien d'autre, car nous ne pourrons plus accéder au Debian physique une fois l'installation de Proxmox terminée.

Laissez l'interface graphique ou tout autres fonctionnalités cochées serait une grosse perte de ressources et de performances.
{% endhint %}
