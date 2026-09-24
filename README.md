# OpenLDAP Server on Debian13

<p align="center">
  <img src="Images/Cover/Cover_OpenLDAP.jpg" alt="OpenLDAP-Cover" width="100%">
</p>

## :book: Index

* 👮 [Terms of use](./LICENSE)
* ♻️ [Features](#recycle-features)
* ✅ [Requirements](#white_check_mark-requirements)
* 📚 [Resources](#books-resources)
* ⚙️ [Install basic software](#gear-install-basic-software)

## :recycle: Features
ORDER:

* 1. Create the directory tree structure
* 2. Load the sudo schema
* 3. Create the users
* 4. Create the groups
* 5. Create the sudo roles
* 6. Apply the LAM ACL

## 1 install software
```bash
apt update
apt install slapd ldap-utils sudo
```

### 1.1 Create admin password 

## 2 Initialize LDAP wizard
```
sudo dpkg-reconfigure slapd
```
### 2.1 Select NO omit LDAP config

### 2.2 Check the Domain name

### 2.3 Check the organization  name

### 2.4 Enter and repeat the admin password 

### 2.5 No delete old database 

### 2.6 Yes move old database

### 2.7  Restart and check service slapd
```bash
sudo systemctl restart slapd
sudo systemctl status slapd
```
###  2.8 Verify Domain name its correct
sudo  slapcat

## 3 Create structure of LDAP dc=computer,dc=academy,dc=com (Example)

```conf
dc=computer,dc=academy,dc=com
├── ou=Users
│   ├── ou=Active
│   ├── ou=Inactive
│   └── ou=Services
│
├── ou=Groups
│   ├── ou=System
│   ├── ou=Applications
│   └── ou=NetworkGroups
│
├── ou=Machines
│   ├── ou=Servers
│   ├── ou=Clients
│   └── ou=Disabled
│
├── ou=Roles
│   ├── ou=Sudoers
│   ├── ou=LDAP
│   └── ou=Printing
│
├── ou=Policies
│
├── ou=Certificates
│   ├── ou=CertificateAuthorities
│   ├── ou=Personal
│   ├── ou=Machines
│   ├── ou=Services
│   └── ou=Revoked
│
└── ou=Resources
    ├── ou=Shared
    ├── ou=Printers
    ├── ou=Applications
    └── ou=Rooms
```

### 3.1 Create a new structure file base.ldif
```bash
nano base.ldif
```

```conf
# base.ldif

dn: dc=computer,dc=academy,dc=com
objectClass: top
objectClass: dcObject
objectClass: organization
dc: computer
o: Computer Academy

dn: ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Users

dn: ou=Active,ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Active

dn: ou=Inactive,ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Inactive

dn: ou=Services,ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Services

dn: ou=Groups,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Groups

dn: ou=System,ou=Groups,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: System

dn: ou=Applications,ou=Groups,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Applications

dn: ou=NetworkGroups,ou=Groups,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: NetworkGroups

dn: ou=Machines,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Machines

dn: ou=Servers,ou=Machines,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Servers

dn: ou=Clients,ou=Machines,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Clients

dn: ou=Disabled,ou=Machines,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Disabled

dn: ou=Roles,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Roles

dn: ou=Sudoers,ou=Roles,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Sudoers

dn: ou=LDAP,ou=Roles,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: LDAP

dn: ou=Printing,ou=Roles,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Printing

dn: ou=Policies,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Policies

dn: ou=Certificates,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Certificates

dn: ou=CertificateAuthorities,ou=Certificates,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: CertificateAuthorities

dn: ou=Personal,ou=Certificates,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Personal

dn: ou=Machines,ou=Certificates,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Machines

dn: ou=Services,ou=Certificates,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Services

dn: ou=Revoked,ou=Certificates,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Revoked

dn: ou=Resources,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Resources

dn: ou=Shared,ou=Resources,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Shared

dn: ou=Printers,ou=Resources,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Printers

dn: ou=Applications,ou=Resources,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Applications

dn: ou=Rooms,ou=Resources,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Rooms
```

### 3.2 Import structure to lDAP 
```bash
sudo ldapadd -x -D "cn=admin,dc=computer,dc=academy,dc=com" -W -f base.ldif
```
⚠️ If import fails because to the first DN block erase this.

Check the base group is imported
```bash
sudo ldapsearch -x -b "dc=computer,dc=academy,dc=com" ou
```

## 4 Import sudoers or other schemas to LDAP
El esquema sudo debe existir antes de importar cualquier LDIF que contenga objetos sudoRole, pero no depende de que hayas importado previamente base.ldif.

### 4.1 Download the Debian packet

```bash
mkdir sudo-schema
cd sudo-schema
apt download sudo-ldap
```

### 4.2 Extract the Debian packet
```bash
dpkg-deb -x sudo-ldap_*.deb extract
```

### 4.3 Import sudoers schema
```bash
sudo ldapadd -Y EXTERNAL -H ldapi:/// -f extract/usr/share/doc/sudo-ldap/schema.olcSudo
```
:warning: if you dont find the schema in the extract you can search:

```bash
find extract -name "schema.olcSudo"
```

## 5 Create Users

### 5.1 Create Users.ldif
```bash
nano Users.ldif 
```
```conf
# Users.ldif

dn: uid=LDAP-Writer,ou=Services,ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
uid: LDAP-Writer
cn: LDAP-Writer
sn: LDAP-Writer
userPassword: {SSHA}K9sL4Ny7jVwq8Bt2cWmYF7RzP1XeHkQa
description: Service account for writing to the LDAP tree

dn: uid=LDAP-Reader,ou=Services,ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
uid: LDAP-Reader
cn: LDAP-Reader
sn: LDAP-Reader
userPassword: {SSHA}XyZ12345abcdef67890GhIjKlMnOpQrS
description: Service account for reading to the LDAP tree

dn: uid=user1,ou=Active,ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
uid: user1
cn: John Smith
sn: Smith
givenName: John
uidNumber: 1002
gidNumber: 1002
homeDirectory: /home/john
loginShell: /bin/bash
userPassword: {SSHA}N4mY8uLpQ2vKj7XtBwR5cHd9ZaEsTgF1
shadowLastChange: 0

dn: uid=user2,ou=Active,ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
uid: user2
cn: Alice Smith
sn: Smith
givenName: Alice
uidNumber: 1004
gidNumber: 1004
homeDirectory: /home/asmith
loginShell: /bin/bash
userPassword: {SSHA}T8pVn3LqH5yKc9RxMwEaZ7BdFuGsJ2Nt
shadowLastChange: 0

dn: uid=zabbix-service,ou=Services,ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
cn: Zabbix Service Account
sn: Service
uid: zabbix-service
userPassword: {SSHA}R7xTc2PnLmQ4VbY9KwEjF5ZdNsAuHcG3
description: Service account for monitoring LDAP
```

### 5.2 Import Users 
```bash
ldapadd -x -D "cn=admin,dc=computer,dc=academy,dc=com" -W -f Users.ldif
```

## 6 Create Groups

### 6.1 Create Groups.ldif
```bash
nano Groups.ldif 
```
```conf
# Groups.ldif

dn: cn=LDAP-Writers,ou=Applications,ou=Groups,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: groupOfNames
cn: LDAP-Writers
member: uid=LDAP-Writer,ou=Services,ou=Users,dc=computer,dc=academy,dc=com
member: uid=user1,ou=Active,ou=Users,dc=computer,dc=academy,dc=com
description: Group for user accounts that write LDAP

dn: cn=LDAP-Readers,ou=Applications,ou=Groups,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: groupOfNames
cn: LDAP-Readers
member: uid=LDAP-Reader,ou=Services,ou=Users,dc=computer,dc=academy,dc=com
member: uid=zabbix-service,ou=Services,ou=Users,dc=computer,dc=academy,dc=com
description: Group for user accounts that read LDAP

dn: cn=Linux-Administrators,ou=System,ou=Groups,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: posixGroup
cn: Linux-Administrators
gidNumber: 2001
description: Group for user accounts that administer Linux systems using sudo comand

dn: cn=SSH-Access,ou=System,ou=Groups,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: posixGroup
cn: SSH-Access
gidNumber: 2002
description: POSIX group used to restrict remote SSH access to authorized users

dn: cn=Wiki-Access,ou=Applications,ou=Groups,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: groupOfNames
cn: Wiki-Access
member: uid=user1,ou=Active,ou=Users,dc=computer,dc=academy,dc=com
description: Authorized users for MediaWiki access
```

### 6.2 Import Groups
```bash
ldapadd -x -D "cn=admin,dc=computer,dc=academy,dc=com" -W -f Groups.ldif
```

## 7 Create Roles

### 7.1 Create Roles.ldif
```bash
nano Roles.ldif 
```

```conf
# Roles.ldif

dn: cn=Role-Linux-Admin,ou=Sudoers,ou=Roles,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: sudoRole
cn: Role-Linux-Admin
sudoUser: %Linux-Administrators
sudoHost: ALL
sudoCommand: ALL

dn: cn=Role-LDAP-Admin,ou=LDAP,ou=Roles,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: sudoRole
cn: Role-LDAP-Admin
sudoUser: cn=LDAP-Administrators,ou=Applications,ou=Groups,dc=computer,dc=academy,dc=com
sudoHost: ALL
sudoCommand: /usr/bin/ldap*
sudoCommand: /usr/sbin/slap*
sudoCommand: /bin/systemctl *slapd*
sudoCommand: /usr/bin/journalctl *slapd*
sudoCommand: /usr/bin/sudoedit /etc/ldap/*
sudoCommand: /usr/bin/sudoedit /etc/default/slapd/*
sudoCommand: /usr/bin/sudoedit /etc/systemd/system/slapd*
```

### 7.2 Import Roles
```bash
ldapadd -x -D "cn=admin,dc=computer,dc=academy,dc=com" -W -f Roles.ldif
```

## 8 Create ACL lists

### 8.1 Create ACLs to assign permissions and protect the LDAP tree from anonymous queries.
```bash
nano ACL.ldif 
```

```conf
# ACL.ldif

dn: olcDatabase={1}mdb,cn=config
changetype: modify
delete: olcAccess
olcAccess: {2}to * by * read
-
add: olcAccess
olcAccess: {2}to dn.subtree="dc=computer,dc=academy,dc=com"
  by group.exact="cn=LDAP-Writers,ou=Applications,ou=Groups,dc=computer,dc=academy,dc=com" write
  by group.exact="cn=LDAP-Readers,ou=Applications,ou=Groups,dc=computer,dc=academy,dc=com" read
  by * none
```

### 8.2 Import ACL 
```bash
sudo ldapmodify -Y EXTERNAL -H ldapi:/// -f ACL.ldif
```

### 9.0 Advanced Indexing

```conf
# Index.ldif

dn: olcDatabase={1}mdb,cn=config
changetype: modify
add: olcDbIndex
olcDbIndex: entryCSN eq
olcDbIndex: entryUUID eq
olcDbIndex: sn eq,sub
olcDbIndex: mail eq
olcDbIndex: loginShell eq
olcDbIndex: sudoUser eq
olcDbIndex: sudoHost eq
```
Apply:
```bash
sudo ldapmodify -Y EXTERNAL -H ldapi:/// -f Index.ldif
```

Check:
```bash
sudo ldapsearch -LLL -Y EXTERNAL -H ldapi:/// -b "olcDatabase={1}mdb,cn=config" olcDbIndex
```
Stop the service:
```bash
sudo systemctl stop slapd
```
Regenerate index:
```bash
sudo slapindex -n 1
```
Change owner files:
```bash
sudo chown -R openldap:openldap /var/lib/ldap/
```

Check owner files:
```bash
sudo ls -la /var/lib/ldap/
```

Start the service:
```bash
sudo systemctl start slapd
```

# Multimaster
```conf
 LDAP01 completo
↓
Exportar
↓
Crear LDAP02
↓
Verificar que LDAP02 funciona
↓
Configurar replicación
↓
Comprobar que replica
↓
Instalar LAM
```

## 9 Activate SincProv on LDAP-1 and LDAP-2:

### 9.1 create SincProv.ldif:
```bash
nano syncprov.ldif
```

```bash
dn: cn=module{0},cn=config
changetype: modify
add: olcModuleLoad
olcModuleLoad: syncprov.la

dn: olcOverlay=syncprov,olcDatabase={1}mdb,cn=config
objectClass: olcOverlayConfig
objectClass: olcSyncProvConfig
olcOverlay: syncprov
olcSpCheckpoint: 100 10
olcSpSessionLog: 100
```

### 9.2 Import SincProv
```bash
sudo ldapadd -Y EXTERNAL -H ldapi:/// -f syncprov.ldif
```

## 10 Create server ID on LDAP-1 and LDAP-2:

### 10.1 Create ServerID.ldif
```bash
nano ServerID.ldif 
```

LDAP-1:
```conf
dn: cn=config
changetype: modify
add: olcServerID
olcServerID: 1
```

LDAP-2:
```conf
dn: cn=config
changetype: modify
add: olcServerID
olcServerID: 2
```

### 10.2 Import
Import:
```bash
sudo ldapmodify -Y EXTERNAL -H ldapi:/// -f ServerID.ldif
```

## 11 Activate SyncRepl on LDAP-1 nad LDAP-2

### 11.1 Create SyncRepl.ldif
```bash
nano SyncRepl.ldif
```

LDAP-1:
```conf
# SyncRepl.ldif

dn: olcDatabase={1}mdb,cn=config
changetype: modify
add: olcSyncrepl
olcSyncrepl: rid=001
  provider=ldap://IP-LDAP-2
  bindmethod=simple
  binddn="uid=LDAP-Syncer,ou=Services,ou=Users,dc=computer,dc=academy,dc=com"
  credentials="LDAP-Syncer-PASS"
  searchbase="dc=computer,dc=academy,dc=com"
  type=refreshAndPersist
  retry="5 5 300 +"
  timeout=1
```

LDAP-2:
```conf
# SyncRepl.ldif

dn: olcDatabase={1}mdb,cn=config
changetype: modify
add: olcSyncrepl
olcSyncrepl: rid=002
  provider=ldap://IP-LDAP-1
  bindmethod=simple
  binddn="uid=LDAP-Syncer,ou=Services,ou=Users,dc=computer,dc=academy,dc=com"
  credentials="LDAP-Syncer-PASS"
  searchbase="dc=computer,dc=academy,dc=com"
  type=refreshAndPersist
  retry="5 5 300 +"
  timeout=1
```

### 11.2 Import:
```bash
sudo ldapmodify -Y EXTERNAL -H ldapi:/// -f SyncRepl.ldif
```
### 11.3 Check:
Check: 
```bash
sudo ldapsearch -LLL -Y EXTERNAL -H ldapi:/// -b "olcDatabase={1}mdb,cn=config" olcSyncrepl
```
## 12 Activate Mirror mode on LDAP-1 and LDAP-2 (Only Master-Slave)

### 12.1 Create Mirror.ldif
```bash
nano Mirror.ldif
```

```conf
# Mirror.ldif

dn: olcDatabase={1}mdb,cn=config
changetype: modify
add: olcMirrorMode
olcMirrorMode: TRUE
```

### 12.2 Import:
```bash
sudo ldapmodify -Y EXTERNAL -H ldapi:/// -f Mirror.ldif
```

Check:
```bash
sudo ldapsearch -LLL -Y EXTERNAL -H ldapi:/// -b "olcDatabase={1}mdb,cn=config" olcMirrorMode
```

## Enable TLS with StartTLS
In a multi-master OpenLDAP environment, it is common practice to enable TLS using self-signed certificates from an internal CA

* Create an internal CA.
* Generate a certificate for each LDAP node.
* Configure slapd to use TLS.
* Restart and verify.
* Configure replication to use ldaps:// or startTLS.
* Distribute the CA certificate to all clients and LDAP nodes.

### Create an internal CA

```bash
mkdir /root/ca
```
```bash
cd /root/ca
```
```bash
openssl genrsa -out ca.key 4096
openssl req -new -x509 \
-days 3650 \
-key ca.key \
-out ca.crt \
-subj "/C=US/O=Computer_Academy/CN=Computer_Academy_LDAP_CA"
```

### Generate a certificate for each LDAP node

LDAP01:
```bash
openssl genrsa -out ldap01.key 4096
```

```bash
openssl req -new \
-key ldap01.key \
-out ldap01.csr \
-subj "/C=US/O=Computer_Academy/CN=ldap01.computer.academy.com"
```
LDAP02:
```bash
openssl genrsa -out ldap02.key 4096
```

```bash
openssl req -new \
-key ldap02.key \
-out ldap02.csr \
-subj "/C=US/O=Computer_Academy/CN=ldap02.computer.academy.com"
```

### Create SAN files
LDAP01:
```bash
cat > ldap01.ext << EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage=digitalSignature,keyEncipherment
extendedKeyUsage=serverAuth
subjectAltName=@alt_names

[alt_names]
DNS.1=ldap01.computer.academy.com
DNS.2=ldap01
IP.1=YOUR-STATIC-IP
EOF
```

LDAP02:
```bash
cat > ldap02.ext << EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage=digitalSignature,keyEncipherment
extendedKeyUsage=serverAuth
subjectAltName=@alt_names

[alt_names]
DNS.1=ldap02.computer.academy.com
DNS.2=ldap02
IP.1=YOUR-STATIC-IP
EOF
```

### Self-sign the certificate
LDAP01:
```bash
openssl x509 -req \
-in ldap01.csr \
-CA ca.crt \
-CAkey ca.key \
-CAcreateserial \
-out ldap01.crt \
-days 3650 \
-extfile ldap01.ext
```

LDAP02:
```bash
openssl x509 -req \
-in ldap02.csr \
-CA ca.crt \
-CAkey ca.key \
-CAcreateserial \
-out ldap02.crt \
-days 3650 \
-extfile ldap02.ext
```

### Install certificates on each node
LDAP01:
```bash
mkdir -p /etc/ldap/certs
mv ldap01.crt /etc/ldap/certs/
mv ldap01.key /etc/ldap/certs/
mv ca.crt /etc/ldap/certs/
```
LDAP02:
```bash
mkdir -p /etc/ldap/certs
mv ldap02.crt /etc/ldap/certs/
mv ldap02.key /etc/ldap/certs/
mv ca.crt /etc/ldap/certs/
```
Assign permissions and owner on each node
```bash
chown openldap:openldap /etc/ldap/certs/*
chmod 600 /etc/ldap/certs/*.key
chmod 644 /etc/ldap/certs/*.crt
```
### Configure TLS in cn=config LDAP on each node
Create TLS.ldif on LDAP01:
```conf
dn: cn=config
changetype: modify
replace: olcTLSCACertificateFile
olcTLSCACertificateFile: /etc/ldap/certs/ca.crt
-
replace: olcTLSCertificateFile
olcTLSCertificateFile: /etc/ldap/certs/ldap01.crt
-
replace: olcTLSCertificateKeyFile
olcTLSCertificateKeyFile: /etc/ldap/certs/ldap01.key
```

Create TLS.ldif on LDAP02:
```conf
dn: cn=config
changetype: modify
replace: olcTLSCACertificateFile
olcTLSCACertificateFile: /etc/ldap/certs/ca.crt
-
replace: olcTLSCertificateFile
olcTLSCertificateFile: /etc/ldap/certs/ldap02.crt
-
replace: olcTLSCertificateKeyFile
olcTLSCertificateKeyFile: /etc/ldap/certs/ldap02.key
```

Apply to each node:
```bash
sudo ldapmodify -Y EXTERNAL -H ldapi:/// -f TLS.ldif
```

### Activate LDAPS listener on each node
```bash
sudo nano /etc/default/slapd
```

Search 
```conf
SLAPD_SERVICES="ldap:/// ldapi:///"
```

Replace:
```conf
SLAPD_SERVICES="ldap:/// ldaps:/// ldapi:///"
```

Restart slapd service
```conf
systemctl restart slapd
```

Verify LDAPS por is active
```bash
ss -lntp | grep 636
```

Check TLS:

```bash
openssl s_client \
-connect ldap01.midominio.local:636 \
-CAfile ca.crt
```
Correct result:
```conf
Verify return code: 0 (ok)
```

### Configure CA trust
Copy ca.crt to all LDAP nodes and clients
```bash
cp ca.crt /usr/local/share/ca-certificates/
```
```bash
update-ca-certificates
```
### TLS on multimaster
Modify SyncRepl on each LDAP:
```bash
nano Syncrepl.ldif
```

LDAP01:
```conf
dn: olcDatabase={1}mdb,cn=config
changetype: modify
replace: olcSyncrepl
olcSyncrepl: rid=001
  provider=ldap://IP-LDAP-2:389
  starttls=yes
  bindmethod=simple
  binddn="uid=LDAP-Reader,ou=Services,ou=Users,dc=computer,dc=academy,dc=com"
  credentials=YOUR-PASSWORD
  searchbase="dc=computer,dc=academy,dc=com"
  type=refreshAndPersist
  retry="5 5 300 +"
  timeout=5
  tls_reqcert=demand
```

LDAP02:
```conf
dn: olcDatabase={1}mdb,cn=config
changetype: modify
replace: olcSyncrepl
olcSyncrepl: rid=002
  provider=ldap://IP-LDAP-1:389
  starttls=yes
  bindmethod=simple
  binddn="uid=LDAP-Reader,ou=Services,ou=Users,dc=computer,dc=academy,dc=com"
  credentials=YOUR-PASSWORD
  searchbase="dc=computer,dc=academy,dc=com"
  type=refreshAndPersist
  retry="5 5 300 +"
  timeout=5
  tls_reqcert=demand
```
Check: 
```bash
journalctl -u slapd -f
```

## 13 Install and configure LAM 

### 13.1 Download and install Packet

```bash
sudo apt install ldap-account-manager
```
⚠️ If you want to manage home directories and quotas on client hosts, you must use the `ldap-account-manager-lamdaemon` package on the LDAP clients.

### 13.2 Update PHP memory limit to 256M
```bash
 nano /etc/php/8.4/apache2/php.ini
```
```bash
memory_limit = 256M
```
### 13.3 Secure IP range to connect 

```bash
 nano /etc/apache2/conf-enabled/ldap-account-manager.conf
```

```conf
#Require all granted
Require ip 127.0.0.1 192.168.10.0/24
```
### 13.3 Restart service Apache2

```conf
sudo systemctl restart apache2
```

### 13.4 Try web acces
http://LDAP-IP/lam
<p align="center">
    <img src="Images/LAM/LAM-Cover.png">
</p>

### 13.5 Click the menu "LAM configuration" on the top right.
<p align="center">
    <img src="Images/LAM/LAM-Edit-Profiles.png">
</p>

### 13.6 Click "Edit server profiles" to modify the OpenLDAP profile.
* User: lam
* pass: lam
<p align="center">
    <img src="Images/LAM/LAM-Acces-Profile.png">
</p>

### 13.7 Change default password LAM 
On the first tab, "General Settings," scroll all the way down to the section
labeled "Profile Password" and enter the new password twice.
<p align="center">
    <img src="Images/LAM/LAM-Profile-Password.png">
</p>
⚠️ To give it a more corporate and professional setup, we will configure LAM to use a user from our LDAP tree,
allowing it to be managed in the same way as the service accounts we will be using.

In the "General Settings" tab, within the "Server Settings" section,
we will edit the "Login method," "LDAP suffix," "Bind user," and "Bind password" fields.

<p align="center">
    <img src="Images/LAM/LAM-Change-LAM-User.png">
</p>

On the Tool settings, input the domain name of your OpenLDAP server.
On the Security settings, select the login method as Fixed list and input the details admin user for the OpenLDAP server.
On the Profile password, input the new password and repeat.

⚠️ We recommnded change login method in server preferences to LDAP search

### 13.8 Edit users and groups directory

Next, click on the Account Types section the configure the following section:
<p align="center">
    <img src="Images/LAM/LAM-Account-types.png">
</p>
On the Users section, input the default base domain for OpenLDAP users. In his case, the default suffix is People.
On the Groups section, input the default base domain for the group. In this case, the default other group is Groups.
Click Save to apply the changes.

## Hosts

### Install basic software
```bash
sudo apt install sssd sssd-tools libnss-sss libpam-sss sudo-ldap
```
### Create file /etc/sssd.conf
```bash
nano /etc/sssd.conf
```

```conf
[sssd]
config_file_version = 2
services = nss, pam, ssh, sudo
domains = computer.academy.com

[nss]
homedir_substring = /home

[pam]

[domain/computer.academy.com]

id_provider = ldap
auth_provider = ldap
chpass_provider = ldap
sudo_provider = ldap

cache_credentials = False
enumerate = False

ldap_uri = ldap://LDAP01-IP,ldap://LDAP02-IP
ldap_search_base = dc=computer,dc=academy,dc=com

ldap_default_bind_dn = uid=LDAP-Reader,ou=Servicios,ou=Usuarios,dc=computer,dc=academy,dc=com
ldap_default_authtok_type = password
ldap_default_authtok = YOUR-PASS

ldap_user_search_base = ou=Users,dc=computer,dc=academy,dc=com
ldap_group_search_base = ou=Groups,dc=computer,dc=academy,dc=com
ldap_sudo_search_base = ou=Sudoers,ou=Roles,dc=computer,dc=academy,dc=com

ldap_schema = rfc2307bis

ldap_user_object_class = posixAccount
ldap_group_object_class = posixGroup

ldap_tls_reqcert = demand
ldap_id_use_start_tls = true

fallback_homedir = /home/%u
default_shell = /bin/bash

access_provider = ldap
ldap_access_filter = (memberOf=cn=SSH-Access,ou=System,ou=Groups,dc=computer,dc=academy,dc=com)
```
