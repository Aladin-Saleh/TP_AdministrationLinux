# TP_AdministrationLinux
TP d'Administration linux de l'ESIEE Amiens

![image](https://user-images.githubusercontent.com/67257097/232489035-d3e42d53-ad11-478c-aa99-53120f3cf7ea.png)


# Création d'un pare-feu (pfsense)

Via oracle virtual box.  
Paramètre du parefeu dans la VM.  

```bash
Version : FreeBSD (64bits)
Type : BSD

RAM : 1024 MB
Disque dur virtuelle (VDI).
Dynamically allocated (16 GB).
```

## Configuration 

Clic droit -> Settings  
```
Processeur : 4
Storage : Charger l'image iso de pfsense
Réseau : Première carte en accès par pont (Bridged Adapter) (WAN)
         Deuxieme carte en host-only Adapter (LAN)
         Troisième carte en Internal Network (DMZ)
```

## Etapes de configuration du pfsense.  

```
Accept.  
Ok.  
Selection du langage.
Auto UFS.

No
Reboot
Desactiver le pfsense avant reboot (Devices -> Optical Drive -> Décocher le pfsense)
```

## Modification des adresses

```
On choisit l'option 2
LAN
On entre l'@ de l'interface pfsense (10.9.1.254)
On entre le masque : /24
Laisser vide (Entrer)
Laisser vide (Entrer)
Ne pas activer le DHCP (n)
Rester en HTTPS (n)
```

## Configurer l'interface réseau connecté sur le LAN

```
Modifier les propriété de la carte reseau VirtualBox Host-Only Network -> IPv4 -> fournir l'@ et le masque du LAN.

Ouvrir le navigateur à l'@ donnée

Par defaut :
Login : admin
MDP : pfsense

Hostname : parefeu
domaine : grp09.lab
Primary DNS : 1.1.1.1

NEXT

Timezone : Europe/Paris

NEXT

Décocher Block private Networks from entering via WAN

NEXT
NEXT

Configuration des logs
Reloads
Accept
```


