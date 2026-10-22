# Sécurité et limites — 0.4.55-beta.1

Cette bêta corrige la protection des fichiers personnels, vérifie les fichiers Drive contre un manifeste de publication et durcit le paquet Electron. Elle ne certifie pas les EXE/DLL de tous les jeux ou leurs mises à jour tierces.

## Durcissement de la version 0.4.55

- Toutes les commandes IPC exigent le processus d'interface attendu, son cadre principal exact et l'URL locale du launcher. Un sous-cadre avec la même URL ne peut pas emprunter les droits de la fenêtre principale.
- Le bac à sable est activé globalement. L'intégration Node, les webviews, les sous-cadres, les workers web, les fenêtres externes, les téléchargements initiés par Chromium et les permissions de caméra, microphone, écran, périphériques et géolocalisation sont refusés. Les liens externes prévus continuent de passer par les commandes du processus principal.
- Le contrôle du partage reste exclusivement sur `127.0.0.1`, avec un secret aléatoire de 256 bits comparé en temps constant. Il exige maintenant le bon en-tête Host et refuse les requêtes Origin/Sec-Fetch-Site des navigateurs. Taille des requêtes, connexions et délais sont bornés.
- La réutilisation d'Ozuro Partage revérifie tous les fichiers du moteur contre le paquet courant, y compris les DLL et instantanés V8. Un ajout, une modification ou une absence entraîne une nouvelle copie vérifiée. Cela ne protège pas contre un logiciel malveillant ayant déjà les mêmes droits et pouvant modifier les fichiers après leur contrôle.
- Les réglages et reçus JSON utilisent un fichier temporaire unique créé exclusivement. Les écritures vers un même fichier sont sérialisées, et les chemins comportant une jonction ou un lien symbolique sont refusés. Les alias de périphériques Windows supplémentaires sont également refusés dans les noms téléchargés.
- Les nouveaux journaux filtrent les URL sensibles, les formes usuelles de jetons, les chemins de profil Windows et les adresses IP hors boucle locale. L'export applique à nouveau ce filtre aux anciens journaux et aux données structurées. Il faut toujours relire un diagnostic avant de le partager : un texte libre peut contenir d'autres informations personnelles.

Ces contrôles sont des défenses vérifiées par tests, pas une garantie d'absence de toute vulnérabilité. Le moteur torrent, l'extraction, les mises à jour et les prérequis conservent leurs contrôles d'intégrité existants. Aucun réglage antivirus, VPN, pare-feu ou routeur n'est désactivé par cette version.

Références : [recommandations Electron](https://www.electronjs.org/docs/latest/tutorial/security), [noms réservés Windows](https://learn.microsoft.com/en-us/windows/win32/fileio/naming-a-file).

## Réglages et sauvegardes

MW2/MW3 ont une politique liée à leur torrent exact : les configurations absentes sont initialisées, les configurations existantes sont conservées et les statistiques, sauvegardes et caches personnels de l’auteur sont omis. Les anciens reçus sont nettoyés avant réparation. Les chemins personnels connus, dont `players2`, restent protégés pour les sources importées, les superpositions Drive et la désinstallation.

## Références Drive

La taille et le SHA-256 de chaque fichier doivent figurer dans un manifeste livré par un éditeur de confiance. COD2 utilise 94 références embarquées dans le catalogue de publication. Le launcher vérifie les octets reçus avant remplacement ou extraction, ainsi que les caches réutilisés. Une divergence supprime la copie temporaire et conserve le fichier installé. Les anciens reçus ne constituent plus une référence d’authenticité.

Pour une source personnalisée, importer un manifeste obtenu indépendamment du stockage Drive modifiable. Un manifeste inconnu ou calculé à partir de fichiers déjà compromis ne prouve pas leur authenticité. Les anciennes sources Drive sans empreintes sont refusées avec un message explicite ; les sources torrent intégrées continuent à fonctionner.

L’éditeur peut compléter une liste Drive avec les empreintes de ses originaux :

```powershell
node tools/hash-drive-manifest.cjs liste-drive.json "C:\Publication-originale" publication-sha256.json
```

L’outil conserve la liste d’origine et crée un nouveau fichier. Pour une archive, utiliser `folderId: "archive:ID1:ID2"`, dans l’ordre des liens, et une entrée par volume, avec l’ID Drive, son chemin local, sa taille, sa date et son SHA-256. Le bouton d’importation accepte aussi les manifestes d’archives. Le script Apps Script exporte une liste ; seul l’outil local ajoute les références d’intégrité. Le secours par archives du navigateur vérifie aussi le manifeste de publication lorsqu’il existe. Sans ce manifeste, une archive choisie manuellement est une source explicitement approuvée par l’utilisateur ; son empreinte locale contrôle la copie, pas l’identité de son éditeur.

## Paquet Windows

Electron 44.9.0 est figé dans le verrou npm. Les fuses désactivent `RunAsNode`, `NODE_OPTIONS`, les arguments d’inspection Node et les privilèges supplémentaires `file://`. Les cookies sont chiffrés ; seul `app.asar` peut charger l’application et son intégrité doit correspondre aux métadonnées de l’EXE. Les protections renderer/IPC et les contrôles de chemins restent actifs.

L’override ciblé de `global-agent` 4.1.3 supprime la dépendance à la chaîne `roarr`/`sprintf-js` signalée par npm, sans rétrograder electron-builder. Cet outil n’est pas livré comme dépendance applicative.

**Authenticode reste à finaliser.** Aucun certificat de signature de code n’est configuré. Les fuses et SHA-256 ne remplacent pas une identité d’éditeur signée ; un attaquant contrôlant toute la distribution pourrait remplacer l’EXE lui-même.

Avec un certificat Authenticode légitime, fournir `CSC_LINK` et, si nécessaire, `CSC_KEY_PASSWORD` via l’environnement sécurisé de compilation, puis lancer `npm run build:signed`. La configuration impose une signature SHA-256 et échoue si les éléments de signature manquent ; elle ne crée aucun certificat autosigné. Ne jamais placer de clé privée ou de mot de passe dans Git. Vérifier la signature et l’horodatage de l’EXE final avant publication.

## Tracker HTTPS sur Cloudflare Workers

**Les torrents intégrés utilisent HTTPS et un tracker hébergé directement sur Cloudflare Workers**, avec un registre Durable Objects SQLite partagé, une expiration des pairs et des limites de requêtes. Aucun fichier des jeux ne transite par Workers. Les URL de prévisualisation et les logs d'observabilité sont désactivés ; la configuration opérationnelle est livrée au Worker comme secret Cloudflare et n'est pas versionnée.

Le Worker retient l'adresse IPv4 fournie à l'entrée par Cloudflare et ignore les indications `ip`, `ipv6` ou `X-Forwarded-For` du client. Il ne résout jamais l'ancien DNS domestique. Les clients IPv6 seuls ne sont pas pris en charge dans cette première version. Le plan Workers Free est soumis à quotas : un dépassement peut interrompre les annonces jusqu'au renouvellement du quota.

**L'accès individuel reste à finaliser.** Le catalogue distribué contient une clé commune extractible. Le mode privé réduit la découverte publique ; il ne réserve pas techniquement les fichiers aux exécutables Ozuro et ne masque pas les adresses de sortie des pairs. Le Worker accepte aussi des autorisations individuelles désactivables et limitées à certains torrents, mais le système d'identification et de délivrance de ces autorisations n'existe pas encore.

La migration modifie uniquement les annonces, en conservant les dictionnaires `info` et leurs empreintes : les morceaux, les modes, les reçus et caches restent compatibles. Les sources personnalisées ne sont pas réécrites. L'opérateur doit retirer l'ancien service, son démarrage automatique, sa redirection de port et le DNS qui pointait vers sa box. Une ancienne IP déjà diffusée et les historiques DNS ne disparaissent pas.

Le VPN doit couvrir qBittorrent et le moteur aria2 du launcher si l'opérateur souhaite masquer l'adresse domestique aux participants. Un port fermé ne protège pas la ligne contre une saturation réseau. Les tests du tracker et du launcher ne garantissent pas la capacité d'envoi entre deux utilisateurs derrière des réseaux qui bloquent les connexions entrantes.

Même une autorisation individuelle ne peut empêcher un utilisateur autorisé de recopier les fichiers qu’il reçoit.
