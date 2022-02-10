<p align="right">
<img src="https://user-images.githubusercontent.com/72150651/153272845-88618bfc-e46f-4482-b249-9dc26c550ca1.png"/>
</p  
 
-----------------
# CPE LYON - 4ETI - Année 2022/2023  
## Administration Système  
# __TP4 - Gestion des paquets__  
###### Compte rendu réalisé par Mariem Ben Salah
-----------------
 
# Exercice 1 - Commandes de base
1. Afin de mettre à jour le système, on fait recours au gestionnaire de paquets __APT__ et plus spéciallement aux deux commandes: 
  * ` sudo apt update ` qui va chercher les mises à jour disponibles pour le système et les programmes installés sur les sources définis dans __/etc/apt/source.list__. Un fichier d'index est créé dans __/var/lib/apt/*_Packages__ pour lister les mises à jours disponibles. Il servira de référence pour l'installation de nouvelles mises à jour.
  *  `sudo apt upgrade` : qui va installer les mises à jour identifiées avec apt update sans supprimer les paquets installés. S'il ya de nouvelles dépendances à installer, les paquets peuvent être installés ou non selon le type de commande utilisée: apt, apt-get ou aptitude  
   
&#x1F4A1; __Notez bien__ qu'on a utilisé sudo avec les commandes apt. En effet, ça permet d'exécuter une commande en prenant l'identité du compte administrateur du système __root_.
 
2. Pour créer un alias il suffit de taper la commande `alias maj="sudo apt update && sudo apt upgrade -y"`.  
Pour que cet alias soit tenu en compte lors du prochain redémarrage, il faut le rajouter dans le fichier __~/.bashrc__ avec la commande `nano ~/.bashrc` et ensuite `source ~/.bashrc` pour forcer la relecture du fichier en question.
 
3. Pour déterminer les 5 derniers paquets installés sur la machine, il suffit de taper cette commande ` grep " installed" /var/log/dpkg.log | tail -n 5 `  
 
&#x1F4A1; __Notez bien__ qu'il faut bien mettre l'espace entre " et installed sinon il va afficher les paquets half-installed aussi.
 
4. Pour trouver les 5 derniers paquets installés avec __APT__ il suffit de faire `apt list --installed | tail -n 5`  
 
5. Trouver le nombre total de paquets installés sur la machine peut être assez controversé. En effet, quand on veut calcluler ce nombre avec dpkg y'a trois moyens de le faire : 
* Soit en utilisant `dpkg --get-selections | wc --lines` qui affiche 804
* Soit en utilisant `dpkg --list | wc --lines` qui affiche 809
* Soit en utilsiant `dpkg -l | grep ^ii | wc -l` qui affiche le __BON__ nombre car les paquets instalés commencent par __ii__
Si on veut maintenant afficher ce nombre avec apt, on utilise:
* `apt list --installed | wc --lines` qui affiche 805 
la diffèrence entre les valeurs affichées par ces commandes se voit quand on utilise la commande less avec les commandes précédentes qui montre que ya des lignes en plus qui s'affichent et qui rajoutent dans le nombre de lignes.
On ne peut pas utiliser le fichier __dpkg.log__ parce qu'il contient les paquets non installés aussi.
 
<p align="center">
 <img src="https://user-images.githubusercontent.com/72150651/153258261-568da427-d611-46a8-bbb1-43ece0da3121.png"/>
</p>
  
 ==> image capturée avec la commande `dpkg --get-selections | less`
  
<p align="center">
 <img src="https://user-images.githubusercontent.com/72150651/153258402-d1a0deca-361e-43af-9f36-b026b67a82eb.png"/>
</p>
  
 ==> image capturée avec la commande `apt list --installed | less`
  
6. Pour afficher le nombre de paquets disponibles en téléchargement sur les dépôts Ubuntu, il faut taper ` sudo apt list | wc --lines`
 
7. Installation et test des paquets suivants:
 
  * __glances__ : surveille le processeur, la charge système, la mémoire, la bande passante réseau, l'utilisation du disque ainsi que les processus
  * __tldr__ simplify the beloved man pages with practical examples.
  * __hollywood__ : fill the console with Hollywood melodrama technobabble
   
  <p align="center">
   <img src="https://user-images.githubusercontent.com/72150651/153264608-ee119a4b-37fe-40c3-89a7-7364db5a685c.png"/>
  </p>
   
8. Pour trouver tous les paquets permettant de jouer au sudoku, il suffit de taper `apt search sudoku`
 
<p align="center">
 <img src="https://user-images.githubusercontent.com/72150651/153265634-2473d6a6-f3f8-447c-8227-4207a72288fd.png"/>
</p>

  # Exercice 2
  __Objectif:__ Ecrire un script qui permet d'indiquer le paquet qui a isntallé la commande passée en argument
  ```
  which -a $1 | xargs dpkg -S 2> /dev/null | cut -f1 -d:
  ```
  __OU__
  ```
  dpkg -S $(which -a $1) 2> /dev/null | cut -f1 -d:
  ```
  __Explication:__
  * __which -a *commande*__ permet d'afficher où se trouve cette commande 
  * __xargs dpkg -S__ permet de passer la sortie de which -a en argument pour dpkg -S
  * __2> /dev/null__ permet de rediriger la sortie d'erreur dans /dev/null qui agit comme un trou noir (se nettoie tout le temps)
  * __cut -f1 -d:__ permet d'avoir la première partie avant le séparateur __:__
  
  # Exercice 3
  __Objectif:__ Ecrire une commande qui affiche *"INSTALLE"* ou *"NON INSTALLE"* selon le nom et le statut du package spécifié dans la commande
  ```
  (dpkg -l $1 2> /dev/null | grep "^ii" > /dev/null) && echo "INSTALLE" || echo "NON INSTALLE"
  ```
   __Explication:__  
   * __dpkg -l__ affiche les paquets installés
   * __grep "^ii"__ cherche les mots commançant par __ii__
   * __&& echo "INSTALLE"__ permet d'afficher __INSTALLE__ si la première condition est vérifiée
   * __|| echo "NON INSTALLE"__ permet d'afficher __NON INSTALLE__ dans le cas contraire
    
 <p align="center">
   <img src="https://user-images.githubusercontent.com/72150651/153269104-24f60004-e36e-42a0-a6a0-57f438153c19.png"/>
 </p>

  # Exercice 4
  __Objectif:__ Lister les programmes livrés avec __coreutils__
   
  * utilisation de `dpkg -L coreutils` 
  * /usr/share/man/man1/[.1.gz: La commande "[" est la commande de test. Elle permet d'écrire des conditions dont ont peut voir les résultats à l'aide des opérateur && et ||
  
  # Exercice 5 - Aptitude
  __Objectif:__ Installation des paquets emacs et lynx à l'aide de aptitude
  Pour utiliser aptitude, il faut tout d'abord l'installer avec `apt install aptitude` et ensuite il faut taper `aptitude` sur le   terminal et faire une recherche en tapant `/emacs` ou `/lynx`. Il faut ensuite appuyer sur la touche __n__ jusqu'à trouver le     bon paquet puis appuyer sur __+__ pour le préparer à l'installation et enfin appuyer sur __g__ deux fois.
  ###### emacs:
  Editeur de texte extensible,libre et personnalisable.
  ###### lynx:
  navigateur internet qui affiche du texte ASCII sur le terminal mais pas de photos ni du contenu multimédia.  
   
  Exemple: ` lynx https://cpe.com`
   
# Exercice 6 - Installation d'un paquet par PPA
1. Installation de la version Oracle de Java (avec l'ajout des PPA)

```
 sudo add-apt-repository ppa:linuxuprising/java
 sudo apt update
 sudo apt install oracle-java15-installer
```
 
2. Le nouveau dossier __/etc/apt/sources.list.d__ contient deux fichiers:
 * linuxuprising-ubuntu-java-impish.list
 * original.list
Les deux fichiers contiennet pas principalement des lignes commentées (liens vers des configurations de sécurité ...)
 
 # Exercice 7 - Installation d'un logiciel à partir du code source  
 __Objectif:__ Création de nos propres paquets et dépôts, ce qui permettra de gérer les programmes que nous écrivons comme s'ils provenaient de dépôts officiles.
  
  1. Cloner le dépôt git avec  `git clone https://gitlab.com/jallbrit/cbonsai`
  2. Suivre le readme.md
   
  # Exercice 8 - Création de dépôt personnalisé
  
  ## Création d'un paquet Debian avec dpkg-deb
  
  1. Création de sous-dossiers dans le dossier scripts avec `mkdir`  
  
  2. Création d'un fichier qui s'appelle __control__ avec `touch` dans le sous dossier __DEBIAN__ créé précédemment et le remplir avec les champs suivants:
  
  ![image](https://user-images.githubusercontent.com/72150651/153302033-ee88ec37-f1ab-4154-a79e-c67e01edeba7.png)
  
  3. Revenir dans __$HOME__ et tapez `dpkg-deb --build origine-commande` pour construire le paquet.  
  
 &#128079; __Félicitations! Vous avez crée votre propre paquet__
  
 ## Création du dépôt personnel avec reprepro
 
 1. Création d'un dosier __repo-cpe__ dans le dossier personnel
 2. Ajout de deux sous-dossiers: __conf__ (contiendra la configuration du dépôt) et __packages (contiendra les paquets)
 3. Création d'un fichier __distributions__ dans __conf__
 
 ![image](https://user-images.githubusercontent.com/72150651/153302604-8f02e742-c0ef-47e9-9393-dec9b522aea2.png)
 
 4. Génération de l'arborescence du dépôt dans le dossier __repo-cpe__ avec `reprepro -b . export`
 5.Copie du paquet __origine-commande__ dans packages, puis, `reprepro -b . includedeb cosmic origine-commande.deb` à la racine
 afin que le paquet soit inscrit dans le dépôt.
 6. Indication à __APT__ qu'il existe un nouveau dépôt dans lequel il peut trouver des logiciels. Pour cela, création (avec sudo) du fichier __repo-cpe.list__ contenat: __deb file:/home/VOTRE_NOM/repo-cpe cosmic multiverse__ dans __/etc/apt/sources.list.d__  
 
 &#x1F4A1; __Notez bien__ que cette ligne est à adapter au beosin
 
 7. Lancement de la commande `sudo apt update`
 
&#128079; __Féliciations ! Votre dépôt est désormais pris en compte ! ... Enfin, pas tout à fait... Si vous regardez la sortie d’apt update, il est précidé que le dépôt ne peut être pris en compte car il n’est pas signé. La signature permet de vérifier qu’un paquet provient bien du bon dépôt. On doit donc signer notre dépôt.__
  
  ## Signature du dépôt avec GPG
  
  1. Création d'une nouvelle paire de clés avec `gpg --gen-key`
  
  &#x274C; __N'OUBLIEZ VOTRE PASSPHRASE!!!__
  
  2. Ajout de `SignWith: yes` au fichier __distributions__
  
  3. Ajout de la clé au dépôt avec `reprepro --ask-passphrase -b . export`
  
  &#x274C;  __Attention ! Cette méthode n’est pas sécurisée et obsolète ; dans un contexte professionnel, on utiliserait plutot `gpg-agent`__
  
  4. Ajout de la clé publique au dépôt avec `gpg --export -a "auteur" > public.key`
  
  5. Ajout de cette clé à la liste des clés fiables connues de __APT__ avec `sudo apt-key add public.key`
  
  &#x1F44F; __Félicitations ! La configuration est (enfin) terminée ! Vérifiez que vous pouvez installer votre paquet comme n'importe quel autre paquet__
  
  
 
 

  
  
