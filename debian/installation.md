---
description: >-
  Ici nous installon d'abbord un debian plustot qu'un proxmox pour des raison de
  stabilité et de propreté
---

# 💿 Installation

{% hint style="info" %}
Ici je par du prinsipe que vous avez déja installer debian en physique sur votre ordinateur mais voici également quelque conceil:
{% endhint %}

{% hint style="danger" %}
Vous aurez aubligatoirement besoin d'un autre ordinateur pour accéder a votre ordinateur car nous deverons taper des commande en ssh
{% endhint %}

## Partition

{% tabs %}
{% tab title="Disque Principale" %}
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
Attention le formatage de ce si sera dans proxmox
{% endhint %}

{% hint style="danger" %}
Si vous faite une configuration multi disque il faut qu'il ai le même stockage et la même vitesse
{% endhint %}

| Partitionement | Nombre de Disque | Type         |
| -------------- | ---------------- | ------------ |
| ZFS            | 2-3              | RAID Mirroir |
| ZFS            | 4-6              | RAID 5       |
| LVM            | 1                | NORAID       |
{% endtab %}
{% endtabs %}

### Chois des package en fin d'installation

* SSH Server
* Utilitaire Usuel

{% hint style="warning" %}
Ne choisisez rien d'autre car nous ne pouron plus accéder au debian physique un fois l'installation terminer donc il serais une grosse perte de performance de laisser cocher d'autre chose comme une interface graphique par exemple
{% endhint %}
