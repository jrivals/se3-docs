=================================================
Bonjour, ReStructuredText ! : essai d'un tutoriel
=================================================

Une introduction à https://fr.wikipedia.org/wiki/ReStructuredText

essai de documentation et d'essais de mise en formes diverses.

Une table des matières est mise en place automatiquement.

.. sectnum::
.. contents:: Table des matières


Première Partie
===============

gras, italiques et autres mises en forme
----------------------------------------
du texte **en gras** ou en *italique* ou encore ``essai mis en cadre dans la phrase``.

Pour du texte souligné ?


des touches ?
-------------

Séquence de touches ? kbd::[Alt+F11]


Symboles ? et ligne de séparation
---------------------------------

(C) (R) (TM) -- ... -> <- => <= 

On peut utiliser le signe "fin de paragraphe" ?
&#182;

Et aussi rajouter une ligne de séparation :

----

du texte ici en dessous de la ligne de séparation.


des liens
---------

* exemple

https://fr.wikipedia.org/wiki/ReStructuredText

* autre exemple

Une phrase avec un lien vers restrutured_ et `le site de SambaÉDU`_.

.. _restrutured : https://fr.wikipedia.org/wiki/ReStructuredText
.. _le site de SambaÉDU : https://www.sambaedu.org/


Références dans le document
---------------------------

On peut revenir sur un titre de la table des matières, par exemple `gras, italiques et autres mises en forme`_ ou bien `Les tableaux`_.

On peut aussi créer une ancre et ensuite s'y référer au cours du texte ; voir un lien dans le § `Les tableaux`_.

.. _monAncre:

C'est pratique, non ?


des listes
----------

liste non numérotée :

* item 1
    * sous-item
        * encore un sous-item
        * dfgt
* item 2
* item 3
    * sous-item
    * sous-item

liste numérotée automatiquement :

#. liste 1

    #. sousliste a
    
    #. sousliste b
    
#. liste 2

#. liste suivante

    #. sousliste a
    
        #. sous-sous-liste
        
    #. sousliste b
    
    #. sousliste c

liste encore avec numérotation :

a. liste n

b. liste nb


des anotations et remarques
---------------------------

.. Error:: C'est une erreur ?

.. Important::
   * c'est important
   * bien le lire
   * se concentrer
   * se reposer…

.. Note::
    - une liste
    - est possible
    - À noter genre remarque…

.. WARNING::
    * comme ci-dessus
    * on peut avoir une liste

.. Attention:: on peut faire aussi attention :-)


citation et commentaire non affiché
-----------------------------------

    Après la pluie le beau temps.

    C'est la fête à la grenouille !

avec un commentaire qui n'apparaît pas dans l'affichage, juste entre les deux lignes ci-dessous :-)

----

.. un commentaire qui n'apparaît pas à l'affichage !

----


des notes
---------

on peut avoir une note [#]_ et une autre note [#]_ qui devraient être numérotées automatiquement.

autre texte éventuel…

.. [#] c'est la 1ère note.
.. [#] c'est la 2e note. 



Deuxième Partie
===============

Inclure du code ou autre texte dans un cadre
--------------------------------------------

Voici du code ::

    # lsblk
    NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
    sda      8:0    0 698,7G  0 disk
    ├─sda1   8:1    0  19,9G  0 part /
    ├─sda2   8:2    0  19,9G  0 part
    ├─sda3   8:3    0     1K  0 part
    ├─sda5   8:5    0   5,5G  0 part [SWAP]
    └─sda6   8:6    0 653,3G  0 part /home
    sr0     11:0    1  1024M  0 rom

autre exemple ::

  int main ( int argc, char *argv[] ) {
      printf("Hello World\n");
      return 0;
  }


Inclure des images
------------------

voici une image :

.. figure:: images/essai.png
    :width: 300pt


Les labels
----------

Term 1
    Definition 1

Term 2
    Definition 2


Les tableaux
------------

Voici un tableau :

=====  =====  ======
   Inputs     Output
------------  ------
  A      B    A or B
=====  =====  ======
False  False  False
True   False  True
False  True   True
True   True   True
=====  =====  ======

On peut revenir dans le texte à l'ancre monAncre_.