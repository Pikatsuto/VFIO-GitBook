---
description: Ici nous créerons votre VM Windows en passthrough
---

# 🖥 Création de la VM Windows

## Configuration de la machine

{% hint style="danger" %}
Selon votre iGPU la machine virtuelle Linux peut aussi nécessiter votre GPU. Comme Linux sera votre VM principale, nous allons lui réserver dans un premier la carte son intégrée au GPU. Puis dans un second temps, la lier avec une carte son virtuelle générée par un logiciel installé sur la machine Windows.
{% endhint %}

{% hint style="danger" %}
Avec Virtio, Windows ne reconnaîtra pas votre disque dur à l'installation. Il est donc indispensable de monter un périphérique ISO supplémentaire avec le pilote pour le charger au moment du formatage de vos disque:\
[https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/?C=M;O=D](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/?C=M;O=D)
{% endhint %}

{% hint style="info" %}
Je vous déconseille de mettre un mot de passe car cela sera gênant pour la liaison Windows-Linux. Notamment au moment de taper votre mot de passe.
{% endhint %}

Ici, je considère que vous savez comment créer une VM proxmox, mais voici la configuration optimale.

Il est important aussi de faire votre installation Windows sans votre passthrough car vous pouvez être gêné par l'installation automatique des pilotes.

N'oubliez pas non plus d'installer les pilotes virtio présents dans votre ISO une fois Windows installé et lancé.

{% hint style="warning" %}
Quand vous ajoutez les PCIe pour les cartes graphiques, n'oubliez pas de cocher `ROM BAR`, `PCI-Express` et `Primary GPU`.

Quant aux cartes audios, cochez seulement `ROM BAR` et `PCI-Express`.
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

{% tab title="Installation" %}
| Nom             | Valeur |
| --------------- | ------ |
| Carte Graphique | VirtIO |
{% endtab %}

{% tab title="Post Installation" %}
| Nom             | Valeur              | Précision               |
| --------------- | ------------------- | ----------------------- |
| Carte Graphique | None                |                         |
| USB             | Souris / Clavier    |                         |
| PCIE            | Pour moi ma RX 6600 | seulement le PCIE vidéo |
{% endtab %}
{% endtabs %}
