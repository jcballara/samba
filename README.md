# samba
En aquest repositori adjuntaré comandes i instruccions per a treballar amb samba, així com l'ús de Samba AD, el servei de directori de domini de Samba.  
La plataforma utilitzada és un Ubuntu Server 22.04.  
El nom del domini a configurar és DOM01.COM  

## Instal.lar Samba AD
**Instal.lació dels paquets:**  
```bash
sudo apt install acl attr samba samba-dsdb-modules samba-vfs-modules winbind libpam-winbind libnss-winbind libpam-krb5 krb5-config krb5-user
```
Durant el procés d'instal.lació cal tenir en compte:
Default Kerberos version 5 realm → DOM01.COM  !!IMPORTANT, cal escriure-ho en majúscules!!  
Kerberos servers for your realm → El nom del servidor definit en el hostname  
Administrative server → El nom del servidor definit en el hostname  

**Deshabilitar el servei de DNS del servidor donat que el propi Samba AD ja el munta:**  
```bash
sudo systemctl mask systemd-resolved.service
sudo systemctl stop systemd-resolved.service
sudo rm /etc/resolv.conf
```

**Configurar de forma manual el resolv.conf del servidor per a què la màquina pugui sortir a internet:**  
Editar el fitxer /etc/resolv.conf i afegir-hi les següents línies (adaptant les parts que es customitzen):  
```bash
nameserver _ip_del_servidor_ #La ip del servidor és la de la xarxa interna.
nameserver 8.8.8.8  #Aquest és un DNS extern.
search dom01.com #Serà el domini 
```
**Abans de fer la promoció a domini, cal elminiar els fitxers per defecte de configuració inicials:** 
```bash
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf_orig
sudo mv /etc/krb5.conf /etc/krb5.conf_orig
```

**Promoció del servidor a domini:**  
```bash
sudo samba-tool domain provision --use-rfc2307 --interactive
```

**Parar i deshabilitar els serveis que deixa arrencats la instal.lació:**  
```bash
sudo systemctl stop smbd nmbd winbind
sudo systemctl disable smbd nmbd winbind
```

**Arrancar i habilitar els serveis de Samba AD:**  
```bash
sudo systemctl unmask samba-ad-dc
sudo systemctl enable samba-ad-dc
sudo systemctl start samba-ad-dc
```

**Crear una unitat organitzativa, un grup, usuari i afegir-lo al grup:**  
La gestió es fa amb la comanda samba-tool:  
```bash
sudo samba-tool ou create 'OU=docents,DC=itb,DC=local' --description='Unitat de docents'
sudo samba-tool group add 'docents' --groupou='OU=docents' --description='Grup de docents'
sudo samba-tool user create 'jordi.casas' 'jordi12345' --userou='OU=docents' --surname='Casas Ballara' --given-name='Jordi'
sudo samba-tool group addmembers 'docents' jordi.casas
```
