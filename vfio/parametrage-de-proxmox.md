---
description: >-
  Sur cette page, nous paramétrons Proxmox pour le VFIO qui nécessite des modifications
  GRUB et modules.
---

# ⚙ Paramétrage de Proxmox

## Modules et démarrage

### **Activer l'IOMMU**

Ici nous ajoutons l'IOMMU dans la config GRUB pour l'ajouter au menu de démarrage de l'ordinateur. Cette fonction est indispensable au VFIO (passage d'un composant physique à une machine virtuelle).

```bash
sed -i -e 's/quiet/quiet amd_iommu=on initcall_blacklist=sysfb_init/g' /etc/default/grub;
update-grub;
```

### Puis on active les modules

Ici nous activons des modules au démarrage qui sont également indispensables pour le GPU passthrough dont l'IOMMU en fait partie.

```bash
echo "vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd" > /etc/modules
```

### Même chose pour KVM

```bash
echo "options vfio_iommu_type1 allow_unsafe_interrupts=1" > /etc/modprobe.d/iommu_unsafe_interrupts.conf;
echo "options kvm ignore_msrs=1" > /etc/modprobe.d/kvm.conf;
```

### On blackliste les pilotes graphiques

Ici nous excluons les pilotes graphiques pour éviter des problèmes d'attributions des cartes graphiques entre Proxmox et les machines virtuelles.

Pour les GPU PCI Express :

```bash
echo "blacklist amdgpu"         > /etc/modprobe.d/blacklist.conf;
echo "blacklist nouveau"       >> /etc/modprobe.d/blacklist.conf;
echo "blacklist nvidia"        >> /etc/modprobe.d/blacklist.conf;
echo "blacklist radeon"        >> /etc/modprobe.d/blacklist.conf;
echo "blacklist snd_hda_intel" >> /etc/modprobe.d/blacklist.conf;
```

&#x20;si vous avez un iGPU AMD

```bash
echo "blacklist ccp"           >> /etc/modprobe.d/blacklist.conf;
echo "blacklist xhci_hcd"      >> /etc/modprobe.d/blacklist.conf;
```

### Puis attribuer vos GPU en VFIO&#x20;

Ici vous devez chercher l'identifiant de vos GPU. Pour cela, nous tapons la première commande pour lister les cartes graphiques.\
\
Une fois cette liste obtenue, on va allez chercher leur code d'identification un par un, grâce a la première commande.

Dans cet exemple, nous voyons `03:00.0`. Pour la recherche, nous ne prenons en compte que la partie avant le `.` ce qui nous donne `03:00` pour la deuxième commande.

**Attention** : Dans les images d'illustration, j'ai utilisé `09:00` car l'iGPU (utilisé dans mon cas) produit beaucoup trop de slot PCIe. Dans ce cas de figure, il est plus simple de réaliser cette étape plus car Proxmox précise quelle adresse de PCI utiliser sur l'interface d'administration.

<pre class="language-bash"><code class="lang-bash">lspci | grep VGA
#03:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Navi 23 (rev c7)
<strong>#09:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Cezanne (rev c8)
</strong>
# AMD GPU: RX 6600
lspci -n -s 03:00
<strong>#03:00.0 0300: 1002:73ff (rev c7)     &#x3C;== Video
</strong>#03:00.1 0403: 1002:ab28              &#x3C;== Audio

# AMD iGPU: Vega intégré au Ryzen 7 5700G
lspci -n -s 09:00
#09:00.0 0300: 1002:1638 (rev c8)     &#x3C;== Video
#09:00.1 0403: 1002:1637              &#x3C;== Video
#09:00.2 1080: 1022:15df
#09:00.3 0c03: 1022:1639
#09:00.4 0c03: 1022:1639
#09:00.6 0403: 1022:15e3              &#x3C;== Audio
</code></pre>

Enfin on les ajoute en chargement dans le module VFIO

```bash
echo "# AMD GPU: RX 6600
options vfio-pci ids=1002:73ff,1002:ab28 disable_vga=1

# AMD APU: RYZEN 7 5700G
options vfio-pci ids=1002:1638,1002:1637,1022:15e3 disable_vga=1" > /etc/modprobe.d/vfio.conf;

update-initramfs -u;
reboot;
```
