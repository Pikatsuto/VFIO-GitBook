---
description: >-
  Ici votre igpu ne fonctionera pas tout de suite et cecis est aussi valable
  pour des carte graphique un peut plus ancien donc voici le fix
---

# 🔧 Coréction des bug de passthrough

## Récupération de votre VBIOS

ici on vas clone le bios de votre carte graphque qui peut poser problème en physique

donc pour ceci on récupère l'id de votre carte graphique

```bash
lspci | grep VGA
#03:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Navi 23 (rev c7)
#09:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Cezanne (rev c8)
```

puis le clone du bios

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

## Attribution du VBIOS a la VM et fix du processeur

Changer VMID par celui de votre VM

```bash
VMID=201
```

```bash
sed -i "s/cpu: host/cpu: host,hidden=1/g" /etc/pve/qemu-server/${VMID}.conf
sed -i "s/,x-vga=1/,x-vga=1,romfile=gpu.rom/g" /etc/pve/qemu-server/${VMID}.conf
```

et dans mon cas mon igpu a un 2e slot graphique au millieu des autre donc j'ajouté ceci (penser a changer PCIEIDADD par votre valeur

```bash
PCIEIDADD=09:00.1
```

```bash
sed -i "s/${PCIEID}/${PCIEID};0000:${PCIEIDADD}/g" /etc/pve/qemu-server/${VMID}.conf
```

