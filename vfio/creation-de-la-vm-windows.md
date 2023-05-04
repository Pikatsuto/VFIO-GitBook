---
description: Ici nous créons votre VM windows en passthrough
---

# 🖥 Création de la VM Windows

## Configuration de la machine

{% hint style="danger" %}
Celon votre IGPU la vm linux peut aussi avoir besoin de votre GPU donc comme Linux cera votre VM principale nous allons luis réserver le PCIE son de votre GPU comme paliatif et on utilisera ensuite un logiciel pour lier une carte son virtuel de votre windows sur votre linux
{% endhint %}

{% hint style="danger" %}
Avec Virtio windows ne reconaitera pas votre disque a l'installation il est donc indispensable mon monté un périférique iso supplèmentaire avec le pilote pour le charger lors du formatage de vos disque:\
[https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/?C=M;O=D](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/?C=M;O=D)
{% endhint %}

{% hint style="info" %}
Je vous déconceil de mettre un mots de passe car ce cera gènant pour la liéson windows linux notament pour votre clavier quand vous alez taper votre mots de passe
{% endhint %}

ici je concidère que vous savez créer une VM proxmox mais voici la configuration optimale

il est important aussi de faire votre installation windows sans votre passthrue car vous pouvez être géné par l'installation automatique des pilote

n'oublier pas nonplus d'installer les pilote virtio présent dans votre ISO une fois windows installer et démarer

{% hint style="warning" %}
sur les carte graphque en ajoutant les pcie penser a cocher ROM BAR, PCI-Express et Primary GPU\
pour les carte audio seulement ROM BAR et PCI-Express
{% endhint %}

{% tabs %}
{% tab title="Toujours" %}
| Nom           | Valeur     |
| ------------- | ---------- |
| OS            | Windows 10 |
| Machine       | Q35        |
| Disque        | VirtIO     |
| Carte réseaux | VirtIO     |
| CPU Mode      | Host       |
| BIOS          | OVMF UEFI  |
{% endtab %}

{% tab title="Instalation" %}
| Nom             | Valeur |
| --------------- | ------ |
| Carte Graphique | VirtIO |
{% endtab %}

{% tab title="Post Installation" %}
| Nom             | Valeur              | Présision               |
| --------------- | ------------------- | ----------------------- |
| Carte Graphique | None                |                         |
| USB             | Sourie / Clavier    |                         |
| PCIE            | Pour moi ma RX 6600 | seulement le PCIE vidéo |
{% endtab %}
{% endtabs %}
