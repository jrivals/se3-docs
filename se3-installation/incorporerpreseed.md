# Installation automatique d'un `Se3`

…article en chantier…

* [Préliminaire](#préliminaire)
* [Le fichier `preseed`](#le-fichier-preseed)
* [Incorporer le fichier `preseed` à l'archive d'installation](#incorporer-le-fichier-preseed-à-larchive-dinstallation)
* [Utiliser l'archive d'installation personnalisée](#utiliser-larchive-dinstallation-personnalisée)
* [Solution alternative](#solution-alternative)
* [Références](#références)


## Préliminaire

L'objectif est de créer un `CD d'installation` complètement automatisé de son `SE3 Wheezy`, ainsi, avec ce `CD` *personnalisé* et une sauvegarde de son `SE3`, cela permettra très rapidement de (re)-mettre en production son `SE3`, que ce soit sur la même machine ou sur une autre machine.


## Le fichier `preseed`

Il faut commencer par (re)-créer son fichier `preseed` en utilisant [l'interface ad doc](http://dimaker.tice.ac-caen.fr/dise3xp/se3conf-xp.php?dist=wheezy).

Les explications sont dans la [documentation création du fichier `preseed`](http://wwdeb.crdp.ac-caen.fr/mediase3/index.php/Installation_sous_Debian_Wheezy_en_mode_automatique)


Il faut ensuite télécharger les fichiers **se3.preseed** et **setup_se3.data** ainsi créés en remplaçant les xxxx par le nombre qui convient :
```sh
wget http://dimaker.tice.ac-caen.fr/dise3wheezy/xxxx/se3.preseed
wget http://dimaker.tice.ac-caen.fr/dise3wheezy/xxxx/setup_se3.data
```

Il faut effectuer des modifications du `preseed` pour une automatisation complète :
```sh
nano ./se3.preseed
```

Ensuite,…

```sh
#MODIFIE, pour éviter un problème de fichier corrompu avec netcfg.sh
#mais cela poses peut être des problèmes par la suite car pas de réseau juste dans l'installateur
#d-i preseed/run string netcfg.sh
#AJOUTE, pour indiquer le miroir et eventuellement le proxy pour atteindre le miroir
#Mirror settings
#If you select ftp, the mirror/country string does not need to be set.
#d-i mirror/protocol string ftp

d-i mirror/country string manual
d-i mirror/http/hostname string ftp.fr.debian.org
d-i mirror/http/directory string /debian
d-i mirror/http/proxy string
#AJOUTE, pour evite de répondre à la question
#Some versions of the installer can report back on what software you have
#installed, and what software you use. The default is not to report back,
#but sending reports helps the project determine what software is most
#popular and include it on CDs.
popularity-contest popularity-contest/participate boolean false
#AJOUTE, pour installer par exemple les packages des modules du se3 mais j'ai pas essayé
#Individual additional packages to install
#d-i pkgsel/include string backuppc ...
#Whether to upgrade packages after debootstrap.
#Allowed values: none, safe-upgrade, full-upgrade
#d-i pkgsel/upgrade select none
#MODIFIE Preseed commands
----------------
d-i preseed/early_command string cp /cdrom/setup_se3.data ./; \
    cp /cdrom/se3.preseed ./; \
    cp /cdrom/se3scripts/* ./; \
    chmod 755 se3-early-command.sh se3-post-base-installer.sh install_phase2.sh; \
    ./se3-early-command.sh se3-post-base-installer.sh
```

Il faut aussi télécharger les fichiers suivants qui seront aussi nécessaires
```sh
mkdir ./se3scripts
cd se3scripts
wget http://dimaker.tice.ac-caen.fr/dise3wheezy/se3scripts/se3-early-command.sh
wget http://dimaker.tice.ac-caen.fr/dise3wheezy/se3scripts/se3-post-base-installer.sh
wget http://dimaker.tice.ac-caen.fr/dise3wheezy/se3scripts/sources.se3
wget http://dimaker.tice.ac-caen.fr/dise3wheezy/se3scripts/install_phase2.sh
wget http://dimaker.tice.ac-caen.fr/dise3wheezy/se3scripts/profile
wget http://dimaker.tice.ac-caen.fr/dise3wheezy/se3scripts/bashrc
cd ..
```


## Incorporer le fichier `preseed` à l'archive d'installation

Ensuite nous allons incorporer ces fichiers dans un `cd d'installation Wheezy`. Il nous faut pour cela une archive `Debian Wheezy`.

Tout d'abord, récupérez une image d'installation de `Debian`. Une image *netinstall* devrait suffire.
```sh
wget http://cdimage.debian.org/cdimage/archive/latest-oldstable/amd64/iso-cd/debian-7.11.0-amd64-netinst.iso
```
Ensuite, on va créer deux répertoires :
* isoorig : il contiendra le contenu de l'image d'origine
* isonew : il contiendra le contenu de votre image personnalisée

On monte ensuite l'iso téléchargée dans isoorig, puis on copie son contenu dans isonew.

mkdir isoorig isonew
mount -o loop -t iso9660 debian-7.11.0-amd64-netinst.iso isoorig
rsync -a -H –exclude=TRANS.TBL isoorig/ isonew (j'ai pas tres bien compris a quoi cela sert d'exclure TRANS.TBL car il n'existe pas )
Les modifications suivantes seront à réaliser dans le dossier isonew. On va maintenant faire en sorte que l'installateur se charge automatiquement.
On donne les droits en ecriture aux 3 fichiers à modifier :
chmod 755 ./isonew/isolinux/txt.cfg
chmod 755 ./isonew/isolinux/isolinux.cfg
chmod 755 ./isonew/isolinux/prompt.cfg 
On modifie le fichier isolinux/txt.cfg ainsi :
nano ./isonew/isolinux/txt.cfg

default install
  label install
      menu label ^Install
      menu default
      kernel /install.amd/vmlinuz
      append auto=true vga=normal file=/cdrom/se3.preseed initrd=/install.amd/initrd.gz -- quiet
(Veillez à adapter install.amd/initrd.gz selon l'architecture utilisée, ici 64bit. En cas de doute, regardez ce qu'il y a dans le dossier isoorig.)
Ensuite, éditez isolinux/isolinux.cfg et isolinux/prompt.cfg et changez timeout 0 en timeout 4 par exemple et prompt par prompt 1
nano ./isonew/isolinux/isolinux.cfg
nano ./isonew/isolinux/prompt.cfg
Enfin on copie les 2 fichiers du preseed à la racine du répertoire isonew et les fichiers se3scripts :

Enfin on crée la nouvelle image ISO :
cd isonew
 md5sum find -follow -type f > md5sum.txt (ne marche pas, pb avec le lien symbolique ... il y a une boucle ... ceci dit il n'y a aucun fichier qui sont dans le md5sum.txt qui ont ete modifie donc cette etape ne sert a rien il me semble)
apt-get install genisoimage
genisoimage -o ../my_wheezy_install.iso -r -J -no-emul-boot -boot-load-size 4 -boot-info-table -b isolinux/isolinux.bin -c isolinux/boot.cat ../isonew


## Utiliser l'archive d'installation personnalisée

Je me suis arrêté là car j'ai utilisé ISO pour tester sur une VM.  L'iso démarre, ne pose aucune question mais le clavier est en qwerty et à la première connexion en root la 2eme phase ne démarre pas toute seul, il faut lancer à la main ./install_phase2.sh qui est bien présent au bon endroit, idem pour setup_se3.data

## Solution alternative


## Références

Voici quelques références que nous avons utilisé pour la rédaction de cette documentation :

* Article du site [`Debian Facile`](https://debian-facile.org) : [preseed debian](https://debian-facile.org/doc:install:preseed) qui décrit l'incorporation d'un fichier `preseed`.
* …
