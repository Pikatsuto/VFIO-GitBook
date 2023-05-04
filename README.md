---
description: Ici nous configuron votre bios pour pouvoir faire notre GPU passthrough
---

# ⚙ Configuration

## Liste des paramètre

{% hint style="info" %}
Si le nom des paramètrte n'est pas identique dans votre bios c'est normal car chaque constructeur sont différent mais il sont toujour reconaissable car les technologie on le même nom les différence sont les abrégé ou non et des mots en plus
{% endhint %}

Ici chaquin a un bos diférent donc je ne peut pas vous montré d'image mais voici la liste des paramètre a changer

| Nom                       | Valeur    | Précision                                                                                                                       |
| ------------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------- |
| BIOS Mode                 | Advanced  | Pour avoir accès aux settings                                                                                                   |
| SVM Mode                  | Enable    | Pour avoir la virtualisation (Je recommande également d'activé la VT-X si disponible)                                           |
| iGPU                      | Force     | Uniquement si vous n'avez pas de deuxième carte graphique et que votre processeur a une puce intégré.                           |
| IOMMU                     | Enable    | Pour activé la partie VFIO                                                                                                      |
| iGPU Configuration ou UMA | UMA\_AUTO | Réglages similaire que l'iGPU, a activé que si vous n'avez pas de deuxième GPU et que votre processeur possède une puce intégré |
| Boot / CSM                | Enable    | Démarrage en Legacy pour la virtualisation                                                                                      |
