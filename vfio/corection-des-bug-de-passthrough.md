---
description: >-
  Ici votre iGPU ne fonctionnera pas tout de suite et ainsi que 
  pour des cartes graphiques plus anciennes donc voici comment 
  corriger ce problème.
---

# 🔧 Correction des bugs de passthrough

## Récupération de votre VBIOS

Ici nous allons cloner le bios de votre carte graphique qui peut poser un problème en physique

donc pour se faire on récupère l'id de votre carte graphique :

```bash
lspci | grep VGA
#03:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Navi 23 (rev c7)
#09:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Cezanne (rev c8)
```

puis on clone le bios :

```bash
PCIEID=09:00.0
```

```bash
export PATH_TO_ROM=$(find /sys/devices/pci0000:00/ | grep $PCIEID | grep rom)

echo 1 > $PATH_TO_ROM
mkdir -p /usr/share/kvm/
cat $PATH_TO_ROM > /usr/share/kvm/gpu.rom
echo 0 > $PATH_TO_ROM
```

## Attribution du VBIOS à la VM et correction du processeur

Changer VMID par celui de votre VM

```bash
VMID=201
```

```bash
sed -i "s/cpu: host/cpu: host,hidden=1/g" /etc/pve/qemu-server/${VMID}.conf
sed -i "s/,x-vga=1/,x-vga=1,romfile=gpu.rom/g" /etc/pve/qemu-server/${VMID}.conf
```

Dans mon cas, mon iGPU a un 2e slot graphique au milieu des autres donc j'ajoute ceci (penser à changer PCIEIDADD par votre valeur)

```bash
PCIEIDADD=09:00.1
```

```bash
sed -i "s/${PCIEID}/${PCIEID};0000:${PCIEIDADD}/g" /etc/pve/qemu-server/${VMID}.conf
```

