# samba
En aquest repositori adjuntaré comandes i instruccions per a treballar amb samba, així com l'ús de Samba AD, el servei de directori de domini de Samba.  
La plataforma utilitzada és un Ubuntu Server 22.04.  

## Instal.lar Samba AD
**Instal.lació dels paquets:**  
```sudo apt install acl attr samba samba-dsdb-modules samba-vfs-modules winbind libpam-winbind libnss-winbind libpam-krb5 krb5-config krb5-user```

**Promoció del servidor a domini:**  
```sudo samba-tool domain provision --use-rfc2307 --interactive```

**Parar els serveis que deixa arrencats la instal.lació:**  
```sudo systemctl stop smbd nmbd winbind
sudo systemctl disable smbd nmbd winbind
sudo systemctl unmask samba-ad-dc
sudo systemctl enable samba-ad-dc
sudo systemctl start samba-ad-dc
```


