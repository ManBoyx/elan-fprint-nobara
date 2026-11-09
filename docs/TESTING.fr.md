[English](TESTING.md) · **Français**

# Plan de test matériel

Notez la révision du firmware, les versions de Fedora et de libfprint, si le lecteur
sortait d'un démarrage à froid ou d'une reprise, et la sortie exacte de chaque test. Ne
publiez ni gabarits d'empreintes, ni mots de passe de comptes, ni journaux de débogage
non relus.

1. Confirmer `04f3:0c00`, `bcdDevice 0281` et `fprintd-list "$USER"` après un démarrage
   à froid et après un redémarrage.
2. Enregistrer un doigt sur un lecteur vide : huit étapes acceptées, validation
   réussie, puis lister le doigt.
3. Effectuer au moins 20 vérifications avec ce doigt et 10 avec un autre doigt. Noter
   les correspondances, non-correspondances, nouvelles tentatives, expirations et plantages
   du démon.
4. Répéter la vérification après une veille et une reprise, et avec l'autosuspension USB
   dans son état par défaut. L'ancien essai `power/control=on` n'a pas, à lui seul, supprimé
   les expirations.
5. Annuler une vérification en attente. Confirmer que la tentative suivante fonctionne
   toujours.
6. Supprimer uniquement le doigt de test et vérifier que les autres empreintes stockées,
   s'il y en a, subsistent. Ne jamais lancer l'effacement complet du lecteur dans des
   tests de routine.
7. Essayer la connexion GDM, le déverrouillage de l'écran et une application utilisant PAM.
   Garder la connexion par mot de passe disponible tout du long.

La version avec le correctif 0006 a été installée sur le lecteur testé. Un doigt
correspondant et un doigt différent ont donné respectivement `verify-match` et
`verify-no-match`. Une erreur de scan réessayable n'a pas encore été reproduite avec cette
version, donc la régression du résultat en double reste non vérifiée sur le matériel.
`sudo -k` suivi de `sudo -v` a réussi avec le doigt enregistré et sans mot de passe. Le
déverrouillage de l'écran GNOME après Super+L a aussi réussi par empreinte. Une nouvelle
connexion GDM avec cette version reste à tester.
Le test unitaire `fpi-ssm` de l'amont a réussi. Le test générique `fpi-device` de l'ancienne
branche source échoue sur `/driver/initial_features/no_storage` dans cette version ; les
tests du pilote `elanmoc2` sont ignorés car les liaisons d'introspection ne sont pas
installées. Ces résultats ne prouvent pas la fiabilité sur le matériel.
