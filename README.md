# Ozuro Launcher — Bêta

Launcher Windows en français, aux couleurs bleu, blanc, rouge.

## Télécharger

[Télécharger Ozuro Launcher 0.4.36-beta.1 pour Windows](https://github.com/matthieu545/ozuro-launcher-downloads/releases/download/v0.4.36-beta.1/Ozuro-Launcher-0.4.36-beta.1.exe).

**Version bêta — destinée aux tests.** Le fichier portable suffit ; aucun jeu n'est inclus dans l'EXE. La [page de cette préversion](https://github.com/matthieu545/ozuro-launcher-downloads/releases/tag/v0.4.36-beta.1) contient l'EXE, sa notice et son empreinte SHA-256. Les versions précédentes restent dans [Releases](https://github.com/matthieu545/ozuro-launcher-downloads/releases).

![Ozuro Launcher Bêta — Launcher anglais](docs/images/launcher.png)

## Utiliser

1. Fermer l'ancien launcher, puis ouvrir le nouvel EXE sur Windows 10/11 x64.
2. Sélectionner un jeu et les modes souhaités.
3. Choisir son dossier d'installation, puis démarrer le téléchargement.
4. Une fois l'installation terminée, choisir le mode et cliquer sur Jouer.

Les téléchargements dépendent de la disponibilité des sources et des joueurs qui partagent les fichiers. Les prérequis Microsoft manquants sont détectés par le launcher ; Windows peut demander une autorisation pour les installer.

## Version 0.4.36-beta.1

- Tracker intégré déplacé sur Cloudflare Workers en HTTPS, sans serveur de tracker sur le PC de partage.
- Les 11 torrents privés conservent leurs fichiers, leurs infohashes, leurs caches et leurs choix de modes.
- Les anciennes configurations intégrées connues adoptent la nouvelle annonce sans réinitialiser les réglages.
- Protections MW2/MW3, vérification SHA-256 de Drive et durcissement Electron/ASAR conservés.
- Badge BÊTA, onglet Launcher anglais, fond illustré et identité tricolore conservés ; photos des jeux désactivées.

**Utiliser cette version pour le nouveau tracker.** Les anciennes annonces HTTP ont été arrêtées. Les préférences restent dans le dossier historique `%APPDATA%\FR Launcher` et les fichiers déjà installés sont conservés.

Les fichiers des jeux s'échangent directement entre participants ; Cloudflare ne les transporte pas. Les participants voient l'adresse de sortie des clients torrent. Le tracker gratuit est soumis aux quotas Cloudflare.

**Limites restantes :** clé commune extractible, absence d'identification individuelle et EXE non signé Authenticode. Le contrôle du launcher ne certifie pas tous les EXE/DLL des jeux. Voir [les détails de sécurité](SECURITE.md).

## Vérifier le fichier

L'empreinte SHA-256 figure dans [SHA256SUMS.txt](SHA256SUMS.txt) et avec la release.

## À propos de ce dépôt

Ce dépôt public distribue uniquement l'application et sa notice. Le dépôt de développement est privé. Les jeux, sauvegardes, configuration du tracker et catalogue opérationnel ne sont pas hébergés comme fichiers sources ici. Un utilisateur disposant de l'EXE peut en extraire les sources de téléchargement.
