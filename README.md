# TP_AdministrationLinux
TP d'Administration linux de l'ESIEE Amiens

![image](https://user-images.githubusercontent.com/67257097/232501406-0cf49ab5-c508-4a97-9999-981245f2ba31.png)



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

# Mise en place du serveur DHCP

Cloner le template Ubuntu Server 20.04.
```
Click droit -> Cloner 
L'appeler IPAM (IP Adresse Manager)
Lancer la machine
Utiliser Putty pour se connecter en ssh
Entrer les logs (celui du template)
```
## Configuration du netplan

```
sudo nano /etc/netplan/00-installer-config.yml
mise à jour de l'@ par celle du plan (10.9.1.253)
quitter
sudo netplanapply
```

Normalement on se fait kick de putty car modification de l'@
Se reconnecter avec la nouvelle adresse.  

## Changement du nom de la machine 

```
sudo hostnamectl set-hostname ipam
```
Verifier que la modification à été pris en compte en ouvrant une nouvelle session.  

## Installation du service DHCP

Se rendre sur le guide ubuntu d'installation : https://ubuntu.com/server/docs/network-dhcp

```
sudo apt install isc-dhcp-server
sudo nano /etc/dhcp/dhcpd.conf

Remplacer 10.5.5 par 10.9.1
Remplacer le masque pour 255.255.255.224 par 255.255.255.0
Mettre l'@ du serveur DNS pour le domain-name-server par l'@du serveur DNS
Le domain-name à mettre à jour 
option routers <adresses parefeu>

```
Sauvegarder et quitter

## Modification des interfaces d'écoutes

```
Check l'interface à mettre à jour

ip -br a 
sudo nano /etc/default/isc-dhcp-server
On met le nom de notre interface dans "INTERFACESv4"
sudo systemctl restart isc-dhcp-server.service
```

On peut vérifier la fonctionnalité du serveur DHCP en créant une machine 
## Création du clone de test

```
Settings -> System -> Tout desactiver sauf network
            Network -> Host-Only Adapter
```
            
Lancer la machine -> Ne pas charger d'image.  
Verifier que l'@ a bien été attribué.

# Mise en place du serveur DNS

Faire une sauvegarde de la VM DHCP.  
Se rendre sur : https://ubuntu.com/server/docs/service-domain-name-service-dns  

```
sudo apt install bind9
sudo apt install dnsutils
```

## Configuration du service DNS

```
sudo nano /etc/bind/named.conf.local
Ajouter : 
zone "grp09.lab" {
    type master;
    file "/etc/bind/db.grp09.lab";
};
```
Enregistrer et quitter.  

## Creation du db.grp09.lab

```
cd /etc/bind/
sudo cp db.local db.grp09.lab
```

On edite db.grp09.lab.  
Consiste à remplacer localhost par grp09.lab  
Remplacer l'@ IPv4 (A) par celle du serveur DHCP
```
ns IN A @DHCP
ldap IN A @LDAP
parefeu IN A @PAREFEU
```

```
sudo systemctl restart bind9.service
```

## Verification du fonctionnement du serveur DNS

```
nslookup
server @DNS
chercher le ldap, le parefeu
```

Ne pas oublier de faire des snapshot des VM.  
