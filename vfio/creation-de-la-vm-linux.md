---
description: Ici nous créons votre VM linux en passthrough
---

# 🖥 Création de la VM Linux

## Configuration de la machine

{% hint style="warning" %}
Pour votre interface graphique Gomne et KDE sont déconceigler car ne fonctione pas parfaitement avec barrier qui nous permetera de lier windows et linux
{% endhint %}

{% hint style="info" %}
Je vous déconceil de mettre un mots de passe car ce cera gènant pour la liéson windows linux notament pour votre clavier quand vous alez taper votre mots de passe
{% endhint %}

{% hint style="danger" %}
Celon votre IGPU la vm linux peut aussi avoir besoin de votre GPU donc comme Linux cera votre VM principale nous allons luis réserver le PCIE son de votre GPU comme paliatif et on utilisera ensuite un logiciel pour lier une carte son virtuel de votre windows sur votre linux
{% endhint %}

ici je concidère que vous savez créer une VM proxmox mais voici la configuration optimale

il est important aussi de faire votre installation windows sans votre passthrue car vous pouvez être géné par l'installation automatique des pilote

{% hint style="warning" %}
sur les carte graphque en ajoutant les pcie penser a cocher ROM BAR, PCI-Express et Primary GPU\
pour les carte audio seulement ROM BAR et PCI-Express
{% endhint %}

{% tabs %}
{% tab title="Toujours" %}
| Nom           | Valeur    |
| ------------- | --------- |
| OS            | Linux     |
| Machine       | Q35       |
| Disque        | VirtIO    |
| Carte réseaux | VirtIO    |
| CPU Mode      | Host      |
| BIOS          | OVMF UEFI |
{% endtab %}

{% tab title="Instalation" %}
| Nom             | Valeur |
| --------------- | ------ |
| Carte Graphique | VirtIO |
{% endtab %}

{% tab title="Post Installation" %}
| Nom             | Valeur                              | Présision |
| --------------- | ----------------------------------- | --------- |
| Carte Graphique | None                                |           |
| USB             | Tous vos USB sauf souris et clavier |           |
| PCIE            | Pour moi la Vega de mon IGPU        | vidéo     |
| PCIE            | Pour moi la Vega de mon IGPU        | Audio     |
| PCIE            | Pour moi ma RX 6600                 | Audio     |
{% endtab %}
{% endtabs %}
