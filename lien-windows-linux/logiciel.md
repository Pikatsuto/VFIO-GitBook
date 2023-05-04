---
description: Je vous propose des logiciels pour lier Windows et Linux
---

# 💽 Logiciel

## Barrier

Je vous propose une vidéo vous expliquant comment ajouter `barrier` pour lier souris et clavier entre Windows et Linux.

{% hint style="info" %}
Dans notre configuration, le Windows est un serveur et le Linux est un client.
{% endhint %}

{% hint style="info" %}
Si vous avez des bugs lorsque que votre souris passe de Windows à Linux, vous pouvez ajouter une latence de 10 ms qui ne sera effective que sur la transition de l'écran Windows vers Linux donc aucun retard en jeux et pas de ressenti gênant.
{% endhint %}

{% hint style="info" %}
Il se peut qu'il y ait un blocage, il suffit pour cela de regarder du côté de votre pare-feu.
{% endhint %}

{% embed url="https://youtu.be/EtYOaH7deeo" %}

## AudioReplay

Ici je conseille ceci : *c'est du click and connect* \
[https://audiorelay.net/](https://audiorelay.net/)

## SAMBA

Pour les dossiers utilisateurs, je conseille [SAMBA](https://fr.wikipedia.org/wiki/Samba_(informatique)). Vous pouvez suivre la vidéo ci-dessous et ensuite vous changer le "path" par votre dossier `/home/user` sous Linux.

Il ne vous restera qu'à ajouter votre partage SAMBA en périphérique réseau sous Windows et faire : Propriétés > Changer le répertoire sur vos dossiers utilisateur.

{% hint style="info" %}
Vous pouvez ignorer la partie `fstab` et `mount` car vous n'en aurez pas besoin.
{% endhint %}

{% embed url="https://youtu.be/sTZTvsff0DI" %}
