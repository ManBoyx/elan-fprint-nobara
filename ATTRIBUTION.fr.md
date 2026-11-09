[English](ATTRIBUTION.md) · **Français**

# Attribution et provenance des correctifs

Ce dépôt télécharge une révision épinglée de la
[branche `elanmoc2` de Depau](https://gitlab.freedesktop.org/Depau/libfprint/-/tree/elanmoc2).
Cette branche fait partie de `libfprint` et est sous licence LGPL-2.1 ou ultérieure.

| Correctif | Origine |
| --- | --- |
| 0001 | Commande d'enregistrement sur six octets pour `04f3:0c00`, adaptée de [bassemabdelbaset](https://github.com/bassemabdelbaset/libfprint-elan-04f3-0c00-ubuntu). |
| 0002 | Suppression plus sûre et pas d'effacement implicite de tout le lecteur, d'après [jvikramsrd](https://github.com/jvikramsrd/elan-0c00). |
| 0003 | Correction de la propriété du `GError` de nouvelle tentative, par jvikramsrd. |
| 0004 | Contrôles de longueur sur la réponse d'informations de doigt, par jvikramsrd. |
| 0005 | Adaptation de compilation pour Fedora : omission de l'exemple C++ sans rapport. |
| 0006 | Terminer une action de vérification/identification réessayable après son unique rapport de résultat. |

Ce travail n'est affilié ni à ELAN, ni à HP, ni à Fedora, ni aux mainteneurs de
`libfprint`. Les gabarits d'empreintes restent sur le lecteur ; ce dépôt ne contient
ni gabarit d'empreinte ni pilote binaire Windows.
