[English](README.md) · **Français** · [Čeština](README.cs.md)

# ELAN 04f3:0c00 sur Fedora 44 et Nobara 44

Intégration expérimentale de `libfprint` pour le lecteur USB ELAN Match-on-Chip 2
`04f3:0c00`, révision de firmware `0281` (2.81). Elle a été testée sur un HP Pavilion
15-eg0900nc sous Fedora 44. L'installateur accepte aussi Nobara 44 (basée sur Fedora).
C'est un ensemble de correctifs et un installateur pour la pile d'empreintes digitales
du système, **pas** une application d'authentification autonome.

L'installateur épingle la [branche `elanmoc2` de Davide Depau](https://gitlab.freedesktop.org/Depau/libfprint/-/tree/elanmoc2)
à la révision `11f0316d069cc90c154c8cb0e46478388c5e2a74`, applique les correctifs de
`patches/`, compile `libfprint`, et ne laisse que `fprintd` charger la bibliothèque
obtenue depuis `/opt/elan-fprint-fedora44/lib64`. La bibliothèque empaquetée par Fedora
reste en place. `rollback` retire ce contournement et conserve les gabarits stockés sur le lecteur.

## État

- Sur l'ordinateur testé, `fprintd-enroll` a franchi huit étapes et enregistré une
  empreinte de l'index droit. `fprintd-verify` a renvoyé `verify-match`.
- L'empreinte est restée enregistrée après un redémarrage, et `fprintd-list` détectait
  toujours le lecteur.
- Avec la version mise à jour, `sudo -k` suivi de `sudo -v` a authentifié avec le doigt
  enregistré, sans mot de passe. Le déverrouillage de l'écran GNOME après Super+L a
  aussi réussi avec le doigt, sans mot de passe de session.
- Certaines tentatives d'enregistrement et de vérification plus anciennes ont expiré.
  Le lecteur a aussi renvoyé quelques non-correspondances. Le correctif 0006 traite une
  assertion de résultat en double observée après des erreurs de scan réessayables. La
  version mise à jour a été installée : un doigt correspondant a renvoyé `verify-match`,
  un autre doigt a renvoyé `verify-no-match`. Le chemin d'erreur réessayable n'a pas
  encore été reproduit avec cette version, donc le correctif de l'assertion n'est pas
  vérifié sur le matériel.
- Ce n'est pas un pilote officiel de `libfprint` ni de Fedora. Les autres révisions de
  firmware sont volontairement refusées par l'installateur.
- Une connexion GDM complète avec la version mise à jour n'a pas encore été confirmée.

## Installation

Gardez la connexion par mot de passe activée comme solution de repli. Depuis ce dépôt :

```bash
bash elan-fprint.sh install
fprintd-list "$USER"
fprintd-verify -f right-index-finger
```

Lancez `fprintd-enroll -f right-index-finger` seulement si ce doigt n'est pas déjà
listé. Le lecteur conserve les gabarits après un redémarrage ou une réinstallation du
pilote. L'enregistrement peut modifier les gabarits stockés sur le lecteur, y compris
ceux créés par Windows Hello : n'enregistrez pas si vous avez encore besoin de ces
gabarits, tant que vous n'avez pas vérifié comment les deux systèmes interagissent sur
votre machine.

Le script ne demande `sudo` que pour les dépendances de compilation Fedora et pour
l'installation. Saisissez le mot de passe dans votre propre terminal, jamais dans un
ticket ou une conversation. Si une version antérieure de ce même contournement est
installée, utilisez d'abord sa commande `rollback` : l'installateur refuse volontairement
de l'écraser.

La commande accepte `build`, `status`, `debug-on`, `debug-off` et `rollback`.
`build` vérifie les sources, compile et contrôle l'ABI de `fprintd` sans installer le
pilote ; elle peut installer des dépendances de compilation manquantes. `debug-on`
active temporairement les messages du pilote dans le journal de `fprintd`. Il faut le
faire suivre de `debug-off` une fois le dépannage terminé. Les journaux de débogage
peuvent contenir des identifiants de comptes locaux : relisez-les avant de les publier.

## Connexion et trousseau GNOME

Sous Fedora, l'enregistrement est utilisé par `fprintd` et PAM. L'ordinateur testé avait
la fonctionnalité `with-fingerprint` d'`authselect` activée pour la connexion GDM.
L'installateur ne modifie pas la configuration PAM et ne retire pas l'authentification
par mot de passe.

Une empreinte authentifie l'utilisateur, mais elle ne fournit pas le mot de passe qui
chiffre le trousseau « Connexion » de GNOME. Après une connexion par empreinte, GNOME
peut demander une fois le mot de passe du trousseau, quand une application accède pour
la première fois à un secret enregistré. Mettre un mot de passe vide au trousseau évite
cette demande **au prix de stocker ses secrets sans protection par mot de passe**. Ce
dépôt ne fait pas ce changement. Sur un ordinateur portable sans chiffrement du disque,
c'est une perte de protection importante si quelqu'un d'autre lit le disque.

## En quoi cela diffère du travail existant

Ce dépôt ne prétend pas être le premier pilote pour `04f3:0c00`. Il rassemble et teste
sous Fedora 44 des travaux existants de la communauté, et ajoute un correctif pour la
gestion des nouvelles tentatives. La commande d'enregistrement sur six octets vient du
[projet Ubuntu de bassemabdelbaset](https://github.com/bassemabdelbaset/libfprint-elan-04f3-0c00-ubuntu).
Les correctifs de sûreté mémoire et de suppression s'appuient sur le
[travail de jvikramsrd](https://github.com/jvikramsrd/elan-0c00).
Le pilote d'origine de Davide Depau a été obtenu par rétro-ingénierie à partir de
captures USB de Windows. Voir [ATTRIBUTION.fr.md](ATTRIBUTION.fr.md) pour la provenance
de chaque correctif.

La bonne destination à long terme est une demande de fusion dans `libfprint` en amont,
avec des résultats matériels reproductibles : démarrages à froid, veille et reprise,
correspondance, non-correspondance, enregistrement, suppression et annulation. Voir
[docs/TESTING.fr.md](docs/TESTING.fr.md) et [docs/UPSTREAM.fr.md](docs/UPSTREAM.fr.md).

## Licence

Le pilote `libfprint` corrigé et cet ensemble de correctifs sont distribués sous
LGPL-2.1 ou ultérieure, comme les sources d'origine. Voir [LICENSE](LICENSE).
