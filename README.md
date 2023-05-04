---
description: Ici nous configurons votre bios pour pouvoir faire notre GPU passthrough
---

# ⚙ Configuration

## Liste des paramètres

{% hint style="info" %}
Si le nom des paramètres n'est pas identique dans votre bios c'est normal. Chaque constructeur est différent mais ils sont toujours reconnaissables car les technologies en question ont le même nom. Les différences que vous pouvez observer sont des abréviations pouvant raccourcir ou ajouter des mots.
{% endhint %}

Ici chaque machine a un bios différent donc je ne peux pas vous montrer d'illustration universelle, mais vous trouverez ci-dessous la liste des paramètres à changer :

| Nom                       | Valeur    | Précision                                                                                                                        |
|---------------------------|-----------|----------------------------------------------------------------------------------------------------------------------------------|
| BIOS Mode                 | Advanced  | Pour avoir accès aux paramètres                                                                                                  |
| SVM Mode                  | Enable    | Pour avoir la virtualisation (Je recommande également d'activer la VT-X si disponible)                                           |
| iGPU                      | Force     | Uniquement si vous n'avez pas de deuxième carte graphique et que votre processeur a une puce intégrée.                           |
| IOMMU                     | Enable    | Pour activer la partie VFIO                                                                                                      |
| iGPU Configuration ou UMA | UMA\_AUTO | Réglage similaire que l'iGPU, à activer que si vous n'avez pas de deuxième GPU et que votre processeur possède une puce intégrée |
| Boot / CSM                | Enable    | Démarrage en Legacy pour la virtualisation                                                                                       |
