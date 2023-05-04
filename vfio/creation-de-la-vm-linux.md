---
description: Ici nous créerons votre VM linux en passthrough
---

# 🖥 Création de la VM Linux

## Configuration de la machine

{% hint style="warning" %}
Pour les interfaces graphiques Gnome et KDE sont déconseillées, car elles ne fonctionnent pas parfaitement avec `barrier` qui nous permet de lier Windows et Linux.
{% endhint %}

{% hint style="info" %}
Je vous déconseille de mettre un mot de passe car cela sera gênant pour la liaison Windows-Linux. Notamment au moment de taper votre mot de passe.
{% endhint %}

{% hint style="danger" %}
Selon votre iGPU la machine virtuelle Linux peut aussi nécessiter votre GPU. Comme Linux sera votre VM principale, nous allons lui réserver dans un premier la carte son intégrée au GPU. Puis dans un second temps, la lier avec une carte son virtuelle générée par un logiciel installé sur la machine Windows.
{% endhint %}

Ici, je considère que vous savez comment créer une VM proxmox, mais voici la configuration optimale.

Il est important aussi de faire votre installation Windows sans votre passthrough car vous pouvez être gêné par l'installation automatique des pilotes.

{% hint style="warning" %}
Quand vous ajoutez les PCIe pour les cartes graphiques, n'oubliez pas de cocher `ROM BAR`, `PCI-Express` et `Primary GPU`.

Quant aux cartes audios, cochez seulement `ROM BAR` et `PCI-Express`.
{% endhint %}

{% tabs %}
{% tab title="Toujours" %}
| Nom           | Valeur    |
| ------------- | --------- |
| OS            | Linux     |
| Machine       | Q35       |
| Disque        | VirtIO    |
| Carte réseau  | VirtIO    |
| CPU Mode      | Host      |
| BIOS          | OVMF UEFI |
{% endtab %}

{% tab title="Instalation" %}
| Nom             | Valeur |
| --------------- | ------ |
| Carte Graphique | VirtIO |
{% endtab %}

{% tab title="Post Installation" %}
| Nom             | Valeur                              | Précision |
| --------------- | ----------------------------------- | --------- |
| Carte Graphique | None                                |           |
| USB             | Tous vos USB sauf souris et clavier |           |
| PCIE            | Pour moi la Vega de mon IGPU        | vidéo     |
| PCIE            | Pour moi la Vega de mon IGPU        | Audio     |
| PCIE            | Pour moi ma RX 6600                 | Audio     |
{% endtab %}
{% endtabs %}
