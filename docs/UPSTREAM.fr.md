[English](UPSTREAM.md) · **Français**

# Chemin vers la prise en charge en amont

`04f3:0c00` manque dans la liste des appareils pris en charge par `libfprint` en amont.
Ce dépôt Fedora est une intégration matérielle de la branche `elanmoc2`, pas une nouvelle
implémentation indépendante du protocole.

Pour une soumission à `libfprint` en amont :

1. Rebaser le pilote `elanmoc2` sur le `libfprint` actuel et séparer la prise en charge du
   protocole, les correctifs de sûreté et la gestion des nouvelles tentatives en commits
   faciles à relire.
2. Fournir des preuves matérielles pour `04f3:0c00` révision `0281` : interrogation du
   firmware, enregistrement en huit étapes, validation, correspondance, non-correspondance,
   annulation, suppression, démarrage à froid et veille/reprise. Utiliser la matrice de
   `TESTING.fr.md`.
3. Ajouter un test de non-régression pour le chemin de nouvelle tentative.
   `fpi_device_verify_report()` n'accepte qu'un seul rapport par action. L'ancien pilote
   signalait une erreur réessayable, continuait à scanner dans la même action, puis
   signalait de nouveau, ce qui produisait des assertions `result_reported == FALSE`. Le
   correctif 0006 termine l'action après son rapport de nouvelle tentative ; une nouvelle
   action est nécessaire pour un autre scan.
4. Étudier séparément les vraies expirations USB et les expirations sans doigt, à l'aide
   de journaux de commande, de point d'accès et de durée. Si les traces Linux ne sont pas
   concluantes, les comparer aux captures du pilote Windows officiel. Ne pas collecter ni
   publier de gabarits d'empreintes ni d'autres données personnelles.
5. Lancer la suite de tests de l'amont et analyser ses résultats par rapport à une base
   propre. La branche épinglée actuelle a un échec générique
   `/driver/initial_features/no_storage` dans les versions ancienne et mise à jour sous
   Fedora 44 ; le test `elanmoc2` est ignoré dans cette version car les liaisons
   d'introspection sont absentes.

L'installation testée utilise une bibliothèque isolée dans `/opt` et une surcharge systemd.
Une fois la prise en charge intégrée en amont et livrée par Fedora, retirez cette surcharge
locale avec `bash elan-fprint.sh rollback` avant d'utiliser le paquet Fedora.
