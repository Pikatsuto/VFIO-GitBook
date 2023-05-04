---
description: >-
  La nous paramétron votre proxmox pour le VFIO car il y a des modifiquation
  GRUB et module faire pour ceci
---

# ⚙ Paramétrage de Proxmox

## Module et démarage

### **Activer l'IOMMU**

Ici nous ajoutons l'IOMMU dans la config grub pour l'avoir au démarage de l'ordinateur car cette fonction est indispensable au VFIO (passage d'un composant physique a une machine virtuel)

```bash
sed -i -e 's/quiet/quiet amd_iommu=on initcall_blacklist=sysfb_init/g' /etc/default/grub;
update-grub;
```

### Puis on active les module

Ici nous activons des module au démarage qui sont tout aussi indispensable pour un GPU passthrough d'ailleur l'IOMMU en fait aussi partie

```bash
echo "vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd" > /etc/modules
```

### encore la même chose pour KVM

```bash
echo "options vfio_iommu_type1 allow_unsafe_interrupts=1" > /etc/modprobe.d/iommu_unsafe_interrupts.conf;
echo "options kvm ignore_msrs=1" > /etc/modprobe.d/kvm.conf;
```

### On blacklist les pilote graphique

ici nous blacklistion les pilote graphique car sinon il y a de forte change que vous ayller des bug d'atrubution avec vos carte graphique entre votre proxmox et vos machine virtuel

pour les GPU PCI Express

```bash
echo "blacklist amdgpu"         > /etc/modprobe.d/blacklist.conf;
echo "blacklist nouveau"       >> /etc/modprobe.d/blacklist.conf;
echo "blacklist nvidia"        >> /etc/modprobe.d/blacklist.conf;
echo "blacklist radeon"        >> /etc/modprobe.d/blacklist.conf;
echo "blacklist snd_hda_intel" >> /etc/modprobe.d/blacklist.conf;
```

&#x20;si vous avez un igpu amd

```bash
echo "blacklist ccp"           >> /etc/modprobe.d/blacklist.conf;
echo "blacklist xhci_hcd"      >> /etc/modprobe.d/blacklist.conf;
```

### Puis attribuer vos GPU en VFIO&#x20;

ici vous devez chercher l'identifiant de vos GPU pour ça on vas taper la première commande pour lister vos carte graphique\
\
un fois cette liste faite on vas allez chercher leur code d'identifiquation 1 par 1 grace a la première commande

dans cette éxemple nous voyons 03:00.0 donc pour la recherche nous allons couper a partire du point ce qui donne 03:00 pour la 2e command

attention pour le 09:00 de mon coté j'ai beaucoup trop de slot pcie et c'est car j'utilise un IGPU (la partie graphique intégrer a mon prosseceu) dans ce cas de figure il est plus simple de revenire sur cette étape plus tard car proxmox nous présise le quel est le quel dans l'interface

<pre class="language-bash"><code class="lang-bash">lspci | grep VGA
#03:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Navi 23 (rev c7)
<strong>#09:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Cezanne (rev c8)
</strong>
# AMD GPU: RX 6600
lspci -n -s 03:00
<strong>#03:00.0 0300: 1002:73ff (rev c7)     &#x3C;== Video
</strong>#03:00.1 0403: 1002:ab28              &#x3C;== Audio

# AMD IGPU: Vega intégrer au Ryzen 7 5700G
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
